## AQS

全称AbstracQueueSynchronizer，是一个用来构建锁和同步器的框架。JUC包中基本所有的并发工具都使用到AQS。

+ 类结构

  + AbstractOwnableSynchronizer

    适用于独占的情况

    ```java
    private transient Thread exclusiveOwnerThread;
    ```

    这个属性用来存放独占资源的线程。AQS继承了这个类，对于AQS来说主要是在独占的情况下使用上这个属性。

  + AbstractQueuedSynchronizer

    AQS使用的是模板方法的模式，核心的锁和同步机制都已经封装好，使用者需要实现几个方法即可

    ```java
    private transient volatile Node head; //指向等待队列的头
    private transient volatile Node tail;//指向等待队列的尾
    private volatile int state; //同步状态
    protected final boolean compareAndSetState(int expect, int update); //CAS设置同步状态
    static final long spinForTimeoutThreshold = 1000L;
    private Node enq(final Node node); //通过CAS和循环的方式，让node加入到等待队列
    private Node addWaiter(Node mode);
    private void unparkSuccessor(Node node); //唤醒后续结点
    private void doReleaseShared();
    private void setHeadAndPropagate(Node node, int propagate);
    private void cancelAcquire(Node node);
    private static boolean shouldParkAfterFailedAcquire(Node pred, Node node);
    static void selfInterrupt();//中断当前线程
    private final boolean parkAndCheckInterrupt();//
    final boolean acquireQueued(final Node node, int arg); //
    private void doAcquireInterruptibly(int arg);
    private boolean doAcquireNanos(int arg, long nanosTimeout);
    private void doAcquireShared(int arg);//
    private void doAcquireSharedInterruptibly(int arg);//
    private boolean doAcquireSharedNanos(int arg, long nanosTimeout);//
    protected boolean tryAcquire(int arg);//独占式尝试获取资源，继承类需要实现
    protected boolean tryRelease(int arg);//独占式释放资源，继承类需要实现
    protected int tryAcquireShared(int arg);//共享式尝试获取资源，继承类需要实现
    protected boolean tryReleaseShared(int arg);//共享式释放资源，继承类需要实现
    protected boolean isHeldExclusively();//该线程是否正在独占资源。只有用到condition才需要去实现它
    public final void acquire(int arg);//
    public final void acquireInterruptibly(int arg);//
    public final boolean tryAcquireNanos(int arg, long nanosTimeout);
    public final boolean release(int arg);//
    public final void acquireShared(int arg);
    public final void acquireSharedInterruptibly(int arg);
    public final boolean tryAcquireSharedNanos(int arg, long nanosTimeout);
    public final boolean releaseShared(int arg);//
    public final boolean hasQueuedThreads();
    public final boolean hasContended();
    
    ```

+ 内置FIFO队列

  AQS使用了一个双向链表来实现FIFO队列

  ```java
  static final class Node {
          static final Node SHARED = new Node();//表明这个结点是共享的
          static final Node EXCLUSIVE = null;//表明这个结点时独占的
          static final int CANCELLED =  1;//线程已被删除状态值
          static final int SIGNAL    = -1;//后续结点需要被唤醒状态值
          static final int CONDITION = -2;//线程在等待条件状态值
          /**
           * waitStatus value to indicate the next acquireShared should
           * unconditionally propagate
           */
          static final int PROPAGATE = -3;
          volatile int waitStatus;//线程状态，只能取到 CANCELLED SIGNAL CONDITION PROPAGATE
          volatile Node prev;//指向前一个等待结点
          volatile Node next;//指向后一个等待节点
          volatile Thread thread;//本结点指向的线程
          Node nextWaiter; //独占模式下，为null
      
          final boolean isShared() {
              return nextWaiter == SHARED;
          }
      
          final Node predecessor(); //获取
  
          Node(Thread thread, Node mode) {     // Used by addWaiter
              this.nextWaiter = mode;
              this.thread = thread;
          }
  
          Node(Thread thread, int waitStatus) { // Used by Condition
              this.waitStatus = waitStatus;
              this.thread = thread;
          }
      }
  ```

+ LockSupport

  主要提供两类方法

  1. park方法

     park类方法会让当前线程放弃cpu，进入等待状态，操作系统不会对该线程进行调度

  2. unpark方法

     其他线程调用了这个线程的unpark方法，会让这个线程被唤醒

+ 独占

  + 获取

    独占锁获取的流程简单可以概括为

    1. 当前线程调用AQS的acquire()方法获取锁，如果成功就进入临界区
    2. 如果获取失败，就把当前线程加入到等待队列，调用LockSupport的park方法把让线程进入等待状态
    3. 当队列中的等待线程被唤醒以后就重新尝试获取锁资源，如果成功则进入临界区，否则继续挂起等待

    核心的方法是

    ```java
    public final void acquire(int arg) {
            if (!tryAcquire(arg) //怎样获取资源的核心逻辑，由子类来实现
                &&
                acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
                selfInterrupt();
     }
    
    ```

    1. tryAcquire方法是获取资源的核心逻辑，由子类来实现，如果这个方法成功了就获得了资源，失败了才会向下走
    2. addWaiter首先会构建一个独占模式的结点，然后通过CAS的方式尝试让这条线程快速入队，如果入队不成功，就调用enq方法把线程加入到等待队列。enq是通过死循环+CAS的方式让线程入队
    3. acquireQueued方法会判断当前这个结点是否是头节点，如果是头节点，尝试获取资源。否则就会把线程挂起

  + 释放

    核心可以概括为两个步骤

    1. 释放线程独占的资源
    2. 通过LockSupport唤醒后续结点

+ 共享

  + 获取

    整体流程可以概括为

    1. 线程调用acquireShared方法尝试获取资源，如果获取成功，就进入临界区
    2. 如果获取失败，会被加入到等待队列，调用LockSupport.park方法进入等待状态
    3. 队列中如果有线程被唤醒，会尝试获取锁，如果成功获取锁，会把唤醒事件依次传递给队列里面的结点

  + 释放

    1. 释放资源
    2. 依次唤醒后续结点

+ 条件

  TODO

  <https://www.cnblogs.com/lfls/p/7615982.html?utm_source=debugrun&utm_medium=referral>