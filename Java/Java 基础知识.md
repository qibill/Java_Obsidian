## 一、Java 基础知识

### 1. Object 类相关方法

*   getClass 获取当前运行时对象的 Class 对象。
*   hashCode 返回对象的 hash 码。
*   clone 拷贝当前对象， 必须实现 Cloneable 接口。
    *   **浅拷贝**对基本类型进行值拷贝，对引用类型拷贝引用；
    *   **深拷贝**对基本类型进行值拷贝，对引用类型对象不但拷贝对象的引用还拷贝对象的相关属性和方法。
    *   两者不同在于深拷贝创建了一个新的对象。
*   equals 通过内存地址比较两个对象是否相等，String 类重写了这个方法使用值来比较是否相等。
*   toString 返回类名@哈希码的 16 进制。
*   notify 唤醒当前对象监视器的任一个线程。
*   notifyAll 唤醒当前对象监视器上的所有线程。
*   wait
    1.  暂停线程的执行；
    2.  三个不同参数方法（等待多少毫秒；额外等待多少毫秒；一直等待）
    3.  与 <code>Thread.sleep(long time)</code> 相比，sleep 使当前线程休眠一段时间，并没有释放该对象的锁，wait 释放了锁。
*   finalize 对象被垃圾回收器回收时执行的方法。

### 2. [[基本数据类型]]


### 3. 序列化

Java 对象实现序列化要实现 Serializable 接口。

*   反序列化并不会调用构造方法。反序列的对象是由 JVM 自己生成的对象，不通过构造方法生成。
*   序列化对象的引用类型成员变量，也必须是可序列化的，否则，会报错。
*   如果想让某个变量不被序列化，使用 transient 修饰。
*   单例类序列化，需要重写 readResolve() 方法。

### 4. String、StringBuffer、StringBuilder

*   String 由 char\[] 数组构成，使用了 final 修饰，是不可变对象，可以理解为常量，线程安全；对 String 进行改变时每次都会新生成一个 String 对象，然后把指针指向新的引用对象。
*   StringBuffer **线程安全** ；StringBuiler **线程不安全**。
*   操作少量字符数据用 String；单线程操作大量数据用 StringBuilder；多线程操作大量数据用 StringBuffer。
##### 能继承string类
	不可以，因为String类有final修饰符，而final修饰的类是不能被继承的，实现细节不允许改变。
##### Math.round(11.5) 和 Math.round(-11.5)
	Math.round(11.5)=12
    Math.round(-11.5)=-11
    Math.ceil()用作向上取整。
    Math.floor()用作向下取整。
    Math.round() 我们数学中常用到的四舍五入取整。

### 5. 重载与重写

*   重载 发生在同一个类中，方法名相同，参数的类型、个数、顺序不同，方法的返回值和修饰符可以不同。
*   重写 发生在父子类中，方法名和参数相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为 private 或者 final 则子类就不能重写该方法。

### 6. final

*   修饰基本类型变量，一经出初始化后就不能够对其进行修改。
*   修饰引用类型变量，不能够指向另一个引用。
*   修饰类或方法，不能被继承或重写。
##### final，finally，finalize的区别

  final—修饰符（关键字）如果一个类被声明为final，意味着它不能再派生出新的子类，不能作为父类被继承。将变量或方法声明为final，可以保证它们在使用中不被改变。被声明为final的变量必须在声明时给定初值，而在以后的引用中只能读取，不可修改。被声明为final的方法也同样只能使用，不能重载。 
  
  finally—再异常处理时提供 finally 块来执行任何清除操作。如果抛出一个异常，那么相匹配的 catch 子句就会执行，然后控制就会进入 finally 块（如果有的话）。 
  
  finalize—方法名。Java 技术允许使用 finalize() 方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。
### 7. 反射

*   在运行时动态的获取类的完整信息
*   增加程序的灵活性
*   JDK 动态代理使用了反射

### 8. JDK 动态代理

*   使用步骤
    *   创建接口及实现类
    *   实现代理处理器：实现 InvokationHandler ，实现 invoke（Proxy proxy，Method method，Object\[] args） 方法
    *   通过 Proxy.newProxyInstance(ClassLoaderloader, Class\[] interfaces, InvocationHandler h) 获得代理类
    *   通过代理类调用方法。

### 9.  [[IO]]

## 二、Java 集合框架

### 1. List（线性结构）

*   ArrayList Object\[] 数组实现，默认大小为 10 ，支持随机访问，连续内存空间，插入末尾时间复杂度 o(1)，插入第 i 个位置时间复杂度 o(n - i)。扩容，大小变为 1.5 倍，Arrays.copyOf（底层 System.ArrayCopy），复制到新数组，指针指向新数组。
*   Vector 类似 ArrayList，线程安全，扩容默认增长为原来的 2 倍，还可以指定增长空间长度。
*   LinkedList 基于链表实现，1.7 为双向链表，1.6 为双向循环链表，取消循环更能分清头尾。

### 2. Map（K，V 对）

*   HashMap
    *   底层数据结构
        *   JDK 1.8 是数组 + 链表 + 红黑树。链表长度大于 8 时，转化为红黑树，优化查询效率。
        *   JDK 1.7 无红黑树
    *   初始容量为 **16**，通过 tableSizeFor 保证容量为 2 的幂次方。寻址方式，高位异或，(n-1)\&h 取模，优化速度。
    *   扩容机制，当元素数量大于容量 x 负载因子 0.75 时，容量扩大为原来的 2 倍，新建一个数组，然后转移到新数组。
    *   基于 Map 实现。
    *   线程不安全。
*   HashMap (1.7) 多线程循环链表问题
    *   在多线程环境下，进行扩容时，1.7 下的 HashMap 会形成循环链表。
    *   怎么形成循环链表： 假设有一 HashMap 容量为 2 ， 在数组下标 1 位置以 A -> B 链表形式存储。有一线程对该 map 做 put 操作，由于触发扩容条件，需要进行扩容。这时另一个线程也 put 操作，同样需要扩容，并完成了扩容操作，由于复制到新数组是头部插入，所以 1 位置变为 B -> A 。这时第一个线程继续做扩容操作，首先复制 A ，然后复制 B ，再判断 B.next 是否为空时，由于第二个线程做了扩容操作，导致 B.next = A，所以在将 A 放到 B 前，A.next 又等于 B ，导致循环链表出现。
*   HashTable
    *   线程安全，方法基本全用 Synchronized 修饰。
    *   初始容量为 11 ，扩容为 2n + 1 。
    *   继承 Dictionary 类。
*   ConcurrentHashMap
    *   线程安全的 HashMap。
    *   1.7 采用分段锁的形式加锁；1.8 使用 Synchronized 和 CAS 实现同步，若数组的 Node 为空，则通过 CAS 的方式设置值，不为空则加在链表的第一个节点。获取第一个元素是否为空使用 Unsafe 类提供的 getObjectVolatile 保证可见性。
    *   对于读操作，数组由 volatile 修饰，同时数组的元素为 Node，Node 的 K 使用 final 修饰，V 使用 volatile 修饰，下一个节点也用 volatile 修饰，保证多线程的可见性。
*   LinkedHashMap LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。
*   TreeMap 有序的 Map，红黑树结构，可以自定义比较器来进行排序。
*   Collections.synchronizedMap 如何实现 Map 线程安全？ 基于 Synchronized ，实际上就是锁住了当前传入的 Map 对象。

### 3. Set（唯一值）

*   HashSet 基于 HashMap 实现，使用了 HashMap 的 K 作为元素存储，V 为 new Object() ，在 add() 方法中如果两个元素的 Hash 值相同，则通过 equals 方法比较是否相等。
*   LinkedHashSet LinkedHashSet 继承于 HashSet，并且其内部是通过 LinkedHashMap 来实现的。
*   TreeSet 红黑树实现有序唯一。

##### Collection和Collections
java.util.Collection 是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java 类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式。
java.util.Collections 是一个包装类。它包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。
Collections的构造方法是被私有化了的，以此来达到不被实例化的目的。

##### List，set，map继承Collection
List，set继承Collection
## 三、Java 多线程

### 1. [[synchronized]]

### 2. Lock

*   ReentrantLock
    *   基于 AQS （AbstractQueuedSynchronizer）实现，主要有 state (资源) + FIFO (线程等待队列) 组成。
    *   公平锁与非公平锁：区别在于在获取锁时，公平锁会判断当前队列是否有正在等待的线程，如果有则进行排队。
    *   使用 lock() 和 unLock() 方法来加锁解锁。
*   ReentrantReadWriteLock
    *   同样基于 AQS 实现，内部采用内部类的形式实现了读锁（共享锁）和写锁 （排它锁）。
*   非公平锁吞吐量高 在获取锁的阶段来分析，当某一线程要获取锁时，非公平锁可以直接尝试获取锁，而不是判断当前队列中是否有线程在等待。一定情况下可以避免线程频繁的上下文切换，这样，活跃的线程有可能获得锁，而在队列中的锁还要进行唤醒才能继续尝试获取锁，而且线程的执行顺序一般来说不影响程序的运行。

### 3. volatile

*   Java 内存模型

![image](https://img-blog.csdnimg.cn/2020090416055754.png)

*   在多线程环境下，保证变量的可见性。使用了 volatile 修饰变量后，**在变量修改后会立即同步到主存中，每次用这个变量前会从主存刷新。**
*   禁止 JVM 指令重排序。
*   单例模式双重校验锁变量为什么使用 volatile 修饰？ 禁止 JVM 指令重排序，new Object()分为三个步骤：申请内存空间，将内存空间引用赋值给变量，变量初始化。如果不禁止重排序，有可能得到一个未经初始化的变量。

### 4. 线程的五种状态

#### 1) New

一个新的线程被创建，还没开始运行。

#### 2) Runnable

一个线程准备就绪，随时可以运行的时候就进入了 Runnable 状态。

Runnable 状态可以是实际正在运行的线程，也可以是随时可以运行的线程。

多线程环境下，每个线程都会被分配一个固定长度的 CPU 计算时间，每个线程运行一会儿就会停止让其他线程运行，这样才能让每个线程公平的运行。这些等待 CPU 和正在运行的线程就处于 Runnable 状态。

#### 3) Blocked

例如一个线程在等待 I/O 资源，或者它要访问的被保护代码已经被其他线程锁住了，那么它就在阻塞 Blocked 状态，这个线程所需的资源到位后就转入 Runnable 状态。

#### 4) Waiting（无限期等待）

如果一个线程在等待其他线程的唤醒，那么它就处于 Waiting 状态。以下方法会让线程进入等待状态：

*   Object.wait()
*   Thread.join()
*   LockSupport.park()

#### 5) Timed Waiting（有期限等待）

无需等待被其他线程显示唤醒，在一定时间后有系统自动唤醒。

以下方法会让线程进入有限等待状态：

*   Thread.sleep(sleeptime)
*   Object.wait(timeout)
*   Thread.join(timeout)
*   LockSupport.parkNanos(timeout)
*   LockSupport.parkUntil(timeout)

#### 6) Terminated

一个线程正常执行完毕，或者意外失败，那么就结束了。

### 5. wait() 与 sleep()

*   调用后线程进入 waiting 状态。
*   wait() 释放锁，sleep() 没有释放锁。
*   调用 wait() 后需要调用 notify() 或 notifyAll() 方法唤醒线程。
*   wait() 方法声明在 Object 中，sleep() 方法声明在 Thread 中。

### 6. yield()

*   调用后线程进入 runnable 状态。
*   让出 CPU 时间片，之后有可能其他线程获得执行权，也有可能这个线程继续执行。

### 7. join()

*   在线程 B 中调用了线程 A 的 Join()方法，直到线程 A 执行完毕后，才会继续执行线程 B。
*   可以保证线程的顺序执行。
*   join() 方法必须在 线程启动后调用才有意义。
*   使用 wait() 方法实现。

### 9. 线程使用方式

*   继承 Tread 类
*   实现 Runnable 接口
*   实现 Callable 接口：带有返回值

### 10. Runnable 和 Callable 比较

1.  方法签名不同， <code>void Runnable.run()</code> , <code>V Callable.call() throws Exception</code>
2.  是否允许有返回值，<code>Callable</code> 允许有返回值
3.  是否允许抛出异常， <code>Callable</code> 允许抛出异常。
4.  提交任务方式， <code>Callable</code> 使用 <code>Future&lt;T&gt; submit(Callable&lt;T&gt; task)</code> 返回 Future 对象，调用其 get() 方法可以获得返回值， <code>Runnable</code> 使用 <code>void execute(Runnable command)</code> 。

### 11. hapens-before

如果一个操作 happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。

### 12. ThreadLocal

*   场景 主要用途是为了保持线程自身对象和避免参数传递，主要适用场景是按线程多实例（每个线程对应一个实例）的对象的访问，并且这个对象很多地方都要用到。
*   原理 为每个线程创建变量副本，不同线程之间不可见，保证线程安全。使用 ThreadLocalMap 存储变量副本，以 ThreadLocal 为 K，这样一个线程可以拥有多个 ThreadLocal 对象。
*   实际 使用多数据源时，需要根据数据源的名字切换数据源，假设一个线程设置了一个数据源，这个时候就有可能有另一个线程去修改数据源，可以使用 ThreadLocal 维护这个数据源名字，使每个线程持有数据源名字的副本，避免线程安全问题。

### 8. 线程池

1.  分类

*   FixThreadPool 固定数量的线程池，适用于对线程管理，高负载的系统
*   SingleThreadPool 只有一个线程的线程池，适用于保证任务顺序执行
*   CacheThreadPool 创建一个不限制线程数量的线程池，适用于执行短期异步任务的小程序，低负载系统
*   ScheduledThreadPool 定时任务使用的线程池，适用于定时任务

1.  线程池的几个重要参数

*   int corePoolSize, 核心线程数
*   int maximumPoolSize, 最大线程数
*   long keepAliveTime, TimeUnit unit, 超过 corePoolSize 的线程的存活时长，超过这个时间，多余的线程会被回收。
*   BlockingQueue workQueue, 任务的排队队列
*   ThreadFactory threadFactory, 新线程的产生方式
*   RejectedExecutionHandler handler) 拒绝策略

1.  线程池线程工作过程

corePoolSize -> 任务队列 -> maximumPoolSize -> 拒绝策略

<blockquote>核心线程在线程池中一直存活，当有任务需要执行时，直接使用核心线程执行任务。当任务数量大于核心线程数时，加入等待队列。当任务队列数量达到队列最大长度时，继续创建线程，最多达到最大线程数。当设置回收时间时，核心线程以外的空闲线程会被回收。如果达到了最大线程数还不能够满足任务执行需求，则根据拒绝策略做拒绝处理。</blockquote>

1.  线程池拒绝策略（默认抛出异常）

| header 1            | header 2                      |
| ------------------- | ----------------------------- |
| AbortPolicy         | 抛出 RejectedExecutionException |
| DiscardPolicy       | 什么也不做，直接忽略                    |
| DiscardOldestPolicy | 丢弃执行队列中最老的任务，尝试为当前提交的任务腾出位置   |
| CallerRunsPolicy    | 直接由提交任务者执行这个任务                |

1.  如何根据 CPU 核心数设计线程池线程数量

*   IO 密集型 2nCPU
*   计算密集型 nCPU+1
    *   其中 n 为 CPU 核心数量，可通过 <code>Runtime.getRuntime().availableProcessors()</code> 获得核心数：。
    *   为什么加 1：即使当计算密集型的线程偶尔由于缺失故障或者其他原因而暂停时，这个额外的线程也能确保 CPU 的时钟周期不会被浪费。

## 四、Java 虚拟机

### 1. Java 内存结构

![image](https://img-blog.csdnimg.cn/20200904160557208.png)

*   堆 由线程共享，存放 new 出来的对象，是垃圾回收器的主要工作区域。
*   栈 线程私有，分为 Java 虚拟机栈和本地方法栈，存放局部变量表、操作栈、动态链接、方法出口等信息，方法的执行对应着入栈到出栈的过程。
*   方法区 线程共享，存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等信息，JDK 1.8 中方法区被元空间取代，使用直接内存。

### 2. Java 类加载机制

![image](https://img-blog.csdnimg.cn/20200904160557387.png)

*   加载 加载字节码文件。
*   链接
    *   验证 验证字节码文件的正确性。
    *   准备 为静态变量分配内存。
    *   解析 将符号引用（如类的全限定名）解析为直接引用（类在实际内存中的地址）。
*   初始化 为静态变量赋初值。

<blockquote>双亲委派模式</blockquote>

<blockquote>当一个类需要加载时，判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载。加载的时候，首先会把该请求委派该父类加载器的 <code>loadClass()</code> 处理，因此所有的请求最终都应该传送到顶层的启动类加载器 <code>BootstrapClassLoader</code> 中。当父类加载器无法处理时，才由自己来处理。当父类加载器为 null 时，会使用启动类加载器 <code>BootstrapClassLoader</code> 作为父类加载器。</blockquote>

### 3. 垃圾回收算法

*   Mark-Sweep（标记-清除）算法 标记需要回收的对象，然后清除，会造成许多内存碎片。
*   Copying（复制）算法 将内存分为两块，只使用一块，进行垃圾回收时，先将存活的对象复制到另一块区域，然后清空之前的区域。
*   Mark-Compact（标记-整理）算法（压缩法） 与标记清除算法类似，但是在标记之后，将存活对象向一端移动，然后清除边界外的垃圾对象。
*   Generational Collection（分代收集）算法 分为年轻代和老年代，年轻代时比较活跃的对象，使用复制算法做垃圾回收。老年代每次回收只回收少量对象，使用标记整理法。

### 4. 典型垃圾回收器

![image](https://oscimg.oschina.net/oscnet/0665de52604c84bb57094f72d8426f6747b.png)

*   CMS
    *   简介 以获取最短回收停顿时间为目标的收集器，它是一种并发收集器，采用的是 Mark-Sweep 算法
    *   场景 如果你的应用需要更快的响应，不希望有长时间的停顿，同时你的 CPU 资源也比较丰富，就适合适用 CMS 收集器。
    *   垃圾回收步骤
        *   初始标记 (Stop the World 事件 CPU 停顿， 很短) 初始标记仅标记一下 GC Roots 能直接关联到的对象，速度很快；
        *   并发标记 (收集垃圾跟用户线程一起执行) 并发标记过程就是进行 GC Roots 查找的过程；
        *   重新标记 (Stop the World 事件 CPU停顿，比初始标记稍微长，远比并发标记短) 修正由于并发标记时应用运行产生变化的标记。
        *   并发清理，标记清除算法；
    *   缺点
        *   并发标记时和应用程序同时进行，占用一部分线程，所以吞吐量有所下降。
        *   并发清除时和应用程序同时进行，这段时间产生的垃圾就要等下一次 GC 再清除。
        *   采用的标记清除算法，产生内存碎片，如果要新建大对象，会提前触发 Full GC 。

*   G1
    *   简介 是一款面向服务端应用的收集器，它能充分利用多 CPU、多核环境。因此它是一款并行与并发收集器，并且它能建立可预测的停顿时间模型，即可以设置 STW 的时间。
    *   垃圾回收步骤
        1.  初始标记(stop the world 事件 CPU 停顿只处理垃圾)；
        2.  并发标记(与用户线程并发执行)；
        3.  最终标记(stop the world 事件 ,CPU 停顿处理垃圾)；
        4.  筛选回收(stop the world 事件 根据用户期望的 GC 停顿时间回收)
    *   特点
        *   并发与并行 充分利用多核 CPU ，使用多核来缩短 STW 时间，部分需要停顿应用线程的操作，仍然可以通过并发保证应用程序的执行。
        *   分代回收 新生代，幸存带，老年代
        *   空间整合 总体看是采用标记整理算法回收，每个 Region 大小相等，通过复制来回收。
        *   可预测的停顿时间 使用 -XX\:MaxGCPauseMillis=200 设置最长目标暂停值。

<blockquote>在 Java 语言中，可作为 GC Roots 的对象包括 4 种情况：</blockquote>

<blockquote>a) 虚拟机栈中引用的对象（栈帧中的本地变量表）； b) 方法区中类静态属性引用的对象； c) 方法区中常量引用的对象； d) 本地方法栈中 Native 方法引用的对象。</blockquote>

## 五、[[MySQL ]]

## 六、[[Spring]]
## 七、[[网络通信]]

## 八、[[MQ 消息队列]]
## 九、[[Redis]]

## 十、[[Nginx]]

