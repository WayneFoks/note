# Java并发编程重点

## 基础概念

### 锁

**可重入**：“重入”意味着获取锁的操作的粒度是“线程”，而不是调用，一个线程可以多次获得锁。

**互斥锁**：指的是一次最多只能有一个线程持有的锁，比如synchronized和ReentrantLock，同时也是可重入。

### 线程中断

**interrupt**：用interrupt中断线程，并抛出InterruptedException异常。捕获异常让线程继续执行

**决断中断**：比如进入sleep之前就已经触发中断，这时候Main线程把Thread的中断标记设为true，继续执行，等到sleep一触发的时候马上触发中断。

**isInterrupted**：判断中断状态

### 守护线程

Java中有两类线程：User Thread\(用户线程\)、Daemon Thread\(守护线程\)。

**生命周期**

守护线程作用是为其他前台线程的运行提供便利服务，而且仅在普通、非守护线程仍然运行时才需要，比如垃圾回收线程就是一个守护线程。

当VM检测仅剩一个守护线程，而用户线程都已经退出运行时，VM就会退出。

**使用**

1. 用户可以用Thread的setDaemon（true）方法设置当前线程为守护线程。setDaemon\(true\)必须在调用线程的start\(\)方法之前设置，否则会跑出IllegalThreadStateException异常。
2. 在守护线程中产生的新线程也是守护线程。
3. 不要认为所有的应用都可以分配给守护线程来进行服务，比如读写操作，用户线程一旦退出，VM就退出了

### **线程阻塞**

线程可以阻塞于四种状态：

1. `Thread.sleep()`；
2. 当线程碰到一条`wait()`语句时，它会一直阻塞到接到通知`notify()`、被中断或经过了指定毫秒时间为止
3. I/O。常见的一种方式是`InputStream`的`read()`方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间；
4. 线程也可以阻塞等待获取某个对象锁的排他性访问权限（即等待获得`synchronized`语句必须的锁时阻塞）。

注意，**并非所有的阻塞状态都是可中断的**，以上阻塞状态的前两种可以被中断，后两种不会对中断做出反应

### Runnable和Thread实现区别

Runnable接口相比继承Thread类有如下优势：

1. Runnable是接口，Thread是类，没有单继承局限
2. Runnable更好被复用
3. Runnable可以被多个线程共享，Thread只能是独立一个线程。[前者注意线程安全](http://www.importnew.com/20584.html)。

## volatile

Java 1.2之后的内存模型下，线程可以把变量保存在本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。

Java语言规范中指出：为了获得最佳速度，允许线程保存共享成员变量的私有拷贝，而且只当线程进入或者离开同步代码块时才将私有拷贝与共享内存中的原始值进行比较。

Volatile修饰的成员变量在每次被线程访问时，都强迫从共享内存中重读该成员变量的值。而且，当成员变量发生变化时，强迫线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

在访问volatile变量时不会执行加锁操作，也就不会执行线程阻塞，因此volatilei变量是一种比synchronized关键字更轻量级的同步机制。

对于非volatile修饰的变量，尽管jvm的优化，会导致变量的可见性问题，但这种可见性的问题也只是在短时间内高并发的情况下发生，CPU执行时会很快刷新Cache，一般的情况下很难出现，而且出现这种问题是不可预测的，与jvm, 机器配置环境等都有关。

## synchronized

**概念**

* 多线程同时并发访问的资源叫做临界资源
* 采用synchronized修饰符所获得的锁,叫做互斥锁。每个对象的锁只能分配给一个线程，因此叫做互斥锁。
* 每个对象都有一个monitor\(锁标记\)。synchronized关键字经过编译后，会在同步块的前后分别形成monitorenter和monitorexit这两个字节码指令。
* 当线程拥有锁标记时才能访问这个资源，没有锁标记便进入锁池。
* 任何一个对象系统都会为其创建一个互斥锁，这个锁是为了分配给线程的，防止打断原子操作。

**同步机制获取互斥锁**

* 如果同一个方法内同时有两个或更多线程，则每个线程有自己的局部变量拷贝。
* 类的每个实例都有自己的对象级别锁。当一个线程访问实例对象中的synchronized同步代码块或方法时，便获取了该实例的对象级别锁。其他线程访问该实例中被synchronized修饰的代码块时，被阻塞。但是不同实例对象相互不影响。
* 类级别锁被特定类的所有示例共享，它用于控制对static成员变量以及static方法的并发访问。

**内存可见性** 加锁机制（即同步机制）既可以确保可见性又可以确保原子性，而volatile变量只能确保可见性，原因是声明为volatile的简单变量如果当前值与该变量以前的值相关，那么volatile关键字不起作用，也就是说如下的表达式都不是原子操作：“count++”、“count = count+1”。

## Lock

Lock接口有3个实现它的类：ReentrantLock、ReetrantReadWriteLock.ReadLock和ReetrantReadWriteLock.WriteLock，即重入锁、读锁和写锁。lock必须被显式地创建、锁定和释放。

当你需要以下高级特性时，才应该使用：可定时的、可轮询的与可中断的锁获取操作，公平队列，或者非块结构的锁。否则，请使用synchronized。

### 可中断锁

ReetrantLock有两种锁：忽略中断锁和响应中断锁。忽略中断锁与synchronized实现的互斥锁一样，不能响应中断，而响应中断锁可以响应中断。

如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，线程B不想等待了，我们可以让它中断自己或者在别的线程中中断它。如果此时ReetrantLock提供的是忽略中断锁，则它不会去理会该中断，而是让线程B继续等待，而如果此时ReetrantLock提供的是响应中断锁，那么它便会处理中断，让线程B放弃等待，转而去处理其他事情。

### Condition/signal/await

Condition、signal、await的使用，处理更复杂的多线程问题时，会有明显的优势。参考：[http://www.importnew.com/20822.html](http://www.importnew.com/20822.html)

### 对比synchronized

1. 等待可中断：当持有锁的线程长期不释放锁时，正在等待的线程可以选择放弃等待，改为处理其他事情，它对处理执行时间非常上的同步块很有帮助。而在等待由synchronized产生的互斥锁时，会一直阻塞，是不能被中断的。
2. 可实现公平锁：多个线程在等待同一个锁时，必须按照申请锁的时间顺序排队等待，而非公平锁则不保证这点，在锁释放时，任何一个等待锁的线程都有机会获得锁。synchronized中的锁时非公平锁，ReentrantLock默认情况下也是非公平锁，但可以通过构造方法ReentrantLock（ture）来要求使用公平锁。
3. 锁可以绑定多个条件：ReentrantLock对象可以同时绑定多个Condition对象（名曰：条件变量或条件队列），而在synchronized中，锁对象的wait（）和notify（）或notifyAll（）方法可以实现一个隐含条件，但如果要和多于一个的条件关联的时候，就不得不额外地添加一个锁，而ReentrantLock则无需这么做，只需要多次调用newCondition（）方法即可。而且我们还可以通过绑定Condition对象来判断当前线程通知的是哪些线程（即与Condition对象绑定在一起的其他线程）。

## wait/notify

Object是所有类的超类，它有5个方法组成了等待/通知机制的核心：notify\(\)、notifyAll\(\)、wait\(\)、wait\(long\)和wait\(long，int\)，都被声明为final。

### wait和notify

* 在调用之前，线程必须要获得对象级别锁，即只能在同步方法或同步块中调用。
* 如果调用时，没有持有适当的锁，则抛出IllegalMonitorStateException。

**wait** 该方法用来将当前线程置入休眠状态，直到接到通知或被中断为止。进入wait\(\)方法后，当前线程释放锁。在从wait\(\)返回前，线程与其他线程竞争重新获得锁。

**notify** notify后，当前线程不会马上释放该对象锁，要等到程序退出synchronized代码块后，当前线程才会释放锁

### 深入理解

* 如果线程调用了对象的wait\(\)方法，那么线程便会处于该对象的等待池中，**等待池**中的线程不会去竞争该对象的锁。
* 被唤醒\(notify\)的的线程便会进入该对象的**锁池**中，锁池中的线程会去竞争该对象锁。

**通知遗漏** threadA还没开始wait的时候，threadB已经notify了，这样，threadB通知是没有任何响应的，当threadB退出synchronized代码块后，threadA再开始wait，便会一直阻塞等待，直到被别的线程打断（interrupt）。 [通知遗漏一般可以用boolean变量值规避](http://www.importnew.com/20677.html)。

### sleep\(\)和wait\(\)的区别

sleep只释放cpu资源，不改变锁状态；wait释放了锁，notify后继续执行后面的代码。

* sleep\(\)是Thread的静态方法，让调用线程进入睡眠状态，让出执行机会给其他线程，等到休眠时间结束后，线程进入就绪状态和其他线程一起竞争cpu的执行时间。 

  因为sleep\(\)是static的方法，他不能改变对象的机锁，当一个synchronized块中调用了sleep\(\)方法，线程虽然进入休眠，但是对象的机锁没有被释放，其他线程依然无法访问这个对象。

* wait\(\)是Object类的方法，当一个线程执行到wait方法时，它就进入到一个和该对象相关的等待池，同时释放对象的机锁，使得其他线程能够访问

### 线程的四种状态

* 新建（new）：处于该状态的时间很短暂。已被分配了必须的系统资源，并执行了初始化。表示有资格获得CPU时间。调度器可以把该线程变为runnable或者blocked状态
* 就绪（Runnable）：这种状态下只要调度器把时间片分配给线程，线程就能运行。处在这种状态就是可运行可不运行的状态
* 阻塞（Bolocked）：线程能够运行，但有个条件阻止它的运行。当线程处于阻塞状态时，调度器将会忽略线程，不会分配给线程任何CPU时间（例如sleep）。只有重新进入了就绪状态，才有可能执行操作。
* 死亡（Dead）：处于死亡状态的线程讲不再是可调度的，并且再也不会得到CPU时间。任务死亡的通常方式是从run\(\)方法返回。

  ![https://blog.csdn.net/a58220655/article/details/76695142](.gitbook/assets/image%20%283%29.png)

## 线程模式

### 生产者-消费者模型

[http://www.importnew.com/20709.html](http://www.importnew.com/20709.html)

## 内存模式/happen-before

Java内存模型中的八条可保证happen—before的规则，它们无需任何同步器协助就已经存在，可以在编码中直接使用。如果两个操作之间的关系不在此列，并且无法从规则推导出来的话，它们就没有顺序性保障，虚拟机可以对它们进行随机地重排序。

1. 程序次序规则：在一个单独的线程中，按照程序代码的执行流顺序，（时间上）先执行的操作happen—before（时间上）后执行的操作。
2. 管理锁定规则：一个unlock操作happen—before后面（时间上的先后顺序，下同）对同一个锁的lock操作。
3. volatile变量规则：对一个volatile变量的写操作happen—before后面对该变量的读操作。
4. 线程启动规则：Thread对象的start（）方法happen—before此线程的每一个动作。
5. 线程终止规则：线程的所有操作都happen—before对此线程的终止检测，可以通过Thread.join（）方法结束、Thread.isAlive（）的返回值等手段检测到线程已经终止执行。
6. 线程中断规则：对线程interrupt（）方法的调用happen—before发生于被中断线程的代码检测到中断时事件的发生。
7. 对象终结规则：一个对象的初始化完成（构造函数执行结束）happen—before它的finalize（）方法的开始。
8. 传递性：如果操作A happen—before操作B，操作B happen—before操作C，那么可以得出A happen—before操作C。

### 利用happen—before规则分析DCL

DCL即双重检查加锁

```java
public class LazySingleton {
    private int someField;

    private static LazySingleton instance;

    private LazySingleton() {
        this.someField = new Random().nextInt(200)+1;         // (1)
    }

    public static LazySingleton getInstance() {
        if (instance == null) {                               // (2)
            synchronized(LazySingleton.class) {               // (3)
                if (instance == null) {                       // (4)
                    instance = new LazySingleton();           // (5)
                }
            }
        }
        return instance;                                      // (6)
    }

    public int getSomeField() {
        return this.someField;                                // (7)
    }
}
```

我们要说明的是线程Ⅰ的语句\(1\)并不happen-before线程Ⅱ的语句\(7\)

线程Ⅱ在执行getInstance\(\)方法的语句\(2\)时，由于对instance的访问并没有处于同步块中，因此线程Ⅱ可能观察到也可能观察不到线程Ⅰ在语句\(5\)时对instance的写入，也就是说instance的值可能为空也可能为非空。

**情况一**

假设instance的值非空，也就观察到了线程Ⅰ对instance的写入，这时线程Ⅱ就会执行语句\(6\)直接返回这个instance的值，然后对这个instance调用getSomeField\(\)方法，该方法也是在没有任何同步情况被调用，因此整个线程Ⅱ的操作都是在没有同步的情况下调用 ，这时我们便无法利用上述8条happen-before规则得到线程Ⅰ的操作和线程Ⅱ的操作之间的任何有效的happen-before关系（主要考虑规则的第2条，但由于线程Ⅱ没有在进入synchronized块），这就意味着线程Ⅱ在执行语句\(7\)完全有可能观测不到线程Ⅰ在语句\(1\)处对someFiled写入的值

**情况二**

线程Ⅱ在执行语句\(2\)时也有可能观察空值。如果是种情况，那么它需要进入同步块，并执行语句\(4\)。在语句\(4\)处线程Ⅱ不可能够读到instance的空值了。这里因为这时对instance的写和读都是发生在同一个锁确定的同步块中。线程Ⅱ在语句\(3\)处会执行一个lock操作，而线程Ⅰ在语句\(5\)后会执行一个unlock操作，这两个操作都是针对同一个锁–Singleton.class，因此根据第2条happen-before规则，线程Ⅰ的unlock操作happen-before线程Ⅱ的lock操作，再利用单线程规则，线程Ⅰ的语句\(5\) -&gt; 线程Ⅰ的unlock操作，线程Ⅱ的lock操作 -&gt; 线程Ⅱ的语句\(4\)，再根据传递规则，就有线程Ⅰ的语句\(5\) -&gt; 线程Ⅱ的语句\(4\)，也就是说线程Ⅱ在执行语句\(4\)时能够观测到线程Ⅰ在语句\(5\)时对Singleton的写入值。接着对返回的instance调用getSomeField\(\)方法时，我们也能得到线程Ⅰ的语句\(1\) -&gt; 线程Ⅱ的语句\(7\)（由于线程Ⅱ有进入synchronized块，根据规则2可得），这表明这时getSomeField能够得到正确的值。

**另一个问题**

假设线程A执行到instance = new LazySingleton\(\)这句，这里看起来是一句话，但实际上它并不是一个原子操作，我们只要看看这句话被编译后在JVM执行的对应汇编代码就发现，这句话被编译成8条汇编指令，大致做了3件事情：

1. 给LazySingleton的实例分配内存。
2. 初始化LazySingleton\(\)的构造器
3. 将instance对象指向分配的内存空间（注意到这步instance就非null了）。

但是，由于Java编译器允许处理器乱序执行，以及JDK1.5之前JMM（Java Memory Medel，即Java内存模型）中Cache、寄存器到主内存回写顺序的规定，上面的第二点和第三点的顺序是无法保证的，也就是说，执行顺序可能是1-2-3也可能是1-3-2，如果是后者，并且在3执行完毕、2未执行之前，被切换到线程B上，这时候instance因为已经在线程A内执行过了第三点，instance已经是非空了，所以线程B直接拿走instance，然后使用。JDK是1.5或之后的版本，只需要将instance的定义改成“private volatile static LazySingleton instance = null;”就可以保证每次都去instance都从主内存读取。

### 内存模型

**内存间交互操作** [http://www.importnew.com/20778.html](http://www.importnew.com/20778.html)

**final域** final域能确保初始化过程的安全性。具体而言，就是被final修饰的字段在构造器中一旦被初始化完成，并且构造器没有把“this”的引用传递出去（this引用逃逸是一件很危险的事情，其他线程有可能通过这个引用访问到“初始化了一半”的对象），那么在其他线程中就能看到final字段的值。它所带来的安全性是最简单最纯粹的。

## Java并发类

### Executor框架与线程池

Executor框架包括：线程池，Executor，Executors，ExecutorService，CompletionService，Future，Callable。

ExecutorService的生命周期包括三种状态：运行、关闭、终止。创建后便进入运行状态，当调用了shutdown（）方法时，便进入关闭状态，此时意味着ExecutorService不再接受新的任务，但它还在执行已经提交了的任务，当素有已经提交了的任务执行完后，便到达终止状态。

Executors提供了一系列工厂方法用于创先线程池，返回的线程池都实现了ExecutorService接口。分别可以创建固定数目线程池、可重用线程池、单线程、周期性任务执行线程池。

#### Callable

Callable有返回值，Runnable没有返回值。Callable的call\(\)方法只能通过ExecutorService的submit\(Callable task\) 方法来执行，并且返回一个 Future，是表示任务等待完成的Future。

#### 排队策略

* 直接提交。缓冲队列采用SynchronousQueue
* 无界队列。使用无界队列（典型的便是采用预定义容量的LinkedBlockingQueue，理论上是该缓冲队列可以对无限多的任务排队）
* 有界队列。当使用有限的maximumPoolSizes时（一般缓冲队列使用ArrayBlockingQueue，并制定队列的最大长度）

### 阻塞队列和阻塞栈

阻塞队列的接口是java.util.concurrent.BlockingQueue，Java5并发新特性中的内容，它有多个实现类：ArrayBlockingQueue、DelayQueue、LinkedBlockingQueue、PriorityBlockingQueue、SynchronousQueue

阻塞栈与阻塞队列相似，只是它是Java6中加入的新特性，阻塞栈的接口java.util.concurrent.BlockingDeque也有很多实现类

### 障碍器CyclicBarrier

它适用于这样一种情况：你希望创建一组任务，它们并发地执行工作，另外的一个任务在这一组任务并发执行结束前一直阻塞等待，直到该组任务全部执行结束，这个任务才得以执行。这非常像CountDownLatch，只是CountDownLatch是只触发一次的事件，而CyclicBarrier可以多次重用。

### 信号量Semaphore

Java并发包中的信号量Semaphore实际上是一个功能完毕的计数信号量，从概念上讲，它维护了一个许可集合，对控制一定资源的消费与回收有着很重要的意义。Semaphore可以控制某个资源被同时访问的任务数，它通过acquire（）获取一个许可，release（）释放一个许可。如果被同时访问的任务数已满，则其他acquire的任务进入等待状态，直到有一个任务被release掉，它才能得到许可。

### Java安全使用集合

[Collections类可以获取通过同步方法封装非同步集合而得到的集合](http://www.importnew.com/20624.html)。

## 参考

> [Java并发编程](http://www.importnew.com/20886.html)

