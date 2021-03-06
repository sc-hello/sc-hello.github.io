@[TOC](目录)
# 并行与并发
1. 并行：在同一时刻，有多个线程在多个处理器上同时执行；
2. 并发：在同一时刻，只能有一个线程执行，但多个线程被快速轮转，使得在宏观上具有多线程同时执行的效果；

# Java线程的7种状态

1. 新建状态(New)：通过实现Runnable接口或继承Thread类，new一个线程实例后，线程就进入了新建状态；
2. 就绪状态(Ready)：线程对象创建成功后，调用该线程的start()函数，线程进入就绪状态，该状态的线程等待获取CPU时间片；运行状态下时间片用完或调用了yield()函数，线程回到就绪状态；
3. 运行状态(Running)：线程获取到了CPU时间片，正在运行自己的代码；
4. 等待状态(Waiting)：1 运行状态的线程执行wait()、t.join()、LockSupport.park()等，将进入等待状态，其中wait()和t.join()会令JVM把该线程放入锁等待队列，LockSupport.park()不会释放当前线程占有的锁资源；2 等待状态的线程不会被分配CPU时间片，等待被主动唤醒，否则一直处于等待状态；2 唤醒：通过notify()、notifyAll()、调用join的线程t执行完毕，会唤醒锁等待队列中的线程，出队的线程回到就绪状态；另一个线程调用执行LockSupport.unpark(t)唤醒指定线程，该线程回到就绪状态；
5. 超时等待状态(Timed Waiting)：1 与等待状态的区别是：超时等待状态的线程到达指定时间后会自动唤醒；2 以下函数会进入超时等待状态：wait(long)、t.join(long)、sleep(long)、LockSupport.parkUtil(long)，其中wait(long)、t.join(long)会令JVM把线程放入锁等待队列；3 唤醒：超时时间到了，或通过notify()、notifyAll()、调用join的线程t执行完毕，会唤醒锁等待队列中的线程，出队的线程回到就绪状态；非锁等待队列中的线程等超时了就回到就绪状态；
6. 阻塞状态(Blocked)：运行状态的线程获取同步锁失败或发出IO请求，进入阻塞状态，如果是获取同步锁失败则JVM将该线程放入锁同步队列;
7. 终止状态(Terminated)：线程执行结束或执行过程中因异常意外终止就进入终止状态，线程一旦终止就不能复生，这是不可逆的过程；

## 一张图总结Java线程状态
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210705150218127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

# Java线程创建的4种方式
1. 继承Thread类：1.1 定义一个Thread类的子类，并重写run()方法，该run()方法的方法体即线程执行体；1.2 创建Thread子类的实例，即创建线程对象；1.3 调用线程对象的start()方法来启动该线程；
2. 实现Runnable接口： 2.1 定义一个Runnable接口的实现类，重写接口的run()方法，该run()方法的方法体即线程执行体；2.2 创建Runnable实现类的对象；2.3 使用Runnable实现类的对象作为Thread对象的target，该Thread对象即线程对象；2.4 调用线程对象的start()方法来启动线程；
3. 通过Callable和FutureTask创建线程：3.1创建Callable接口的实现类，并重写call()方法，该call()方法即线程执行体，并且有返回值；3.2创建Callable实现类的实例，使用FutureTask对象来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值；3.3使用FutureTask对象作为Thread对象的target创建并启动新线程；3.4调用FutureTask对象的get()方法来获得线程执行结束后的返回值；
4. 基于线程池：4.1 如果并发的线程数量很多，并且每个线程都是执行一个时间很短的任务就结束了，这样频繁创建和销毁线程会大大降低系统效率；4.2 我们可以通过线程池来实现线程的复用，线程池：一个容纳多个线程的容器，其中的线程可以反复使用，无需反复创建销毁线程而消耗过多资源；4.4 ExecutorService pool = Executors.newFixedThreadPool(10);pool.execute(new Runnable()....)；

# start()和run()的区别
1. 调用Thread类的start()方法来启动线程，真正实现了多线程，这时此线程处于可运行状态，并没有真正运行，一旦得到cpu时间片，就开始运行并执行run()方法，而此时无需等待run()方法执行完毕，即可继续执行主线程下面的代码；
2. run()方法只是Thread类的一个普通方法，如果直接调用run()方法，程序中依然只有主线程这一个线程，程序执行路径还是只有一条，要等待run()方法执行完毕后才可继续执行下面的代码；

# 实现Runnable接口比继承Thread类的优势
1. 可以避免Java单继承的局限性;
2. 代码可被多个线程共享，代码和线程独立;
3. 线程池只能放入实现Runnable或Callable接口的对象，不能放入继承Thread类的对象;

# 为什么使用线程池
1. **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗；
2. **提高响应速度**。当任务到达时，不需要等到线程创建就能立即执行；
3. **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统⼀的分配、调优和监控；

# 执行execute()方法和submit()方法的区别？
1. execute() 用于提交没有返回值的任务；
2. submit() 用于提交有返回值的任务。线程池会返回⼀个 Future 类型的对象，可以通过 Future.get() 来获取返回值；

# Runnable和Callable的区别
1. Callable 的实现方法是 call()，Runnable 的实现方法是 run()；
2. Callable 任务执行后有返回值。Callable 与 Future 配合使用，运行 Callable 任务和获取一个 Future 对象表示异步计算后的结果；Runnable 任务执行后是没有返回值的；
3. Callable 的 call() 方法可以抛出异常，Runnable 的 run() 方法不能抛出异常；
# 如何创建线程池

## 方法1：通过Executor框架的工具类Executors来实现
1. newFixedThreadPool（固定大小的线程池）：如果任务数量大于等于线程池中线程的数量，则新提交的任务将在阻塞队列中排队，直到有可用的线程资源；
2. newSingleThreadExecutor（单个线程的线程池）：1 确保池中永远有且只有一个可用的线程；2 在该线程停止或发生异常时，会创建一个新线程代替该线程继续执行任务；
3. newCachedThreadPool（可缓存的线程池）：1 提交新任务时如果有可重用的线程，则重用它们，否则创建一个新线程并将其添加到线程池中；2 线程池的keepAliveTime默认60秒，超过60秒未被利用线程会被终止并从缓存中移除，因此在没有线程任务运行时，newCachedThreadPool不会占用系统资源；3 在有执行时间很短的大量任务需要执行的情况下，newCachedThreadPool能很好地复用线程资源来提高运行效率；
4. newScheduledThreadPool（可做任务调度的线程池）：可定时调度，可设置在给定延迟时间后执行或定期执行某个线程任务；
5. newWorkStealingPool（足够大小的线程池)：1 用于创建持有足够数量线程的线程池来达到快速运算的目的；2 JDK根据当前的运行需求向操作系统申请足够多的线程；

**例如**

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

## 方式2：通过ThreadPoolExecutor构造方法创建
《阿里巴巴Java开发手册》中强制线程池不允许使用 Executors 去创建，而是通过ThreadPoolExecutor 的方式，这样的处理方式一是规避资源耗尽的风险，二是可以更加明确线程池的运行规则。

Executors 返回线程池对象的弊端如下：1. newFixedThreadPool 和 newSingleThreadExecutor：允许请求的队列长度为 Integer.MAX_VALUE，可能堆积大量的请求，从而导致OOM(OutOfMemoryError)；2. newCachedThreadPool 和 newScheduledThreadPool ： 允许创建的线程数量为 Integer.MAX_VALUE，可能会创建大量线程，从而导致OOM；![在这里插入图片描述](https://img-blog.csdnimg.cn/202107061021518.png)
以上是ThreadPoolExecutor类提供的4个构造方法，下面分析最长的那个，其他三个采用默认参数：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
	if (corePoolSize < 0 ||
	    maximumPoolSize <= 0 ||
	    maximumPoolSize < corePoolSize ||
	    keepAliveTime < 0)
	    throw new IllegalArgumentException();
	if (workQueue == null || threadFactory == null || handler == null)
	    throw new NullPointerException();
	this.corePoolSize = corePoolSize;
	this.maximumPoolSize = maximumPoolSize;
	this.keepAliveTime = unit.toNanos(keepAliveTime);
	this.workQueue = workQueue;
	this.threadFactory = threadFactory;
	this.handler = handler;
}
```

**ThreadPoolExecutor 构造函数参数分析：**

1. corePoolSize：核心线程数；
2. maximumPoolSize : 线程池中能拥有最大线程数；
3. workQueue : 用于缓存任务的阻塞队列；
4. keepAliveTime :当线程池中的线程数量大于 corePoolSize 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime 才会被回收销毁；
5. unit : keepAliveTime 参数的时间单位；
6. threadFactory :executor创建新线程的时候会用到；
7. handler :饱和策略；

**corePoolSize、maximumPoolSize、workQueue 三者关系**

1. 如果没有空闲的线程执行该任务且当前运行的线程数少于 corePoolSize ，则添加新的线程执行该任务；
2. 如果没有空闲的线程执行该任务且当前的线程数等于 corePoolSize ，同时阻塞队列未满，则将任务入队列，而不添加新的线程；
3. 如果没有空闲的线程执行该任务且阻塞队列已满同时池中的线程数小于maximumPoolSize ，则创建新的线程执行任务；
4. 如果没有空闲的线程执行该任务且阻塞队列已满同时池中的线程数等于maximumPoolSize ，则根据构造函数中的 handler 指定的策略来拒绝新的任务。

**ThreadPoolExecutor 饱和策略**

如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时， ThreadPoolTaskExecutor 定义了⼀些饱和策略：

1. ThreadPoolExecutor.AbortPolicy ：抛出 RejectedExecutionException 来拒绝新任务的处理，默认的处理策略；
2. ThreadPoolExecutor.CallerRunsPolicy：用调用者所在的线程来执行任务。即被拒绝的任务在主线程中运行，别的任务只能在被拒绝的任务执行完之后才会继续被提交到线程池执行。
3. DiscardPolicy：直接丢弃任务；
4. DiscardOldestPolicy：丢弃阻塞队列中靠最前的任务；

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/92b1a6b181ddc90c80e3424575e57195.png)

**案例**

```java
/**
* 定义⼀个简单的Runnable类，需要⼤约5秒钟来执⾏其任务。
*/
public class MyRunnable implements Runnable {
    private String command;
    public MyRunnable(String s) {
        this.command = s;
    }
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " Start.Time = " + new Date());
        processCommand();
        System.out.println(Thread.currentThread().getName() + " End.Time = " + new Date());
    }
    private void processCommand() {
        try {
            Thread.sleep(5000);
            System.out.println("执行command" + command);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    @Override
    public String toString() {
        return this.command;
    }
}
```

```java
public class ThreadPoolExecutorDemo {
    private static final int CORE_POOL_SIZE = 5;
    private static final int MAX_POOL_SIZE = 10;
    private static final int QUEUE_CAPACITY = 100;
    private static final Long KEEP_ALIVE_TIME = 1L;
    public static void main(String[] args) {
        //使⽤阿⾥巴巴推荐的创建线程池的⽅式
        //通过ThreadPoolExecutor构造函数⾃定义参数创建
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
                CORE_POOL_SIZE,
                MAX_POOL_SIZE,
                KEEP_ALIVE_TIME,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(QUEUE_CAPACITY),
                new ThreadPoolExecutor.CallerRunsPolicy());
        for (int i = 0; i < 10; i++) {
            //创建WorkerThread对象（WorkerThread类实现了Runnable 接⼝）
            Runnable worker = new MyRunnable("" + i);
            //执⾏Runnable
            executor.execute(worker);
        }
        //终⽌线程池
        executor.shutdown();
        while (!executor.isTerminated()) {
        }
        System.out.println("Finished all threads");
    }
}
```
可以看到上⾯的代码指定了：

1. corePoolSize : 核心线程数为 5；
2. maximumPoolSize ：最大线程数 10；
3. keepAliveTime : 等待时间为 1L；
4. unit : 等待时间的单位为 TimeUnit.SECONDS；
5. workQueue ：任务队列为 ArrayBlockingQueue ，并且容量为 100；
6. handler :饱和策略为 CallerRunsPolicy；
```java
pool-1-thread-4 Start.Time = Thu Jul 22 10:03:26 CST 2021
pool-1-thread-3 Start.Time = Thu Jul 22 10:03:26 CST 2021
pool-1-thread-2 Start.Time = Thu Jul 22 10:03:26 CST 2021
pool-1-thread-1 Start.Time = Thu Jul 22 10:03:26 CST 2021
pool-1-thread-5 Start.Time = Thu Jul 22 10:03:26 CST 2021
执行command3
执行command2
执行command1
执行command4
执行command0
pool-1-thread-5 End.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-1 End.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-2 End.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-3 End.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-2 Start.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-4 End.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-3 Start.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-5 Start.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-1 Start.Time = Thu Jul 22 10:03:31 CST 2021
pool-1-thread-4 Start.Time = Thu Jul 22 10:03:31 CST 2021
执行command7
执行command8
执行command6
执行command5
执行command9
pool-1-thread-1 End.Time = Thu Jul 22 10:03:36 CST 2021
pool-1-thread-2 End.Time = Thu Jul 22 10:03:36 CST 2021
pool-1-thread-3 End.Time = Thu Jul 22 10:03:36 CST 2021
pool-1-thread-5 End.Time = Thu Jul 22 10:03:36 CST 2021
pool-1-thread-4 End.Time = Thu Jul 22 10:03:36 CST 2021
Finished all threads
```

# JUC包下的Atomic类
Java的Atomic原子类都放在`java.util.concurrent.atomic`包下

## 常见的几种Atomic类
1. 基本类型：使用原子的方式操作基本类型
	1. AtomicInteger：整形原子类
	2. AtomicLong：长整型原子类
	3. AtomicBoolean：布尔型原子类

2. 数组类型：使用原子的方式操作数组
	1. AtomicIntegerArray：整形数组原子类
	2. AtomicLongArray：长整型数组原子类
	3. AtomicReferenceArray：引用类型数组原子类

3. 引用类型：使用原子的方式操作引用变量
	1. AtomicReference：引用类型原子类；
	2. AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用CAS进行原子更新时可能出现的ABA问题（由于 CAS 设计机制就是获取某两个时刻变量值(初始预期值和当前值)，并进行比较更新，所以说如果在获取初始预期值和当前内存值这段时间间隔内，变量值由 A 变为 B 再变为 A，那么对于 CAS 来说是不可感知的，但实际上变量已经发生了变化（即ABA问题）。解决办法是加版本号，每次更新对版本号 +1，这样当发生 ABA 问题时通过版本号可以得知变量被改动过；

## AtomicInteger类的使用
 - public final int get()：获取当前的值
 - public final int getAndSet(int newValue)：获取当前的值，并设置新的值
 - public final int getAndIncrement()：获取当前的值，并自增
 - public final int getAndDecrement()：获取当前的值，并自减
 - public final int getAndAdd(int delta)：获取当前的值，并加上预期的值
 - boolean compareAndSet(int expect, int update)：如果输入的数值等于预期值，则以原子方式将该值设置为输入值（update）
 - public final void lazySet(int newValue)：最终设置为newValue，使用lazySet，设置之后可能导致其他线程在之后的一小段时间内还是可以读到旧的值。

## AtomicInteger类的原理
1. AtomicInteger 类主要利用 CAS + volatile来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升；
2. CAS在数据更新的时候判断一下在此期间是否有其他线程修改了这个数据，如果修改了则不更新，而是返回当前数据，再重新执行一次任务+CAS这个过程；
3. volatile保证了变量的可见性，使任何时刻任何线程总能拿到该变量的最新值；

# CAS和AQS
## CAS
1. CAS：在数据更新的时候判断一下在此期间是否有其他线程修改了这个数据，如果修改了则不更新，而是返回当前数据，再重新执行一次任务+CAS这个过程；
2. 由于 CAS 设计机制就是获取某两个时刻变量值(初始预期值和当前值)，并进行比较更新，所以说如果在获取初始预期值和当前内存值这段时间间隔内，变量值由 A 变为 B 再变为 A，那么对于 CAS 来说是不可感知的，但实际上变量已经发生了变化（即ABA问题）。解决办法是加版本号，每次更新对版本号 +1，这样当发生 ABA 问题时通过版本号可以得知变量被改动过；

## AQS
1. AQS（Abstract Queued Synchronized，抽象的队列同步器），定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它，如常用的ReentrantLock、ReentrantReadWriteLock、Semaphore、CountDownLatch等；
2. AQS维护了一个volatile int state（代表共享资源）和一个CLH队列（FIFO，多线程争用资源被阻塞时会进入此队列）；
3. AQS核心思想是：如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态；如果被请求的共享资源被占用，那么就需要⼀套线程阻塞等待以及被唤醒时锁分配的机制，这个机制是用CLH队列锁实现的，即将暂时获取不到锁的线程加入到队列中；

> CLH(Craig,Landin,and Hagersten)队列是⼀个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS是将每条请求共享资源的线程封装成⼀个CLH锁队列的⼀个结点（Node）来实现锁的分配。

![在这里插入图片描述](https://img-blog.csdnimg.cn/bf185356bbe8434dbfd20a9119c2c991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

4. AQS使用state来表示同步状态并使用CAS对state进行更新，通过内置的FIFO队列来完成获取资源线程的排队工作；
5. AQS定义两种资源共享方式：1 Exclusive（独占，只有一个线程能执行，如ReentrantLock。又可分为公平锁和非公平锁：公平锁：按照线程在队列中的排队顺序，先到者先拿到锁；非公平锁：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的）；2 Share（共享，多个线程可同时执行，如Semaphore、CountDownLatch）；3 ReentrantReadWriteLock 可以看成是组合式；

```java
private volatile int state;//共享变量，使⽤volatile修饰保证可⻅性

//返回同步状态的当前值
protected final int getState() { 
	return state;
}
// 设置同步状态的值
protected final void setState(int newState) {
	state = newState;
}
//原⼦地（CAS操作）将同步状态值设置为给定值update（如果当前同步状态的值等于期望值）
protected final boolean compareAndSetState(int expect, int update) {
	return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```
7. 自定义同步器时需要重写下面几个AQS提供的模板方法：

```java
protected boolean tryAcquire(int arg)：独占式获取同步状态，试着获取，成功返回true，反之为false

protected boolean tryRelease(int arg)：独占式释放同步状态，等待中的其他线程此时将有机会获取到同步状态

protected int tryAcquireShared(int arg)：共享式获取同步状态，返回值大于等于0，代表获取成功；反之获取失败

protected boolean tryReleaseShared(int arg)：共享式释放同步状态，成功为true，失败为false
```

- 以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调⽤tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。释放锁之前A线程自己可以重复获取此锁（state会累加），即可重入。但要注意，获取多少次就要释放多少次，这样才能保证state 能回到零态；

# synchronized
1. synchronized用于为方法、代码块提供线程安全；
2. synchronized修饰方法、代码块时，同一时刻只能有一个线程执行该方法体或代码块；

## synchronized的三种加锁场景
1. 作用于实例方法：使用当前对象(this)的锁执行互斥处理，如果对象的一个synchronized方法被某个线程执行，其他线程无法访问该对象的任何synchronized方法(但是可以访问其他非synchronized方法);
2. 作用于静态方法：使用类对象的锁执行互斥处理，其他线程无法访问该类任何静态synchronized方法(但是可以访问其他非静态的synchronized方法);
3. 使用synchronized创建同步代码块：synchronized(对象){}，表示使用某对象的锁，只将块中的代码同步，块之外的代码可以被其他线程同时访问;

## synchronized的底层原理
0. synchronized底层原理属于JVM层面；
1. synchronized 同步代码块的情况：synchronized同步代码块使用monitorenter和monitorexit指令，其中monitorenter指令指向同步代码块的开始位置，monitorexit 指令指向同步代码块的结束位置。 当执行monitorenter 指令时，线程试图获取锁也就是获取监视器锁monitor(monitor对象存在于每个Java对象的对象头中，synchronized 便是通过这种方式获取锁的，也是为什么Java中任意对象可以作为锁的原因)。每个对象维护着一个记录着被锁次数的计数器。未被锁定的对象的该计数器为0，当一个线程获得锁（执行monitorenter）后，该计数器自增变为 1 ，当同一个线程再次获得该对象的锁的时候，计数器再次自增；当同一个线程释放锁（执行monitorexit指令）的时候，计数器再自减；当计数器为0的时候，锁将被释放，其他线程便可以获得锁；
2. synchronized 同步方法的情况：同步方法的常量池中有一个ACC_SYNCHRONIZED标志。当某个线程要访问某个方法的时候，会检查是否有ACC_SYNCHRONIZED，如果有，则需要先获得监视器锁monitor，然后开始执行方法，方法执行之后再释放监视器锁；当一个线程获得锁后，该对象的计数器自增变为 1 ，当同一个线程再次获得该对象的锁的时候，计数器再次自增；当同一个线程释放锁的时候，计数器再自减；当计数器为0的时候，锁将被释放，其他线程便可以获得锁；
3. 无论是monitorenter、monitorexit还是ACC_SYNCHRONIZED都是基于monitor实现的；

## 单例模式的双重校验锁方式（考察volatile和synchronized）

```java
public class Singleton {  
    private volatile static Singleton instance;  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    	//先判断对象是否已经实例化过，没有实例化过才进⼊加锁代码
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

但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致⼀个线程获得还没有初始化的实例。例如，线程 T1 执行了 1 和 3，此时 T2 调用 getSingleton() 后发现 instance 不为空，因此返回instance，但此时 instance 还未被初始化。

使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
## JDK1.6之后的synchronized做了哪些优化
1. 为了减少获取锁和释放锁带来的消耗，引入了4种锁的状态：无锁、偏向锁、轻量级锁和重量级锁，会随着多线程的竞争情况逐渐升级，但不能降级；
2. 同时还引进了自旋锁、锁消除、锁粗化等技术来减少锁操作的开销；
## synchronized和ReentrantLock
1. **两者都是可重入锁**：可重入锁也叫递归锁，是指一个线程在外层方法获取了锁，进入内层方法会自动获取锁（比如一个递归函数里有加锁操作，递归过程中这个锁不会阻塞当前线程）；
2. **synchronized依赖于JVM而ReentrantLock依赖于 API**：synchronized是依赖于JVM实现的，JDK1.6为synchronized 关键字做了很多优化，但是这些优化都是在JVM层面实现的；ReentrantLock是在JDK层面实现的，需要 lock() 和unlock() 方法配合try/finally语句块来完成；
3. **ReentrantLock比synchronized增加了一些高级功能**：1 等待可中断：正在等待的线程可以选择放弃等待，改为处理其他事情；2 可实现公平锁：可以指定是公平锁还是非公平锁，而synchronized只能是非公平锁；3 可实现选择性通知：synchronized关键字与wait()和notify()/notifyAll()方法相结合可以实现等待唤醒机制，ReentrantLock的等待唤醒机制需要借助于Condition接口，Condition具有很好的灵活性，可以实现多路通知功能。即在⼀个ReentrantLock对象中可以创建多个Condition实例，线程对象可以注册在指定Condition实例中，通过该Condition实例的signalAll()方法只会唤醒注册在该Condition实例中的所有等待线程，从而可以有选择性地进行线程通知，在线程调度上更加灵活 ；而synchronized关键字就相当于整个Lock对象中只有⼀个Condition实例，所有的线程都注册在它⼀个身上，如果执行notifyAll()方法的话就会唤醒所有处于锁等待状态的线程，这样会造成很大的效率问题；
4. 性能已不是选择标准；

# volatile
1. 在Java的内存模型中，线程把变量保存到本地内存而不是直接在主存中进行读写，这就可能造成⼀个线程在主存中修改了⼀个变量的值，而另外⼀个线程还继续使用它在本地内存中的变量值的拷贝，造成数据的不⼀致；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210705192221755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

2. 把变量声明为volatile就指示 JVM，这个变量是不稳定的，被volatile修饰的变量在被修改后需立即同步到主内存，变量在每次使用之前都需从主内存刷新。因此可以使用volatile来保证多线程操作时变量的可见性；
3. volatile另外一个作用就是可以防止指令重排，保证了有序性，详情见“单例模式的双重校验锁方式”；
4. volatile不能保证被修饰的变量在同一时间只有一个线程访问，因此不能保证原子性；

# volatile与synchronized
## 1 与并发编程的三个重要特性的关系
1. **原子性**指一个操作是不可中断的，要么一次全部执行，要不全部不执行。被synchronized修饰的代码在同一时间只能被一个线程访问，在锁未释放之前，无法被其他线程访问到，因此synchronized可以保证原子性；volatile不能保证被修饰的变量在同一时间只有一个线程访问，因此不能保证原子性；
2. **可见性**指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能立即看到修改的值。synchronized中有一条规则：对一个变量解锁之前，必须先把此变量同步回主存中，因此被synchronized关键字锁住的对象，其值具有可见性；被volatile修饰的变量在被修改后可以立即同步到主内存，变量在每次使用之前都从主内存刷新，因此volatile也可以保证可见性；
3. **有序性**指程序执行的顺序按照代码的先后顺序执行。synchronized无法禁止指令重排，但是Java程序遵循as-if-serial语义：不管怎么重排序（编译器和处理器为了提高并行度），单线程程序的执行结果都不能被改变。由于被synchronized修饰的代码同一时间只能被同一线程访问，也就是单线程执行的，因此可以保证有序性；volatile禁止指令重排，这就保证了程序会严格按照代码的先后顺序执行，也就保证了有序性。

## 2 性能
1. 虽然synchronized做了很多优化，如偏向锁、轻量级锁、重量级锁、锁消除、锁粗化等，但它毕竟还是一种锁。无论是使用同步方法还是同步代码块，在同步操作之前都需要进行加锁，同步操作之后都需要进行解锁，这个加锁、解锁的过程是有性能损耗的；synchronized实现的锁本质上是一种阻塞锁，也就是说多个线程要排队访问同一个共享对象；
2. volatile是JVM提供的一种轻量级同步机制，它是基于内存屏障实现的，不会有阻塞锁带来的性能损耗问题；
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
instance采用volatile 关键字修饰是很有必要的，instance = new Singleton()这段代码其实是分为三步执行：

1. JVM为对象分配内存空间M；
2. 在M上为对象初始化；
3. 将M的地址赋值给instance；

但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致⼀个线程获得还没有初始化的实例。例如，线程 T1 执行了 1 和 3，此时 T2 调用getSingleton() 后发现 instance不为空，因此返回instance，但此时 instance还未被初始化。


## 4 比较
1. volatile 是 JVM 轻量级的同步机制，所以性能比 synchronized 要好；
2. volatile 修饰变量，synchronized 修饰代码块或者方法；
3. 多线程访问 volatile 不会出现阻塞，synchronized 会出现阻塞；
4. volatile 不能保证原子性，synchroinzed 能保证原子性；
# ReentrantLock
1. ReentrantLock是一个可重入的独占锁，通过AQS（Abstract Queued Synchronized，抽象的队列同步器）来实现锁的获取与释放；
2. ReentrantLock支持公平锁和非公平锁的实现，通过在构造函数ReentrantLock(boolean fair)中传递不同的参数来定义不同类型的锁，默认非公平锁；

# sleep()和wait()的区别
1. 原理不同：sleep()是Thread类的静态方法，是线程用来控制自身流程的，它会使此线程暂停执行一段时间，把执行机会让给其他线程；wait()是Object类的方法，用于线程间通信，会使当前持有该对象锁的线程等待，直到其他线程调用notify()或notifyAll();
2. 对锁的处理机制不同：调用sleep()不释放锁，调用wait()释放锁;
3. 使用区域不同：sleep()可以放在任何地方，wait()必须在同步方法或同步代码块内;
4. 是否传参：sleep()必须传参，参数就是休眠时间；wait()可传参也可不传参，传参就是等待参数时间后苏醒，不传参无限等待;

# sleep()和yield()的区别
1. sleep()：调用sleep()会让当前线程从Running进入Timed Waiting状态；

2. yield()：
	2.1 调用yield()会让当前线程从Running进入Runnable状态，然后调度执行其它线程；
	2.2 具体实现依赖于操作系统的任务调度器；

# t.join()和t.join(long n)
1. t.join()：当前线程进入锁等待队列，进入Waiting状态，等待线程t运行结束；
2. t.join(long n)：当前线程进入锁等待队列，进入Timed Waiting状态，等待线程t运行结束，且最多等待n毫秒；

# 守护线程
1. 默认情况下，Java进程需要等待所有线程都运行结束，才会结束；
2. 有一种特殊的线程称作守护线程，只要其它非守护线程运行结束了，即使守护线程的代码没有执行完，也会强制结束；
3. 垃圾回收器线程就是一种守护线程；

# 活锁与死锁
1. 活锁：当一个线程响应另一个线程的行为而运行，并且另一个线程也响应这一个线程的行为而运行时，就可能发生活锁，活锁线程无法继续进行，但线程没有被阻塞，只是忙于互相响应；
2. 死锁：在有多个线程同时被阻塞时，它们之间若互相等待对方释放锁资源，就会出现死锁。
# Java中的锁
Java中的锁主要用于保障多线程场景下数据的一致性。

## 乐观锁与悲观锁
1. 乐观锁以乐观的思想处理数据，操作数据时不会上锁，在更新的时候判断一下在此期间是否有其他线程修改了这个数据，如果修改了则不更新，而是返回当前数据，再重新执行一次任务再继续这个过程；
2. Java中的乐观锁通过版本号机制和CAS（Compare And Swap，比较和交换）算法来实现，在Java中Java.util.concurrent.atomic包下的原子类就是使用乐观锁实现的；
3. 悲观锁采用悲观思想处理数据，每次操作数据时都会上锁，其他线程想操作这个数据但拿不到锁只能阻塞；
4. Java中的悲观锁大部分基于AQS（Abstract Queued Synchronized，抽象的队列同步器）实现，在Java中synchronized和ReentrantLock等都是典型的悲观锁，还有一些使用了synchronized关键字的容器类如HashTable也是悲观锁的应用；
5. 乐观锁适用于读多写少（冲突比较小）的场景，因为不用获取锁和释放锁，省去了锁的开销，提升了吞吐量；
6. 悲观锁适用于读少写多（冲突比较严重）的场景，如果使用乐观锁会导致线程不断进行重试，反而降低了性能，使用悲观锁比较合适；

## 独占锁和共享锁
1. 独占锁：1 每次只允许一个线程持有该锁；2 如果一个线程对数据加上了独占锁，那么其他线程不能再对该数据加任何类型的锁；3 获得独占锁的线程既能读数据又能写数据；4 Java中的synchronized和java.util.concurrent(JUC)包中Lock的实现类都是独占锁；
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
1. 自旋锁认为：如果持有锁的线程能在很短的时间内释放锁资源，那么那些等待竞争锁的线程就不需要做内核态和用户态之间的切换进入阻塞状态，只需等一等（即自旋），在持有锁的线程释放锁后即可立即获取锁，这样就避免了线程在内核态和用户态之间的切换上导致的锁时间消耗；
2. 线程在自旋时会占用CPU，长时间自旋获取不到锁就会造成CPU的浪费，因此自旋锁不适合锁占用时间比较长的并发情况；
3. 适合占用锁的时间非常短或锁竞争不激烈的代码块，对性能会有大幅度提升；
4. 在JDK1.6又引入了自适应自旋，这个就比较智能了，自旋时间不再固定，由前一次在同一个锁上的自旋时间以及锁的拥有者的状态来决定。如果JVM认为这次自旋也很有可能再次成功那就会自旋较多的时间，如果自旋很少成功，那以后可能就直接省略掉自旋过程，避免浪费CPU资源；

## 分段锁
1. 分段锁并非实际的锁，用于将数据分段并在每个分段上都单独加锁，把锁进一步细粒度化，以提高并发效率；
2. ConcurrentHashMap在内部就是使用分段锁实现的；

## 锁升级：无锁-偏向锁-轻量级锁-重量级锁
1. JDK1.6为了提升性能减少获取锁和释放锁带来的消耗，引入了4种锁的状态：无锁、偏向锁、轻量级锁和重量级锁，会随着多线程的竞争情况逐渐升级，但不能降级；
2. 无锁：即乐观锁：不锁住资源，多个线程中只有一个能修改资源成功，其它线程会重试；
3. 偏向锁：用于在某个线程获取锁后，消除这个线程锁重入的开销，看起来好像这个线程得到了该锁的偏向；偏向锁的实现是通过控制对象`Mark Word`的标志位来实现的，如果当前是可偏向状态，需要进一步判断对象头存储的线程ID是否与当前线程ID一致，如果一致则直接进入；
4. 轻量级锁：当线程竞争变得比较激烈时，偏向锁会升级为轻量级锁，即线程通过自旋等待其他线程释放锁；
5. 重量级锁：如果线程竞争进一步加剧，比如线程的自旋超过一定次数，或者一个线程持有锁、一个线程自旋、又来第三个线程访问时，轻量级锁就会升级为重量级锁，重量级锁会使除了此时拥有锁的线程以外的线程都阻塞，重量级锁其实就是互斥锁；Java中的synchronized关键字内部实现原理就是锁升级的过程：无锁-偏向锁-轻量级锁-重量级锁；

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

# ThreadLocal
ThreadLocal提供了线程本地变量，当创建了一个ThreadLocal变量，那么访问这个变量的每个线程都会有这个变量的一个本地副本。当多个线程操作这个变量时，实际操作的是自己本地内存里面的变量，从而避免了线程安全问题。

![在这里插入图片描述](https://img-blog.csdnimg.cn/fddcbdfc46fb404691cecde5957a5dc7.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)
1. Thread类中有一个ThreadLocalMap类型的变量threadLocals，ThreadLocalMap是一个定制化的HashMap。在默认情况下，每个线程中的这个变量为null，只有当前线程第一次调用ThreadLocal的set或者get方法时才会创建它；
2. 其实每个线程的本地变量不是存放在ThreadLocal实例里面，而是存放在调用线程的threadLocals变量里面。也就是说，ThreadLocal类型的本地变量存放在具体的线程内存空间中；
3. ThreadLocal就是一个工具壳，它通过set方法把value值放入调用线程的threadLocals里面并存放起来，其 Key 是 ThreadLocal 对象，Value是 Entry 对象。当调用线程调用它的get方法时，再从当前线程的threadLocals变量里面将其拿出来使用。Thread里面的threadLocals之所以被设计为map结构，是为了每个线程可以关联多个ThreadLocal变量；

![在这里插入图片描述](https://img-blog.csdnimg.cn/38189a8bb8de484a87ad6f851c044679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)
## ThreadLocal的key和value分别是是哪种引用？为什么这么设计？
1. key是弱引用，value是强引用；
2. 如果key是弱引用：当引用ThreadLocal的对象被回收，由于ThreadLocalMap持有ThreadLocal的弱引用，ThreadLocal在下一次垃圾回收时会被回收；value在下一次ThreadLocalMap调用set/get/remove的时候会被清除（清除key为null的记录）；
3. 如果key是强引用：当引用ThreadLocal的对象被回收，由于ThreadLocalMap持有ThreadLocal的强引用，如果ThreadLocalMap没有手动remove，ThreadLocal不会被回收，导致内存泄漏；
## ThreadLocal的会出现什么问题？
当引用ThreadLocal的对象被回收，由于ThreadLocalMap持有ThreadLocal的弱引用，ThreadLocal在下一次垃圾回收时会被回收。然而value是强引用，不会被垃圾回收器回收，导致内存泄漏。
