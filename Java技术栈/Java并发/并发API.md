# Java并发API

> 主要介绍Java原生的并发API使用方式
>
> 对`synchronized`,还有`notify(),notifyAll(),join(), wait()`傻傻分不清,今天就来理一理

## synchronized

> 1. 每一个实例对象都有一个相应的`monitor`.在Hotspot中，对象的监视器(monitor)锁对象是ObjectMonitor对象实现(C++),也就是重量级锁.
>
> 2. `synchronized`可以修饰方法和代码块.
>
>    + 修饰实例方法时,锁对象是**this对象**;
>    +  修饰静态方法时,锁对象是**类的class对象**;
>    +  修改代码块时,锁对象是**指定的对象**
>
> 3. `synchronized`具体实现时:
>
>    + 修饰方法,由方法调用指令来读取运行时常量池中的**ACC_SYNCHRONIZED**标志隐式实现的，如果方法表结构（method_info Structure）中的ACC_SYNCHRONIZED标志被设置，那么**线程在执行方法前会先去获取锁对象的monitor对象，如果获取成功则执行方法代码，执行完毕后释放monitor对象，如果monitor对象已经被其它线程获取，那么当前线程被阻塞**。
>    + 修饰代码块,需要同步的代码块开始的位置插入**monitorenter**指令，在同步结束的位置或者异常出现的位置插入**monitorexit**指令；JVM要保证monitorentry和monitorexit都是成对出现的，任何对象都有一个monitor与之对应，当这个对象的monitor被持有以后，它将处于锁定状态。
>
> 4. 主要是利用锁对象的对象头的**MarkWord**字段,在64位虚拟机中,共8个字节.
>
> 5. ```c++
>    ObjectMonitor() {
>        _count        = 0; //用来记录该对象被线程获取锁的次数
>        _waiters      = 0;
>        _recursions   = 0; //锁的重入次数
>        _owner        = NULL; //指向持有ObjectMonitor对象的线程 
>        _WaitSet      = NULL; //处于wait状态的线程，会被加入到_WaitSet
>        _WaitSetLock  = 0 ;
>        _EntryList    = NULL ; //处于等待锁block状态的线程，会被加入到该列表
>      }
>    ```
>
> 6. 并发访问`synchronized`时,无法获取**monitor对象**的线程,会被阻塞(**Blocked**),进入`_EntryList`列表,当monitor对象释放时会被唤醒(释放时机:同步方法或者代码块正常执行结束;发生异常;wait()方法调用).
>
> 7. 调用`wait()`方法的线程会陷入**Waiting**状态,进入`_WaitSet`集合中,其他线程调用`notify()/notifyAll()`方法时被唤醒.这些方法继承自**Object类**,调用时应该使用锁对象来调用.
>
> 8. ![img](https://raw.githubusercontent.com/tiaotiaopig/feng-images-store/main/images/20180322153316377)
>
> 9. JDK1.6后对`synchronized`的锁机制进行了优化,根据并发的程度,会有个锁升级的过程.

## Semaphore(信号量)

> 就是操作系统的信号量机制,控制并发线程的数量.信号量P可以看作是资源的数量.`P > 0`可以正常执行,`P <= 0`时会阻塞,直到`P > 0`时会被唤醒.有个公平性参数,可以控制(FIFO)线程有序获取信号量.
>
> ```java
> // 构造函数,许可数量也就是P
> Semaphore(int permits)
> Creates a Semaphore with the given number of permits and nonfair fairness setting.
>     
> Semaphore(int permits, boolean fair)
> Creates a Semaphore with the given number of permits and the given fairness setting.
> ```
>
> 常用的API方法
>
> ```java
> void	acquire()
> Acquires a permit from this semaphore, blocking until one is available, or the thread is interrupted.
> void	acquire(int permits)
> Acquires the given number of permits from this semaphore, blocking until all are available, or the thread is interrupted.
>     
> void	release()
> Releases a permit, returning it to the semaphore.
> void	release(int permits)
> Releases the given number of permits, returning them to the semaphore.
> ```
>
> 简单原理:**获取许可,数量不足时,线程会被中断,直到其他线程释放许可,可获取的许可数量满足条件时,线程会被唤醒.**
>
> 简单使用:**如果线程要访问一个资源就必须先获得信号量。如果信号量内部计数器大于0，信号量减1，然后允许共享这个资源；否则，如果信号量的计数器等于0，信号量将会把线程置入休眠直至计数器大于0.当信号量使用完时，必须释放**

## ReentrantLock+Condition

> 可重入锁,提供比`synchronized`更加丰富的特性,如公平锁,等待条件等
>
> `synchronized`可以看作时等待`monitor对象`这一条件,而R可以提供更丰富的条件,每个条件都有一个等待队列,建议使用方式如下:
>
> ```java
> public void m() {
>   lock.lock();  // block until condition holds
>   try {
>     // ... method body
>   } finally {
>     lock.unlock()
>   }
> }
> ```
>
> `Condition.await() 和 Condition.signal() / Condition.signalAll()`