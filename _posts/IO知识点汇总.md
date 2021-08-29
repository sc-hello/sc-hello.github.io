@[TOC](目录)
# I/O模型
1. IO模型：就是选择什么样的通信模式和架构（即使用什么样的通道）进行数据的传输与接收，很大程度上决定了程序通信的性能；
2. 实际通信需求下，要根据不同的业务场景和性能需求选择不同的IO模型；

# Java中的I/O分哪几类
大致可分为4组：

1. 基于字节操作的I/O接口：InputStream和OutputStream；
2. 基于字符操作的I/O接口：Reader和Writer；
3. 基于磁盘操作的I/O接口：File;
4. 基于网络操作的I/O接口：Socket

前两组主要是传输数据的数据格式，后两组主要是传输数据的方式。

# 输入流、输出流
1. **输入流** ：把数据从其他设备上读取到内存中的流；
2. **输出流** ：把数据从内存写出到其他设备上的流；
# 字节流、字符流
## 1 字节与字符
1. bit：位，表示信息的最小单位，二进制位，取0或1；
2. byte：字节，计算机操作数据的最小单位，由8位bit组成，取值范围-128~127；
3. char：字符，用户可读写的最小单位，在Java中由16位bit组成，取值范围（0~65535）；
## 2 字节流
1. 以字节为单位读写数据的流，主要操作类是InputStream、OutputStream的子类；
2. 不用缓冲区，直接对文件本身操作；
## 3 字符流
1. 以字符为单位读写数据的流，主要操作类是Reader、Writer的子类；
2. 使用缓冲区缓冲字符，不关闭流就不会输出任何内容；

## 4 缓冲流
缓冲流，也叫高效流，是对以上4种基本的流的增强，所以也是4个流，按照数据类型分类：

* **字节缓冲流**：`BufferedInputStream`，`BufferedOutputStream` 
* **字符缓冲流**：`BufferedReader`，`BufferedWriter`

缓冲流的基本原理，是在创建流对象时，会创建一个内置的默认大小的缓冲区数组，通过缓冲区读写，减少系统IO次数，从而提高读写的效率。
## 5 互相转换
1. 整个IO包可划分为字节流和字符流，但除了这两个流之外，还存在一组字节流—字符流的转换类；
2. OutputStreamWriter：Writer的子类，将输出的字符流变为字节流，即将一个字符流输出对象变为字节流输出对象；
3. InputStreamReader：Reader的子类，将输入的字节流变为字符流，即将一个字节流的输入对象变为字符流的输入对象；

### 1 字符流转成字节流

```java
public static void main(String[] args) throws IOException {
    File f = new File("test.txt");
    // OutputStreamWriter 是字符流通向字节流的桥梁,创建了一个字符流通向字节流的对象
    OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(f),"UTF-8");
    osw.write("我是字符流转换成字节流输出的");
    osw.close();
}
```

### 2 字节流转成字符流

```java
public static void main(String[] args) throws IOException {  
     File f = new File("test.txt");
     InputStreamReader inr = new InputStreamReader(new FileInputStream(f),"UTF-8");   
     char[] buf = new char[1024];
     int len = inr.read(buf);
     System.out.println(new String(buf, 0, len));
     inr.close();
 }
```

## 5 既然有了字节流，为什么还要有字符流？
1. 我们程序中通常操作的数据都是字符形式的，而字符是由JVM对字节进行转换得到的，这个过程比较耗时，而且如果不知道编码类型的话还很容易出现乱码问题。所以Java提供了字符流类，以字符为单位读写数据，专门用于处理文本文件；
2. 图片、音视频等文件用字节流比较好，如果涉及字符的话用字符流比较好；

#  同步和异步、阻塞和非阻塞
## 同步/异步
1. **同步**：1 一个任务的完成需要依赖另一个任务时，只有等待被依赖的任务完成后，依赖的任务才能完成；2 这是一种可靠的任务序列，要成功都成功，要失败都失败，两个任务的状态可以保持一致；
2. **异步**：1 不需要等待被依赖的任务完成，只是通知被依赖的任务要完成什么工作，依赖的任务就立即继续执行了，只要完成自己的任务就算完成了；2 至于被依赖的任务最终是否真正完成，依赖它的任务无法确定，因此这是一种不可靠的任务序列；
3. 可以用打电话和发短信来很好地比喻同步和异步操作；
4. 同步与异步的处理方式对程序的可靠性影响非常大，同步能保证程序的可靠性，异步可以提升程序的性能，要根据应用场景在可靠性和性能之间保持平衡；

## 阻塞/非阻塞
阻塞与非阻塞主要是从CPU的消耗上来说的。

1. **阻塞**：CPU停下来等待一个慢的操作完成以后，才接着完成其他的工作；
2. **非阻塞**：在这个慢的操作执行时，CPU去做其他工作，等这个慢的操作完成后，CPU再接着完成后续的操作；非阻塞的方式可明显提高CPU的利用率；

## 同步阻塞I/O
```java
老张(客户端）烧水，水壶（服务器）放到炉子上，专心等待水烧开。
```
最简单，但I/O性能比较差，CPU大部分处于空闲状态。

## 同步非阻塞I/O
```java
老张(客户端）烧水，水壶（服务器）放到炉子上，然后去客厅看电视，时不时去看看水有没有烧开。
```
1. 提升I/O性能的常用手段，尤其在网络I/O是长连接同时传输数据也不是很多的情况下，提升性能非常有效；
2. 能提升I/O性能，但也会增加CPU消耗，要考虑增加的I/O性能能不能补偿CPU的消耗，也就是系统的瓶颈是在I/O上还是在CPU上；
## 异步阻塞I/O
```java
老张(客户端）烧水，使用响水壶（服务器）放到炉子上，专心等待水烧开。
```
1. 在分布式系统中经常用到，例如，在一个分布式数据库中写一条记录，通常会有一份是同步阻塞的记录，还有2-3份备份记录会写到其他机器上，这些备份记录通常都采用异步阻塞的方式写I/O；
2. 异步阻塞能提高网络I/O的效率，尤其像上面这种同时写多份相同数据的情况；
## 异步非阻塞I/O

```java
老张(客户端）烧水，使用响水壶（服务器）放到炉子上，然后去客厅看电视，等水壶响后拎壶。
```

1. 用起来比较复杂，只有在一些非常复杂的分布式情况下使用，集群之间的消息同步机制一般用这种I/O组合方式，如Cassandra的Gossip通信机制就采用异步非阻塞的方式；
2. 适合同时要传多份相同的数据到集群中不同的机器，同时数据的传输量虽然不大却非常频繁的情况，用这种网络I/O能让性能达到最高；

# Linux下的4种IO
对于一次IO访问（以read举例），数据会先被拷贝到操作系统内核的缓冲区中，然后才会从操作系统内核的缓冲区拷贝到应用程序的地址空间。所以说，当一个read操作发生时，它会经历两个阶段：

1. 数据准备 ；
2. 将数据从内核拷贝到进程中 ；

## 1 阻塞 I/O
1. 当用户进程发出IO请求之后，内核会去查看数据是否就绪，如果没有就绪就会等待数据就绪，而用户进程就会处于阻塞状态，同时交出CPU；
2. 当数据就绪之后，内核会将数据拷贝到用户进程，并返回结果给用户进程，用户进程才解除block状态；
3. 阻塞I/O在I/O执行的两个阶段都阻塞了；

![在这里插入图片描述](https://img-blog.csdnimg.cn/07c7104fe8774abc8f03100721a34978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAaGVsbG9zYzAx,size_35,color_FFFFFF,t_70,g_se,x_16)

## 2 非阻塞 I/O
1. 当用户进程发起一个IO请求后，并不需要等待，而是马上就得到了一个结果。如果结果是一个error时，它就知道数据还没有准备好，于是它可以再次发送IO请求；
2. 一旦内核中的数据准备好了，并且又再次收到了用户进程的请求，那么它马上就将数据拷贝到用户进程，然后返回；
3. 在非阻塞IO模型中，用户进程需要不断地询问内核数据是否就绪，也就说非阻塞IO不会交出CPU，而会一直占用CPU；

![在这里插入图片描述](https://img-blog.csdnimg.cn/b68e5ba2960b44d89d37f693ffe3f89c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAaGVsbG9zYzAx,size_35,color_FFFFFF,t_70,g_se,x_16)


## 3 I/O 多路复用
1. 单个进程可以同时处理多个网络连接的IO。它的基本原理就是利用select/poll/epoll不断地轮询负责的所有socket，当某个socket有数据到达了，就通知用户进程；
2. 当用户进程调用了select，那么这个进程会被阻塞（交出CPU），而同时，内核会“监视”select负责的所有socket，当任何一个socket中的数据准备好了，select就会返回。这个时候用户进程再调用read操作，将数据从kernel拷贝到用户进程；

![在这里插入图片描述](https://img-blog.csdnimg.cn/5f3df0d3540b4fb7a272e3d5b5420971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAaGVsbG9zYzAx,size_35,color_FFFFFF,t_70,g_se,x_16)
### I/O多路复用之select、poll、epoll的区别
I/O多路复用的本质是通过一种机制（系统内核缓冲I/O数据），让单个进程可以监视多个文件描述符，一旦某个描述符就绪（一般是读就绪或写就绪），能够通知程序进行相应的读写操作。

1. **底层数据结构**：select：数组；poll：链表；epoll：红黑树
2. **支持一个进程所能打开的最大连接数**：select单个进程能打开的最大连接数由FD_SETSIZE宏定义，32位机器为1024，64位机器为2048，虽然可以修改，然后重新编译内核，但性能可能受影响；poll本质上和select没有区别，但它没有最大连接数限制，因为是基于链表来存储的；epoll虽然连接数有上限，但很大，1G内存的机器可以打开10万左右的连接，2G内存的机器可以打开20万左右的连接；
3. **fd剧增后带来的I/O效率问题**：select/poll每次调用时都会对连接进行线性遍历，随着fd的增加会导致“线性下降性能问题”；epoll在内核中是根据每个fd上的callback函数来实现的，只有活跃的socket才会主动调用callback，因此使用epoll没有select/poll的线性下降的性能问题；
4. **消息传递方式**：select/poll内核需要将消息传递到用户空间，都需要内核拷贝动作；epoll通过内核和用户空间共享一块内存来实现；

||select|poll|epoll|
|:--------:|:-------------:|:--------:|:-------------:|
| 操作方式 | 遍历 | 遍历 | 回调 |
| 底层实现 | 数组 | 链表 | 红黑树 |
| I/O效率 | 每次调用都进行线性遍历，时间复杂度为O(N) | 每次调用都进行线性遍历，时间复杂度为O(N) | 事件通知方式，每当fd就绪，系统注册的回调函数就会被调用，将就绪fd放到readyList里面，时间复杂度为O(1) |
| 最大连接数 | 1024（x86)或2048（x64） | 无上限 | 无上限 |
| fd拷贝 | 每次调用select，都需要把fd集合从用户态拷贝到内核态 | 每次调用poll，都需要把fd集合从用户态拷贝到内核态 | 调用epoll_ctl时拷贝进内核并保存，之后每次epoll_wait不拷贝 |

## 4 信号驱动IO模型
在信号驱动IO模型中，当用户线程发起一个IO请求操作，会给对应的socket注册一个信号函数，然后用户线程会继续执行，当内核数据就绪时会发送一个信号给用户线程，用户线程接收到信号之后，便在信号函数中调用IO读写操作来进行实际的IO请求操作。

![在这里插入图片描述](https://img-blog.csdnimg.cn/425fb6eb0b5142e4a7ba2b6751fc9862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAaGVsbG9zYzAx,size_35,color_FFFFFF,t_70,g_se,x_16)

## 5 异步 I/O
用户进程发起I/O操作之后，立刻就可以开始去做其它的事。而另一方面，从内核的角度，当它收到一个asynchronous read之后，首先它会立刻返回，所以不会对用户进程产生任何阻塞。然后，内核会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，内核会给用户进程发送一个信号，告诉它I/O操作完成了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/55ec43b0a80542a2a73a501bc7d9cfe1.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAaGVsbG9zYzAx,size_35,color_FFFFFF,t_70,g_se,x_16)

# BIO、NIO和AIO
## 1 BIO
同步阻塞 I/O 模型。
1. 服务器实现模式为一个连接一个线程，即客户端有连接请求时服务端就启动一个线程进行处理，如果这个连接不做任何事情就会造成不必要的线程开销，可以通过线程池机制改善；
2. 可靠性差，吞吐量低，适用于连接数比较少且固定的架构，对服务器资源要求比较高；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703105932224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

## 2 NIO
同步非阻塞 I/O 模型（多路复用IO模型）。
1. 服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器Selector上，所有连接只需要一个线程即可，通过Selector.select()轮询到有IO请求时才启动一个线程进行处理，减少了资源占用；
2. 可靠性比较好，吞吐量也比较高，适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器；

> NIO可以做到用一个线程来处理多个请求。假设有1000个请求过来，根据实际情况，可能只需要分配50个线程来处理，如果是BIO则要分配1000个线程。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210703110041383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

## 3 AIO
异步非阻塞 I/O 模型。
1. 服务器实现模式为一个有效请求一个线程，客户端的IO请求都是由操作系统先完成了再通知服务器去启动线程进行处理；
2. 用户进程发起一个IO请求后立刻返回，继续执行下面的任务，等IO操作真正完成以后，用户进程会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作，因为真正的IO读写操作已经由内核完成了；
3. 可靠性最好，吞吐量也比较高，适用于连接数目多且连接比较长（重操作）的架构，比如相册服务器；

## 4 BIO和NIO的区别
NIO三大核心：Channel（通道），Buffer（缓冲区），Selector（选择器）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/dd8f884bcf2d4ce5bfbf5932f6f73514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)


**1 是否阻塞**

1. BIO 是阻塞的，NIO 是非阻塞的。
2. BIO 中一个线程调用 read() / write() 时，该线程被阻塞；
3. NIO 中一个线程从 Channel 中读取数据到 Buffer 中时，可以继续做别的操作，当数据读取到 Buffer 中后，线程再处理数据；一个线程请求写入数据到某个Channel中时，也不需要等待完全写入，该线程便可进行别的操作；

**2 缓冲区（Buffer）**

1. BIO 面向流，NIO 面向缓冲区；
2. NIO中，发送给一个通道的所有数据都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要先读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区；

**3 通道（Channel）**

1. NIO 通过通道进行读写；
2. 通道和流的不同之处在于：流只能在一个方向上移动，而通道是双向的，可以用于读、写或者同时用于读写；
3. 通道只能和缓冲区交互，因为有缓冲区，通道可以异步读写；

**4 选择器（Selector）**

1. NIO 用一个线程使用选择器 Selector，通过轮询的方式去监听多个 Channel 上的事件，从而让一个线程就可以处理多个事件；
2. 使用 Selector 的好处：使用更少的线程就可以来处理通道了，相比使用多个线程，节约了服务器资源；
# Netty
1. Netty是一个非阻塞I/O客户端-服务器框架，主要用于开发Java网络应用程序，如协议服务器和客户端；
2. 除了作为异步网络应用程序框架，Netty还包括了对HTTP、HTTP2、DNS及其他协议的支持，涵盖了在Servlet容器内运行的能力、对WebSockets的支持、与Google Protocol Buffers的集成、对SSL/TLS的支持以及对用于SPDY协议和消息压缩的支持；
3. **本质**：JBoss做的一个Jar包；**目的**：快速开发高性能、高可靠性的网络服务器和客户端程序；**优点**：提供异步的、事件驱动的网络应用程序框架和工具；

