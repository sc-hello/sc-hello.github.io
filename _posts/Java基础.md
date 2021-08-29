@[TOC](目录)
# 方法重载和方法重写的区别
1. 都是实现多态的方式；
2. 重载实现编译时的多态性，重写实现运行时的多态性；
3. 重载发生在同一个类中，方法名相同，参数列表不同，与返回值类型、权限修饰、抛出异常无关（比如其他相同返回值类型不同，不能表示重载而且报错）；
4. 重写发生在父类和子类之间，方法名、参数列表相同，方法修饰符权限大于等于父类、返回值类型小于等于父类、抛出异常小于等于父类；
# ==和equals()的区别
1. ==：分两种情况：基本数据类型==比较的是值，引用数据类型==比较的是地址;
2. equals()只针对引用类型，也分两种情况：2.1类没有重写equals()方法，比较的是地址，例如StringBuilder类、StringBuffer类；2.2类重写了equals()方法，比较的是对象内容，例如String类、基本类型包装类；
# 按值传递和按引用传递的区别
1. 基本数据类型按值传递，对形参的修改不会影响实参；
2. 引用数据类型（数组、类、接口）按地址传递，形参和实参指向同一内存地址，形参改变会导致实参改变；
3. String、Integer、Double等Immutable的类型特殊处理，可以理解为传值，操作不会修改实参；
# static关键字的作用
0. static是修饰符，用于修饰类的成员变量、成员方法，还可以编写static代码块；
1. 修饰成员变量:1.1变量被称为静态变量、类变量，属于类，被所有对象共享1.2类初次加载时分配内存并初始化，内存中只有一个副本，而非静态变量在创建对象时分配内存并初始化，属于对象，存在多个副本，各个对象拥有的副本互不影响；
2. 修饰成员方法:2.1方法被称为静态方法、类方法，属于类，被所有对象共享2.2只能访问静态变量和静态方法，但非静态方法不仅可以访问非静态变量和非静态方法，也可以访问静态变量和静态方法2.3方法中不能使用this；
3. 修饰代码块:3.1给类变量进行初始化赋值3.2只会在类被初次加载时执行一次；
# 静态变量、成员变量、局部变量的区别
1. 静态变量:static修饰，类变量，类中方法外，类初次加载时初始化，类调用，对象调用，存储在方法区，与类共存亡；
2. 成员变量:类中方法外，对象创建时初始化，对象调用，存储在堆，与对象共存亡；
3. 局部变量:方法中或作为方法形参，存储在栈，与方法共存亡；
# 成员变量与局部变量的详细区别
1. 语法形式：成员变量在类中方法外，局部变量在方法中或者作为方法形参；成员变量可被public、private等访问控制修饰符及static修饰，局部变量不能被访问控制修饰符及static修饰；但是，成员变量和局部变量都能被final修饰；
2. 存储方式：成员变量是对象的一部分，存放于堆中；局部变量存放于栈中；
3. 生命周期：成员变量与对象共存亡；局部变量与方法的调用共存亡；
4. 成员变量如果没有被赋初值，会自动赋以类型的默认值（特殊情况：被final修饰的成员变量必须显式地赋值或者在构造函数中赋值），局部变量不会自动赋值必须显式赋值；
# 如果两个对象的hashcode相同，equals()一定为true吗？
1. 不一定，如果两对象的hashcode相等，不代表两对象equals()为true，只能说明这两对象在散列存储结构中，存放在同一位置；
2. 两对象先通过hashcode比较，如果hashcode相等，再用equals()来判断两对象的内容是否相等；
3. 如果两对象equals()为true，则这两对象的hashcode一定相等；
# 为什么equals()重写的话，建议也一起重写hashcode方法？
1. 首先保证一个原则，两对象如果equals()为true，其hashcode一定相等；
2. 如果一个类重写了equals方法，但没有重写hashcode方法，那么当类的两个对象通过equals比较时相等，但两者的hashcode不一定相等，这就导致了错误；
# List、Set、Map
1. List:有序的Collection，可以包含重复元素；
2. Set:无序的Collection，不可以包含重复元素；
3. Map:可以把键（key）映射到值（value）的数据类型，key无序且不能重复；

# String,StringBuffer,StringBuilder的区别
1. 可变性:String不可变，StringBuffer和StringBuilder可变;
2. 线程安全:String不可变，因此线程安全;StringBuffer内部使用synchronized进行同步，因此线程安全;StringBuilder线程不安全；
3. 性能:Java对String的操作实际上是一个不断创建新对象并将旧对象回收的过程，性能较差；StringBuffer每次都会对StringBuffer对象本身进行操作，而不是生成新对象，性能较好；StringBuilder每次都会对StringBuilder对象本身进行操作，而不是生成新对象，比使用StringBuffer高10%-15%左右的性能，但要冒多线程不安全的风险；
4. 使用总结:操作少量数据，使用String;单线程操作大量数据，使用StringBuilder;多线程操作大量数据，使用StringBuffer；
# 抽象类和接口
1. 相同点:1.1都不能被实例化1.2接口的实现类或抽象类的子类都只有重写了接口或抽象类中的抽象方法后才能被实例化；
2. 不同点:2.1实现接口的关键字为implements，继承抽象类的关键字为extends 2.2一个类可以实现多个接口，但一个类只能继承一个抽象类，所以使用接口可以间接实现多继承2.3接口强调特定功能的实现(has-a)，而抽象类强调所属关系（is-a）2.4接口成员变量必须被public static final修饰，必须赋初值，不能被修改;抽象类中的成员变量可以是各种类型2.5接口没有构造方法;抽象类有构造方法，是供子类创建对象时，初始化父类成员使用的；

## 什么时候用抽象类，什么时候用接口
0.  分析对象提炼内部共性时形成抽象类，提供调用功能扩充功能时形成接口；
1. 抽象类和其子类是是不是的关系（is-a）。例如：程序员和项目经理都是员工；
2. 接口和其子类是有没有的关系(has-a)。例如：鸟和飞机都有飞的特性；
# Java实例化对象时的初始化顺序
1. 父类静态成员和静态初始化块，按在代码中出现的顺序依次执行；
2. 子类静态成员和静态初始化块，按在代码中出现的顺序依次执行；
3. 父类实例成员和实例初始化块，按在代码中出现的顺序依次执行；
4. 父类构造方法；
5. 子类实例成员和实例初始化块，按在代码中出现的顺序依次执行；
6. 子类构造方法；
# Java8种基本类型及其位数
1. 整型4种:byte8short16int32long64；
2. 浮点型2种:float32double64；
3. 字符型1种:char16；
4. 布尔型1种:boolean1；

# public,protected,default,private的访问权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210418154830988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)
# final/finally/finalize
1. **final**：最终，可以修饰类、变量、成员方法，被修饰类不能被继承、被修饰变量不能被重新赋值（即常量）、被修饰方法不能被重写；
2. final修饰类和方法可以避免API使用者更改基础功能；final修饰变量可以保护只读数据，在并发编程中有利于减少额外的同步开销；
3. **finally**是保证重点代码一定要被执行的一种机制，可以使用try-finally或try-catch-finally来进行类似关闭JDBC连接、保证unlock锁等操作；
4. **finalize**：当一个对象可被回收时，如果需要执行该对象的 finalize() 方法，那么就有可能在该方法中让对象重新被引用，从而实现自救；

# String对象的两种创建方式
1. **字面量**：会创建0或1个对象，如果String Pool中没有“abc”，编译期在String Pool中创建一个"abc"对象，运行期返回String Pool中该“abc"对象的引用；如果String Pool中已有一个“abc”对象，则编译期不创建新的“abc”对象，运行期直接返回String Pool中该“abc”对象的引用；
2. **new**：会创建1或2个对象，如果String Pool中没有“abc”，编译期在String Pool中创建一个"abc"对象，运行期使用new的方式在堆中创建另外一个"abc"对象，并返回该对象的引用；如果String Pool中已有一个"abc"对象，则编译期不创建新的“abc”对象，运行期使用new的方式在堆中创建另外一个"abc"对象，并返回该对象的引用；
# 浅克隆和深克隆的区别
1. 浅克隆：克隆一个新对象，新对象属性值和被克隆对象属性值完全相同，对于非基本类型属性，仍指向原有属性所指向的对象；
2. 深克隆：克隆一个新对象，属性中引用的对象也会被克隆（递归性的），不再指向原有对象；
3. 浅克隆实现：实现Cloneable接口、重写clone()方法（调用super.clone())；
4. 深克隆实现：实现Cloneable和Serializable接口、重写clone()方法（通过对象的序列化和反序列化实现深克隆)；
# new Integer(123) 与 Integer.valueOf(123)有何区别，请从底层实现分析两者区别
1. new Integer(123)每次都会创建一个新对象；Integer.valueOf(123)会使用缓存池中的对象，多次调用会取得同一个对象的引用；
2. Integer 缓存池的大小默认为 -128~127 ；
3. 编译器会在自动装箱过程调用 valueOf() 方法，因此多个Integer实例使用自动装箱来创建并且值相同，就会引用相同的对象；
# Java程序的运行过程
1. Java源文件被编译器编译成字节码文件；
2. JVM将字节码文件编译成相应操作系统的机器码；
3. 机器码调用相应操作系统的本地方法库完成具体的指令操作；
# JRE和JDK
1. JRE即Java运行环境，包括JVM和Java程序运行时所需要的类库；
2. JDK即Java开发工具包，包含JRE和开发工具，例如编译工具(javac.exe)、打包工具(jar.exe)；
3. 如果想要运行一个已有的Java程序，只需安装JRE即可，如果想要开发一个Java程序，必须安装JDK；

```java
# Java与C++的异同
 1. Java是纯粹面向对象语言，所有对象继承自java.lang.Object，C++为了兼容C既支持面向对象也支持面向过程；
 2. Java通过虚拟机实现跨平台特性，C++依赖于特定平台；
 3. Java没有指针，它的引用可以理解为安全指针，而C++具有和C一样的指针；
 4. Java支持自动垃圾回收，C++需要手动回收；
 5. Java不支持多重继承，只能通过实现多个接口来达到相同目的，C++支持多重继承；
 6. Java不支持操作符重载，虽然可以对两个String对象执行加法运算，但这是语言内置支持的操作，不属于操作符重载，C++支持操作符重载；
 7. Java的goto是保留字，但是不可用，C++可以使用goto；
 8. Java不支持条件编译，C++可以通过#ifdef、#ifndef等预处理命令实现条件编译；
```

# Throwable体系
1. **Error**：1 错误，程序无法处理的问题，比较严重；2 大多数错误与代码编写者执行的操作无关，而是代码运行时JVM出现的问题；3 例如，Java虚拟机运行错误，当JVM不再拥有继续执行操作所需的内存资源时，将出现OutOfMemoryError。4 这些错误发生时，JVM一般会选择终止线程；
2. **Exception**：1 异常，程序可以处理的问题；2 Exception类有一个重要的子类RuntimeException，由JVM抛出，例如NullPointerException（要访问的变量没有引用任何对象时，抛出该异常）、ArithmeticException（算术运算异常，一个整数除以0时，抛出该异常）和ArrayIndexOutOfBoundsException（下标越界异常）；
3. **异常和错误的区别**：异常能被程序处理，错误无法被处理；

# Throwable类常用方法
1. **public String getMessage()**：返回异常发生时的简要描述；
2. **public String toString()**：返回异常发生时的详细信息；
3. **public String getLocalizedMessage()**：返回异常对象的本地化信息；
4. **public void printStackTrace()**：在控制台上打印Throwable对象封装的异常信息；

# try/catch/finally
1. **try块**：用于捕获异常，其后可接0或多个catch块，如果没有catch块，则必须跟一个finally块；
2. **catch块**：用于处理try捕获到的异常；
3. **finally块**：无论是否捕获或处理异常，finally块里的语句都会被执行。当在try块或catch块中遇到return语句时，finally语句块将在方法返回之前被执行（如果finally语句中有return，则finally中的返回值会覆盖try块或catch块中的返回值）；

# finally代码块不会被执行的4种情况
1. finally代码块中出现了异常；
2. 在前面的代码中使用了System.exit()退出程序；
3. 程序所在线程死亡；
4. 关闭CPU；
 
# 什么是泛型
1. 可以在类、方法或接口中预支使用的未知类型；
2. 将数据类型作为参数进行传递，使其灵活地应用到类、方法或接口中；
# Classloader.loadClass(className)和Class.forName(className)的区别
1. Classloader.loadClass(className)得到的是类加载过程第一阶段“加载”后的class对象；
2. Class.forName(className)得到的是类加载过程第五阶段“初始化”后的class对象；

# 什么是反射机制
1. 指在运行状态中，对任意一个类，都能够知道这个类的所有属性和方法;对任意一个对象，都能够调用这个对象的所有属性和方法；
2. 作用：2.1 运行时检查类的属性和方法2.2 运行时检查对象的类型2.3 运行时任意调用对象的方法2.4 运行时任意构造一个类的对象；
3. 使用：3.1 java.lang.reflect包中的三个类（1Field:成员变量 2Method:成员方法 3Constructor:构造方法）3.2对public域的方法:getField、getMethod、getConstructor 3.3对所有域的方法:getDeclaredField、getDeclaredMethod、getDeclaredConstructor 3.4利用反射访问私有属性:使用setAccessible(true)；
4. 不足：性能是一个问题，反射相当于一系列解释操作，通知JVM要做什么，性能比直接的Java慢很多；

# 子类继承父类的注意事项
1. 子类构造器会默认调用super()(无论构造器中是否写有super()，除非显式调用super(参数)),用于初始化父类成员；
2. 当父类中存在有参构造时，默认的无参构造就不存在了，因此强烈建议同时显式提供无参构造，因为子类构造不会自动调用父类有参构造，如果没有调用父类有参构造，仍然默认调用父类无参构造，如果父类没有，就会出错；


```java
# Java程序的种类
1. Application：Java应用程序，可以由Java解释器直接运行的程序；
2. Applet：Java小程序，嵌入到Web页面中由Java兼容浏览器控制执行；
3. Servlet：运行于Web Server上，作为客户端请求和服务器响应的中间层；
```

# Java中的String为什么不可变？不可变的好处？
1. 不可变的原因；1.1 String被声明为final class，是典型的Immutable类；1.2 String的主要成员变量char value[]被声明为private final；
2. 不可变的好处：2.1可以缓存 hash 值：例如 String 用作 HashMap 的 key， 不可变性使得 hash 值也不可变，因此只需进行一次计算；2.2String Pool 的需要：只有 String 是不可变的，才能拥有 String Pool；2.3线程安全：String 不可变性天生具备线程安全2.4用作很多场景下的参数：例如在网络连接的过程中如果参数被改变，改变参数的那一方以为现在连接的是其它主机，而实际情况却不是，String可以保证参数不可变；

# HashMap底层原理解析
1. 内部包含一个 Entry 类型的数组 table，Entry包含四个字段，分别是键、值、键的哈希值、以及下一个结点的引用next；
2. 从next字段可以看出Entry是一个链表，即数组中的每个位置被当成一个桶，一个桶存放一个链表；
3.  通过哈希算法将键的哈希值转换成桶下标，使用拉链法来解决哈希冲突，同一链表中存放键的哈希值相同的Entry；
4. 数组table长度默认16，最大为2 ^ 30；
5. 默认装载因子loadFactor为0.75，乘以数组长度capacity得到threshold，如果键值对数量大于threshold，数组扩容为原来的2倍，扩容后需重新计算每个元素的桶下标；
6. 链表长度>8且数组容量>=64时，链表会转化为红黑树；转化的过程：先遍历链表 ，将链表的节点转化为红黑树的节点，然后将链表转化为红黑树；红黑树中元素个数<=6就退化为链表；

# HashMap和HashSet的区别
1. HashMap实现了Map接口，HashSet实现了Set接口；
2. HashMap存储键值对，HashSet存储对象；
3. HashMap调用put()添加元素，HashSet调用add()添加元素；
4. HashMap使用键计算hashcode，HashSet使用成员对象计算hashcode ；

# ConcurrentHashMap
1. ConcurrentHashMap和HashMap的实现方式类似，不同的是它采用分段锁的思想支持并发操作，因此是线程安全的；
2. 如果为了线程安全对整个HashMap加锁，同一时间只能有一个线程可以操作HashMap，效率不高；而ConcurrentHashMap在内部细分为若干个小的HashMap，即Segment，默认16个，对每个Segment单独加锁，提高了并发度；
3. 实现：3.1 ConcurrentHashMap内部包含了一个Segment数组，Segment和HashMap类似，是数组和链表结构；3.2 每个Segment包含并守护一个HashEntry数组，HashEntry是链表结构，在对HashEntry数组里的数据进行修改时，必须首先获得它对应的Segment锁；3.3 在操作ConcurrentHashMap时，如果需要在其中put一个新的数据，并不是将整个ConcurrentHashMap加锁，而是先根据hashcode查询该数据被存放在哪个Segment，然后对该Segment加锁并完成put操作；3.4 在多线程环境下，如果多个线程同时执行put操作，则只要加入的数据被存放在不同的Segment中，在线程间就可以做到并行的线程安全；
# LinkedHashMap
1. 继承自 HashMap;
2. 内部维护了一个双向链表，用来维护插入顺序或 LRU 顺序；
3. accessOrder 决定顺序模式，默认false，维护的是插入顺序; 
4. 如果 accessOrder 为 true，则为 LRU 顺序，在每次访问一个节点时，会将这个节点移到链表尾部，保证链表尾部是最近访问的节点，那么链表首部就是最近最少访问的节点；
5. afterNodeInsertion()在 put 等操作之后执行，当 removeEldestEntry() 方法返回 true 时会移除最近最少访问的节点，也就是链表首部节点first; 
6. removeEldestEntry() 默认为 false，如果需要让它为 true，需要继承 LinkedHashMap 并重写这个方法，这在实现 LRU 的缓存中特别有用，通过移除最近最少使用的节点，不仅保证了缓存空间足够，而且缓存的都是热点数据;

# WeakHashMap
1. WeakHashMap 的 Entry 继承自 WeakReference，被 WeakReference关联的对象在下一次垃圾回收时会被回收；
2. WeakHashMap 主要用来实现缓存，通过使用 WeakHashMap 来引用缓存对象，由 JVM 对这部分缓存进行回收；
# TreeMap
1. TreeMap不仅实现了Map接口，还实现了SortedMap接口，因此集合中的映射关系具有一定的顺序(默认按key的自然顺序排列)；
2. 但是在添加、删除和定位映射关系时，TreeMap比HashMap性能稍差；
3. 由于TreeMap实现的Map集合中的映射关系是根据键对象按照一定顺序排列的，因此不允许键对象是null；

# HashMap和HashTable的异同
1. HashMap允许空键空值，Hashtable不允许；
2. HashMap的方法线程不安全，Hashtable的方法线程安全，HashTable内部的方法由synchronized修饰；
3. 底层数据结构：JDK1.8以后HashMap在解决哈希冲突时有了较大优化，当链表长度>8且数组容量>=64时，链表会转化为红黑树，以减少搜索时间；HashTable没有这样的机制；

# this和super
1. this用来指向当前实例对象，它的一个重要作用就是用来区分对象的成员变量与方法的形参（当一个方法的形参与成员变量名字相同时，就会覆盖成员变量）；
2. super可以用来访问离自己最近的父类的成员变量和方法，当子类的成员变量或方法与父类有相同名字时就会覆盖父类成员变量或方法，要想访问父类成员变量或方法只能通过super关键字来访问；
3. 子类每个构造方法中均隐式调用super()，显式调用父类构造super([参数])会覆盖默认的super() ；
4. super([参数])和this([参数])均需放在构造方法内第一行，因此不能同时出现在一个构造函数里面；
5. this和super均不可以在static环境中使用;
6. 从本质上讲，this是一个指向本对象的指针, 而super是一个Java关键字；

# ArrayList与Vector
1. 都是基于数组实现的动态数组；
2. Vector线程安全，在可能涉及到线程不安全的操作上都进行了synchronized修饰，但性能不如ArrayList； ArrayList线程不安全，但性能优于Vector，单线程下建议使用；

# 限定通配符与非限定通配符
1. 限定通配符对类型进行了限制，有两种：1.1<? extends T>类型必须是T或T的子类1.2<? super T>类型必须是T或T的父类；
2. 非限定通配符：< T >是任意类型；
# PECS原则
1. Producer Extends Consumer Super；
2. 如果需要一个只读List，用来produce T，那么使用<? extends T>，只能向外提供(get)元素, 而不能作为向外获取(add)元素；
3. 如果需要一个只写List，用来consume T，那么使用<? super T> ，只能向外获取(add)元素, 而不能向外提供(get)元素；
# Comparable与Comparator
1. 1.1 Comparable对实现它的类的对象进行比较（需要implements Comparable< T >）1.2 只能在类中重写compareTo(T t)一次，不能经常修改类的代码实现自己想要的排序；1.3 实现此接口的类的对象列表（或对象数组）可以通过Collections.sort（或Arrays.sort）进行排序；
2. 2.1 Comparator对某个类的对象进行比较（不需要implements）2.2 将Comparator匿名内部类对象传递给sort方法（Collections.sort或 Arrays.sort），重写compare(T t1,T t2)，从而在排序上实现灵活精准控制；





