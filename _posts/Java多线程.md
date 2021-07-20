@[TOC](目录)

# 并发与并行+1
1. 并发指多个程序在同一时间段运行，并行指多个程序在同一时刻运行；
2. 单CPU系统中运行多个程序，给人感觉是同时运行，其实微观上是分时的交替运行，即并发；在多 CPU系统中，这些并发程序可以分配到多个CPU上，实现并行；

# Java线程的7种状态+1

1. 新建状态(New)：通过实现Runnable接口或继承Thread类，new一个线程实例后，线程就进入了新建状态；
2. 就绪状态(Ready)：线程对象创建成功后，调用该线程的start()函数，线程进入就绪状态，该状态的线程等待获取CPU时间片;
3. 运行状态(Running)：线程获取到了CPU时间片，正在运行自己的代码，当时间片用完或调用了yield()函数，线程回到就绪状态；
4. 等待状态(Waiting)：1 运行状态的线程执行wait()、join()、LockSupport.park()，将进入等待状态，其中wait()和join()会令JVM把该线程放入锁等待队列，LockSupport.park()和Thread.sleep()类似，都是阻塞当前线程的执行，且都不会释放当前线程占有的锁资源；2 等待状态的线程不会被分配CPU时间片，等待被主动唤醒，否则一直处于等待状态；2 唤醒：通过notify()、notifyAll()、join线程执行完毕，会唤醒锁等待队列中的线程，出队的线程回到就绪状态；另一个线程调用执行LockSupport.unpark(t)唤醒指定线程，该线程回到就绪状态；
5. 超时等待状态(Timed Waiting)：1 与等待状态的区别是：超时等待状态的线程到达指定时间后会自动唤醒；2 以下函数会进入超时等待状态：wait(long)、join(long)、sleep(long)、LockSupport.parkNanos(long)、LockSupport.parkUtil(long)，其中wait(long)、join(long)函数会令JVM把线程放入锁等待队列；3 唤醒：超时时间到了，或通过notify()、notifyAll()、join线程执行完毕，会唤醒锁等待队列中的线程，出队的线程回到就绪状态；非锁等待队列中的线程等超时了回到就绪状态；
6. 阻塞状态(Blocked)：运行状态的线程获取同步锁失败或发出IO请求，进入阻塞状态，如果是获取同步锁失败则JVM将该线程放入锁的同步队列;
7. 终止状态(Terminated)：线程执行结束或执行过程中因异常意外终止，线程一旦终止就不能复生，这是不可逆的过程；

## 一张图总结Java线程状态
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210705150218127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

# Java线程创建的4种方式+1
1. 继承Thread类：1.1 定义一个Thread类的子类，并重写run()方法，该run()方法的方法体即线程执行体；1.2 创建Thread子类的实例，即创建线程对象；1.3 调用线程对象的start()方法来启动该线程；
2. 实现Runnable接口： 2.1 定义一个Runnable接口的实现类，重写接口的run()方法，该run()方法的方法体即线程执行体；2.2 创建Runnable实现类的对象；2.3 使用Runnable实现类的对象作为Thread对象的target，该Thread对象即线程对象；2.4 调用线程对象的start()方法来启动线程；
3. 通过Callable和FutureTask创建线程：3.1创建Callable接口的实现类，并重写call()方法，该call()方法即线程执行体，并且有返回值；3.2创建Callable实现类的实例，使用FutureTask对象来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值；3.3使用FutureTask对象作为Thread对象的target创建并启动新线程；3.4调用FutureTask对象的get()方法来获得线程执行结束后的返回值；
4. 基于线程池：4.1. 如果并发的线程数量很多，并且每个线程都是执行一个时间很短的任务就结束了，这样频繁创建和销毁线程会大大降低系统效率；4.2. 我们可以通过线程池来实现线程的复用，线程池：一个容纳多个线程的容器，其中的线程可以反复使用，无需反复创建销毁线程而消耗过多资源；4.4 ExecutorService pool = Executors.newFixedThreadPool(10);pool.execute(new Runnable()....)；

# start()和run()的区别+1
1. 调用Thread类的start()方法来启动线程，真正实现了多线程，这时此线程处于可运行状态，并没有真正运行，一旦得到cpu时间片，就开始运行并执行run()方法，而此时无需等待run()方法执行完毕，即可继续执行主线程下面的代码；
2. run()方法只是Thread类的一个普通方法，如果直接调用run()方法，程序中依然只有主线程这一个线程，程序执行路径还是只有一条，要等待run()方法执行完毕后才可继续执行下面的代码；

# 实现Runnable接口比继承Thread类的优势+1
1. 可以避免Java单继承的局限性;
2. 代码可被多个线程共享，代码和线程独立;
3. 线程池只能放入实现Runnable或Callable接口的对象，不能放入继承Thread类的对象;

# 为什么使用线程池+1
1. **降低资源消耗**。通过重复利⽤已创建的线程降低线程创建和销毁造成的消耗；
2. **提⾼响应速度**。当任务到达时，不需要等到线程创建就能⽴即执⾏；
3. **提⾼线程的可管理性**。线程是稀缺资源，如果⽆限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使⽤线程池可以进⾏统⼀的分配、调优和监控；

# 执行execute()方法和submit()方法的区别？+1
1. execute() ⽅法⽤于提交没有返回值的任务；
2. submit() ⽅法⽤于提交有返回值的任务。线程池会返回⼀个 Future 类型的对象，可以通过 Future.get() ⽅法来获取返回值， get() ⽅法会阻塞当前线程直到任务完成，⽽使⽤ get(long timeout，TimeUnit unit) ⽅法则会阻塞当前线程⼀段时间后⽴即返回，这时候有可能任务没有执⾏完；

# 5种常用线程池（通过Executor框架的工具类Executors来实现）+1
0. ExecutorService接口有多个实现类可用于创建不同的线程池；
1. newFixedThreadPool（固定大小的线程池）：如果任务数量大于等于线程池中线程的数量，则新提交的任务将在阻塞队列中排队，直到有可用的线程资源；
2. newSingleThreadPool（单个线程的线程池）：4.1 确保池中永远有且只有一个可用的线程；4.2 在该线程停止或发生异常时，会创建一个新线程代替该线程继续执行任务；
3. newCachedThreadPool（可缓存的线程池）：1.1 提交新任务时如果有可重用的线程，则重用它们，否则创建一个新线程并将其添加到线程池中；1.2线程池的keepAliveTime默认60秒，超过60秒未被利用线程会被终止并从缓存中移除，因此在没有线程任务运行时，newCachedThreadPool不会占用系统资源；1.3在有执行时间很短的大量任务需要执行的情况下，newCachedThreadPool能很好地复用线程资源来提高运行效率；
4. newScheduledThreadPool（可做任务调度的线程池）：可定时调度，可设置在给定延迟时间后执行或定期执行某个线程任务；
5. newWorkStealingPool（足够大小的线程池)：5.1 用于创建持有足够数量线程的线程池来达到快速运算的目的；5.2 JDK根据当前的运行需求向操作系统申请足够多的线程；

# 如何创建线程池（尚未完成）+1
《阿⾥巴巴Java开发⼿册》中强制线程池不允许使⽤ Executors 去创建，⽽是通过ThreadPoolExecutor 的⽅式，这样的处理⽅式一是规避资源耗尽的⻛险，二是可以更加明确线程池的运⾏规则。

Executors 返回线程池对象的弊端如下：

1. newFixedThreadPool 和 newSingleThreadExecutor ： 允许请求的队列⻓度为 Integer.MAX_VALUE，可能堆积⼤量的请求，从⽽导致OOM(OutOfMemoryError)；
2. newCachedThreadPool 和 newScheduledThreadPool ： 允许创建的线程数量为 Integer.MAX_VALUE，可能会创建⼤量线程，从⽽导致OOM；

## 方式一：通过ThreadPoolExecutor构造方法创建
![在这里插入图片描述](https://img-blog.csdnimg.cn/202107061021518.png)
例如

```java
ExecutorService executor = new ThreadPoolExecutor(10, 10, 60L, TimeUnit.SECONDS, new ArrayBlockingQueue(10));
```

## 方法二：通过Executor框架的工具类Executors来实现
例如

```java
ExecutorService executorService = Executors.newSingleThreadExecutor();
```

# synchronized+1
1. synchronized用于为方法、代码块提供线程安全；
2. synchronized修饰方法、代码块时，同一时刻只能有一个线程执行该方法体或代码块；

## synchronized的三种加锁场景
1. 作用于实例方法：使用当前对象(this)的锁执行互斥处理，如果对象的一个synchronized方法被某个线程执行，其他线程无法访问该对象的任何synchronized方法(但是可以访问其他非synchronized方法);
2. 作用于静态方法：使用类对象的锁执行互斥处理，其他线程无法访问该类任何静态synchronized方法(但是可以访问其他非静态的synchronized方法);
3. 使用synchronized创建同步代码块：synchronized(对象){}，表示使用某对象的锁，只将块中的代码同步，块之外的代码可以被其他线程同时访问;

## synchronized的底层原理
0. synchronized底层原理属于JVM层⾯；
1. synchronized 同步代码块的情况：synchronized同步代码块使用monitorenter和monitorexit指令，其中 monitorenter指令指向同步代码块的开始位置，monitorexit 指令指向同步代码块的结束位置。 当执⾏monitorenter 指令时，线程试图获取锁也就是获取监视器锁monitor(monitor对象存在于每个Java对象的对象头中，synchronized 便是通过这种⽅式获取锁的，也是为什么Java中任意对象可以作为锁的原因)。每个对象维护着一个记录着被锁次数的计数器。未被锁定的对象的该计数器为0，当一个线程获得锁（执行monitorenter）后，该计数器自增变为 1 ，当同一个线程再次获得该对象的锁的时候，计数器再次自增；当同一个线程释放锁（执行monitorexit指令）的时候，计数器再自减；当计数器为0的时候，锁将被释放，其他线程便可以获得锁；
2. synchronized 同步方法的情况：同步方法的常量池中有一个ACC_SYNCHRONIZED标志。当某个线程要访问某个方法的时候，会检查是否有ACC_SYNCHRONIZED，如果有，则需要先获得监视器锁monitor，然后开始执行方法，方法执行之后再释放监视器锁；当一个线程获得锁后，该对象的计数器自增变为 1 ，当同一个线程再次获得该对象的锁的时候，计数器再次自增；当同一个线程释放锁的时候，计数器再自减；当计数器为0的时候，锁将被释放，其他线程便可以获得锁；
3. 无论是monitorenter、monitorexit还是ACC_SYNCHRONIZED都是基于Monitor实现的；

## 单例模式的双重校验锁方式（考察volatile和synchronized）

```java
public class Singleton {  
    private volatile static Singleton instance;  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    	//先判断对象是否已经实例过，没有实例化过才进⼊加锁代码
	    if (instance == null) {  
	    	//类对象加锁
	        synchronized (Singleton.class) {  
		        if (instance == null) {  
		            instance = new Singleton();  
		        }  
	        }  
	    }  
    	return instance;  
    }  
}
```
instance采⽤ volatile 关键字修饰是很有必要的，instance = new Singleton()这段代码其实是分为三步执⾏：

1. JVM为对象分配内存空间M；
2. 在M上为对象初始化；
3. 将M的地址赋值给instance；

但是由于 JVM 具有指令重排的特性，执⾏顺序有可能变成 1->3->2。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致⼀个线程获得还没有初始化的实例。例如，线程 T1 执⾏了 1 和 3，此时 T2 调⽤ getSingleton() 后发现 instance 不为空，因此返回instance，但此时 instance 还未被初始化。

使⽤ volatile 可以禁⽌ JVM 的指令重排，保证在多线程环境下也能正常运⾏。
## JDK1.6之后的synchronized做了哪些优化
1. 为了减少获取锁和释放锁带来的消耗，引入了4种锁的状态：无锁、偏向锁、轻量级锁和重量级锁，会随着多线程的竞争情况逐渐升级，但不能降级；
2. 同时还引进了自旋锁、锁消除、锁粗化等技术来减少锁操作的开销；
## synchronized和ReentrantLock
1. **两者都是可重入锁**：可重入锁也叫递归锁，是指一个线程在外层方法获取了锁，进入内层方法会自动获取锁（比如一个递归函数里有加锁操作，递归过程中这个锁不会阻塞当前线程）；
2. **synchronized依赖于JVM而ReentrantLock依赖于 API**：synchronized是依赖于JVM实现的，JDK1.6为synchronized 关键字进行了很多优化，但是这些优化都是在虚拟机层⾯实现的；ReentrantLock是JDK层⾯实现的（也就是 API 层⾯，需要 lock() 和 unlock() 方法配合try/finally 语句块来完成）；
3. **ReentrantLock比synchronized增加了一些高级功能**：1 等待可中断：正在等待的线程可以选择放弃等待，改为处理其他事情；2 可实现公平锁：可以指定是公平锁还是⾮公平锁，而synchronized只能是非公平锁，公平锁就是指先等待的线程先获得锁；3 可实现选择性通知：synchronized关键字与wait()和notify()/notifyAll()⽅法相结合可以实现等待/通知机制，ReentrantLock类的等待唤醒机制需要借助Condition接口，Condition具有很好的灵活性，比如可以实现多路通知功能也就是在⼀个Lock对象中可以创建多个Condition实例（即对象监视器），线程对象可以注册在指定的Condition中，从⽽可以有选择性的进⾏线程通知，在调度线程上更加灵活。 在使⽤notify()/notifyAll()⽅法进⾏通知时，被通知的线程是由 JVM 选择的，⽤ReentrantLock类结合Condition实例可以实现“选择性通知” ；synchronized关键字就相当于整个Lock对象中只有⼀个Condition实例，所有的线程都注册在它⼀个身上。如果执⾏notifyAll()⽅法的话就会通知所有处于等待状态的线程这样会造成很⼤的效率问题，⽽Condition实例的signalAll()⽅法 只会唤醒注册在该Condition实例中的所有等待线程；
4. 性能已不是选择标准；

# volatile+1
1. 在Java的内存模型中，线程可以把变量保存到本地内存（比如机器的寄存器）中，而不是直接在主存中进⾏读写，这就可能造成⼀个线程在主存中修改了⼀个变量的值，而另外⼀个线程还继续使⽤它在本地内存中的变量值的拷贝，造成数据的不⼀致；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210705192221755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

2. 把变量声明为volatile就指示 JVM，这个变量是不稳定的，被volatile修饰的变量在被修改后需立即同步到主内存，变量在每次使用之前都需从主内存刷新。因此可以使用volatile来保证多线程操作时变量的可见性；
3. volatile另外一个作用就是可以防止指令重排，保证了有序性，详情见“单例模式的双重校验锁方式”；
4. volatile不能保证被修饰的变量在同一时间只有一个线程访问，因此不能保证原子性；

# volatile与synchronized+1
## 1 与并发编程的三个重要特性的关系
1. **原子性**指一个操作是不可中断的，要么一次全部执行，要不全部不执行。被synchronized修饰的代码在同一时间只能被一个线程访问，在锁未释放之前，无法被其他线程访问到，因此synchronized可以保证原子性；volatile不能保证被修饰的变量在同一时间只有一个线程访问，因此不能保证原子性；
2. **可见性**指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能立即看到修改的值。synchronized中有一条规则：对一个变量解锁之前，必须先把此变量同步回主存中，因此被synchronized关键字锁住的对象，其值具有可见性；被volatile修饰的变量在被修改后可以立即同步到主内存，变量在每次使用之前都从主内存刷新，因此volatile也可以保证可见性；
3. **有序性**指程序执行的顺序按照代码的先后顺序执行。synchronized无法禁止指令重排，但是Java程序遵循as-if-serial语义：不管怎么重排序（编译器和处理器为了提高并行度），单线程程序的执行结果都不能被改变。由于被synchronized修饰的代码同一时间只能被同一线程访问，也就是单线程执行的，因此可以保证有序性；volatile禁止指令重排，这就保证了程序会严格按照代码的先后顺序执行，也就保证了有序性。

## 2 性能
1. 虽然synchronized做了很多优化，如偏向锁、轻量级锁、重量级锁、自旋锁、锁消除、锁粗化等，但它毕竟还是一种锁。无论是使用同步方法还是同步代码块，在同步操作之前需要进行加锁，同步操作之后需要进行解锁，这个加锁、解锁的过程是有性能损耗的；
2. synchronize实现的锁本质上是一种阻塞锁，也就是说多个线程要排队访问同一个共享对象。而volatile是Java虚拟机提供的一种轻量级同步机制，它是基于内存屏障实现的，不会有阻塞锁带来的性能损耗问题；
3. 综上，volatile的性能表现要比synchronized好；

## 3 配合
单例模式的双重校验锁方式（考察volatile和synchronized）

```java
public class Singleton {  
    private volatile static Singleton instance;  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    	//先判断对象是否已经实例过，没有实例化过才进⼊加锁代码
	    if (instance == null) {  
	    	//类对象加锁
	        synchronized (Singleton.class) {  
		        if (instance == null) {  
		            instance = new Singleton();  
		        }  
	        }  
	    }  
    	return instance;  
    }  
}
```
instance采⽤ volatile 关键字修饰是很有必要的，instance = new Singleton()这段代码其实是分为三步执⾏：

1. JVM为对象分配内存空间M；
2. 在M上为对象初始化；
3. 将M的地址赋值给instance；

但是由于 JVM 具有指令重排的特性，执⾏顺序有可能变成 1->3->2。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致⼀个线程获得还没有初始化的实例。例如，线程 T1 执⾏了 1 和 3，此时 T2 调⽤ getSingleton() 后发现 instance不为空，因此返回instance，但此时 instance还未被初始化。

# ReentrantLock+1
1. ReentrantLock是一个可重入的独占锁，通过AQS（Abstract Queued Synchronized，抽象的队列同步器）来实现锁的获取与释放；
2. ReentrantLock支持公平锁和非公平锁的实现，通过在构造函数ReentrantLock(boolean fair)中传递不同的参数来定义不同类型的锁，默认非公平锁；

# sleep()和wait()的区别+1
1. 原理不同：sleep()是Thread类的静态方法，是线程用来控制自身流程的，它会使此线程暂停执行一段时间，把执行机会让给其他线程；wait()是Object类的方法，用于线程间通信，会使当前持有该对象锁的线程等待，直到其他线程调用notify()或notifyAll();
2. 对锁的处理机制不同：调用sleep()不释放锁，调用wait()释放锁;
3. 使用区域不同：sleep()可以放在任何地方，wait()必须在同步方法或同步代码块内;
4. 是否传参：sleep()必须传参，参数就是休眠时间；wait()可传参也可不传参，传参就是等待参数时间后苏醒，不传参无限等待;

# sleep()和yield()的区别+1
1. sleep()：
	1.1 调用sleep()会让当前线程从Running进入Timed Waiting状态；
	1.2 其他线程可以使用interrupt方法打断正在睡眠的线程，这时sleep()会抛出InterruptedException；
	1.3 睡眠结束后的线程未必会立刻得到执行；
	1.4 建议用TimeUnit的sleep()代替Thread的sleep()来获得更好的可读性；

2. yield()：
	2.1 调用yield()会让当前线程从Running进入Runnable状态，然后调度执行其它线程；
	2.2 具体实现依赖于操作系统的任务调度器；

# t.join()和t.join(long n)+1
1. t.join()：等待线程t运行结束；
2. t.join(long n)：等待线程t运行结束，最多等待n毫秒；

# 守护线程+1
1. 默认情况下，Java进程需要等待所有线程都运行结束，才会结束；
2. 有一种特殊的线程称作守护线程，只要其它非守护线程运行结束了，即使守护线程的代码没有执行完，也会强制结束；
3. 垃圾回收器线程就是一种守护线程；

# 上下文切换+1
多线程编程中，线程个数往往大于CPU核心数，而一个CPU核心在任意时刻只能被一个线程使用，为了让所有线程都能得到有效执行，CPU采取为每个线程分配时间片并轮转的形式。当⼀个线程的CPU时间片⽤完时就会重新处于就绪状态，将CPU让给其他线程使⽤，这个过程就是⼀次上下⽂切换。

# 活锁与死锁+1
1. 活锁：当一个线程响应另一个线程的行为而运行，并且另一个线程也响应这一个线程的行为而运行时，就可能发生活锁，活锁线程无法继续进行，但线程没有被阻塞，只是忙于互相响应；
2. 死锁：在有多个线程同时被阻塞时，它们之间若互相等待对方释放锁资源，就会出现死锁。为了避免出现死锁，可以为锁操作添加超时时间，在线程持有锁超时后自动释放该锁；
# Java中的锁+1
Java中的锁主要用于保障多线程场景下数据的一致性。

## 乐观锁与悲观锁
1. 乐观锁以乐观的思想处理数据，操作数据时不会上锁，在更新的时候判断一下在此期间是否有其他线程修改了这个数据，如果修改了则不更新，而是返回当前数据，再重新执行一次任务再继续这个过程；
2. Java中的乐观锁可以通过版本号机制和CAS（Compare And Swap，比较和交换）算法来实现，在Java中Java.util.concurrent.atomic包下的原子类就是使用CAS乐观锁实现的；
3. 悲观锁采用悲观思想处理数据，每次操作数据时都会上锁，其他线程想操作这个数据但拿不到锁只能阻塞；
4. Java中的悲观锁大部分基于AQS（Abstract Queued Synchronized，抽象的队列同步器）实现，在Java中synchronized和ReentrantLock等都是典型的悲观锁，还有一些使用了synchronized关键字的容器类如HashTable也是悲观锁的应用；
5. 乐观锁适用于读多写少（冲突比较小）的场景，因为不用上锁和释放锁，省去了锁的开销，提升了吞吐量；
6. 悲观锁适用于读少写多（冲突比较严重）的场景，如果使用乐观锁会导致线程不断进行重试，反而降低了性能，使用悲观锁比较合适；

## 独占锁和共享锁
1. 独占锁：1 每次只允许一个线程持有该锁；2 如果一个线程对数据加上了独占锁，那么其他线程不能再对该数据加任何类型的锁；3 获得独占锁的的线程既能读数据又能写数据；4 Java中的synchronized和java.util.concurrent(JUC)包中Lock的实现类都是独占锁；
2. 共享锁：1 允许多个线程同时持有该锁；2 如果一个线程对数据加上了共享锁，那么其他线程只能对数据再加共享锁，不能加独占锁；3 获得共享锁的线程只能读数据，不能写数据；4 Java中ReentrantReadWriteLock中的读锁就是共享锁；

## 互斥锁和读写锁
1. 互斥锁是独占锁的一种实现，互斥锁每次只允许一个线程拥有，其他线程只能等待；
2. 读写锁是共享锁的一种实现（Java中的ReentrantReadWriteLock），读写锁分读锁和写锁：多个读锁不互斥，读锁与写锁互斥，写锁与写锁互斥；
4. 读写锁相比于互斥锁并发程度更高；

## 公平锁与非公平锁
1. 公平锁指不同的线程竞争锁的机制是公平的，即遵循先到先得原则；
2. 非公平锁指不同线程竞争锁的机制是不公平的，即遵循随机或就近原则分配锁的机制；
3. 公平锁需要维护一个锁线程等待队列，基于该队列进行锁的分配，因此效率比非公平锁低很多；
4. Java中的synchronized是非公平锁，ReentrantLock默认也是非公平锁；

```java
/**
* 创建一个可重入锁，true 表示公平锁，false 表示非公平锁。默认非公平锁
*/
Lock lock = new ReentrantLock(false);
```

## 可重入锁
1. 可重入锁也叫递归锁，是指一个线程在外层方法获取了锁，进入内层方法会自动获取锁（比如一个递归函数里有加锁操作，递归过程中这个锁不会阻塞当前线程）；
2. 对于ReentrantLock，从名字上就可看出是一个可重入锁，synchronized也是可重入锁；

## 自旋锁
1. 自旋锁认为：如果持有锁的线程能在很短的时间内释放锁资源，那么那些等待竞争锁的线程就不需要做内核态和用户态之间的切换进入阻塞、挂起状态，只需等一等（即自旋），在持有锁的线程释放锁后即可立即获取锁，这样就避免了线程在内核态和用户态之间的切换上导致的锁时间消耗；
2. 线程在自旋时会占用CPU，长时间自旋获取不到锁就会造成CPU的浪费，因此自旋锁不适合锁占用时间比较长的并发情况；
3. 适合占用锁的时间非常短或锁竞争不激烈的代码块，对性能会有大幅度提升；
4. 在JDK1.6又引入了自适应自旋，这个就比较智能了，自旋时间不再固定，由前一次在同一个锁上的自旋时间以及锁的拥有者的状态来决定。如果虚拟机认为这次自旋也很有可能再次成功那就会自旋较多的时间，如果自旋很少成功，那以后可能就直接省略掉自旋过程，避免浪费CPU资源；

## 分段锁
7. 分段锁并非实际的锁，用于将数据分段并在每个分段上都单独加锁，把锁进一步细粒度化，以提高并发效率；
8. ConcurrentHashMap在内部就是使用分段锁实现的；

## 锁升级：无锁-偏向锁-轻量级锁-重量级锁
1. JDK1.6为了提升性能减少获取锁和释放锁带来的消耗，引入了4种锁的状态：无锁、偏向锁、轻量级锁和重量级锁，会随着多线程的竞争情况逐渐升级，但不能降级；
2. 无锁：即乐观锁；
3. 偏向锁：用于在某个线程获取锁后，消除这个线程锁重入的开销，看起来好像这个线程得到了该锁的偏向；偏向锁的实现是通过控制对象`Mark Word`的标志位来实现的，如果当前是可偏向状态，需要进一步判断对象头存储的线程ID是否与当前线程ID一致，如果一致则直接进入；
4. 轻量级锁：当线程竞争变得比较激烈时，偏向锁会升级为轻量级锁，即线程通过自旋等待其他线程释放锁；
5. 重量级锁：如果线程竞争进一步加剧，比如线程的自旋超过一定次数，或者一个线程持有锁、一个线程自旋、又来第三个线程访问时，轻量级锁就会升级为重量级锁，重量级锁会使除了此时拥有锁的线程以外的线程都阻塞，重量级锁其实就是互斥锁了；Java中的synchronized关键字内部实现原理就是锁升级的过程：无锁-偏向锁-轻量级锁-重量级锁；

## 锁优化技术（锁粗化、锁消除）
### 锁粗化

锁粗化就是将同步块的数量减少，并将单个同步块的作用范围扩大，本质上就是将多次上锁解锁的请求合并为一次同步请求。

例：一个循环体中有一个同步代码块，每次循环都会执行加锁解锁操作。

```java
private static final Object LOCK = new Object();

for(int i = 0;i < 100; i++) {
    synchronized(LOCK){
        // do some magic things
    }
}
```

经过锁粗化后就变成下面这样了：

```java
synchronized(LOCK){
     for(int i = 0;i < 100; i++) {
        // do some magic things
    }
}
```

### 锁消除
锁消除指虚拟机编译器在运行时检测到了共享数据没有竞争的锁，从而将这些锁进行消除。

举例：

```java
public String test(String s1, String s2){
    StringBuffer stringBuffer = new StringBuffer();
    stringBuffer.append(s1);
    stringBuffer.append(s2);
    return stringBuffer.toString();
}
```
上面代码中，test方法中有三个变量s1、s2、stringBuffer，它们都是局部变量，局部变量在栈上，栈线程私有，因此就算有多个线程访问test方法也是线程安全的。

我们都知道StringBuffer是线程安全的类，append是同步方法，但test方法本来就是线程安全的，为了提升效率，虚拟机帮我们消除了同步锁，这个过程就称为锁消除。

```java
StringBuffer.class

// append 是同步方法
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

## 一张图总结Java各种锁
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210628141225953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)


