# Lock 类

> `java.util.concurrent.locks`包下有Java自己实现的锁工具类（`Lock`, `ReadWriteLock`）
>
> 两个主要的实现类，`ReentrantLock` 和 `ReentrantReadWriteLock`
>
> 它提供了比`synchronized`更广泛的锁操作，提供灵活构建和不同的特性（公平，共享）
>
> 锁，是控制多线程访问共享资源的工具，通常锁提供独占式的访问共享资源，一个时刻只有一个线程可以获取锁，所有需要访问共享资源的线程必须先获取锁
>
> 但是也有一些锁允许并发访问共享资源，例如：`ReadWriteLock`的读锁
>
> `synchronized`代码块和方法提供访问每个对象关联的隐式监视器锁的途径，但是要求所有锁获取和释放必须发生在一个块结构。但是在有些场景下，锁需要更加灵活
>
> 增加了锁的灵活性，随之增加了锁维护成本，需要显示进行加锁和解锁操作，惯用方式如下：
>
> ```java
>  Lock l = ...;
>  l.lock();
>  try {
>    // access the resource protected by this lock
>  } finally {
>    l.unlock();
>  }
> ```
>
> 在try finally 中，确保无论发生什么异常，获取的锁都要释放，避免死锁
>
> Locks的实现类，提供了额外的功能，非阻塞轮询获取锁`tryLock()`,获取锁失败中断`lockInterruptibly()`,一段时间内轮询`tryLock(long timeout, TimeUnit)`超时后中断
>
> Locks就是个普通类，也可以作为同步代码块的加锁对象，但是不推荐，容易混淆

## ReentrantLock

> Lock接口的具体实现类，可重入互斥锁，提供和`synchronized`相同行为和语义，功能更多
>
> 该锁会被最后一个成功加锁并且没解锁的线程持有
>
> 一个线程调用`lock`方法，如果锁没有被其他线程持有，则获取锁；如果是自己持有，方法立即返回（可重入）
>
> 构造方法可以传入参数，设置是否公平，默认false
>
> 公平锁优先最长等待的线程加锁，非公平锁不保证具体的锁获取次序，公平锁会导致较低的全局吞吐量，没有**锁饥饿**，但是公平锁没法保证线程调度的公平性，仍然会有线程饥饿
>
> `tryLock()`也不会考虑公平性
>
> 推荐：**try块**紧跟在**lock**调用之后
>
> ```java
> class X {
>    private final ReentrantLock lock = new ReentrantLock();
>    // ...
> 
>    public void m() {
>      lock.lock();  // block until condition holds
>      try {
>        // ... method body
>      } finally {
>        lock.unlock()
>      }
>    }
>  }
> ```
>
> 除了实现Lock接口，还提供了一些检测锁状态的方法，实现了序列化接口，可重入次数是**int上限**

## ReadWriteLock

> 读写锁接口，维护一对锁：读锁和写锁。
>
> 读锁可以被多个读线程共同持有，而写锁是互斥的
>
> 保证内存同步性，获取读锁后，能够见到上个写锁释放后，修改的内容
>
> 同一时刻，只有一个写线程可以修改共享数据，但是可以有多个读线程读取共享数据，在一些情况下，可以提高并发量（相比互斥锁）
>
> 也就是说，读写锁使用不合理的话，可能还有反效果
>
> 读写锁能够提高性能，主要取决于：读写操作的相对频次、读写操作的时长、数据竞争程度（并发线程的数量）
>
> 写多读少、读操作时间短（维护读锁更耗时，得不偿失）采用读写锁是不合适的
>
> 还有一些控制策略，需要考虑（果然，增加东西必然会导致系统复杂性增加，感觉还是指数）

## ReentrantReadWriteLock

> 读写锁接口的实现，有两个内部类实现Lock接口，用来完成读锁和写锁的功能
>
> | `static class ` | `ReentrantReadWriteLock.ReadLock`The lock returned by method [`readLock()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/ReentrantReadWriteLock.html#readLock--). |
> | --------------- | :----------------------------------------------------------- |
> | `static class ` | `ReentrantReadWriteLock.WriteLock`The lock returned by method [`writeLock()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/ReentrantReadWriteLock.html#writeLock--). |