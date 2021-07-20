## 并发队列

+ 无锁非阻塞并发队列

  这类型的队列使用的是无锁不阻塞的机制，核心都是通过CAS的机制。以下两者都是通过链表+CAS的机制，分别实现了单链表和双链表。两者都是无界的。

  + ConcurrentLinkedQueue
  + ConcurrentLinkedDeque

+ 普通阻塞队列

  普通阻塞队列主要使用的是锁+等待条件的机制，会阻塞。

  这三个类都实现了BlockingQueue接口，BlockingQueue接口主要定义了以下方法

  ```java
  boolean add(E e);//入队，成功返回true，在有限的情况下如果对内已经没有空间，会抛出IllegalStateException
  boolean offer(E e);//入队，成功返回true，失败返回false，不会阻塞。
  void put(E e) throws InterruptedException;//入队，会阻塞直到成功，响应中断
  boolean offer(E e, long timeout, TimeUnit unit) throws InterruptedException;//入队，会阻塞指定时间
  E take() throws InterruptedException;//出队，会阻塞知道获取成功，响应中断
  E poll(long timeout, TimeUnit unit) throws InterruptedException;//出队，会阻塞指定时间
  boolean remove(Object o);
  public boolean contains(Object o);
  ```

  + ArrayBlockingQueue

    通过数组实现，是有界的。

  + LinkedBlockingQueue

    基于单向链表实现。创建的时候可以值得大小，也可以不指定。默认的情况下是无限的。

    ```java
    private final ReentrantLock takeLock = new ReentrantLock();//出队锁
    private final Condition notEmpty = takeLock.newCondition();//队列为空等待条件
    private final ReentrantLock putLock = new ReentrantLock();//入队锁
    private final Condition notFull = putLock.newCondition();//队列满等待条件
    ```

  + LinkedBlockingDeque

+ 优先级阻塞队列

  + PriorityBlockingQueue

+ 延时阻塞队列

  + DelayQueue

+ 其他阻塞队列

  + SynchronousQueue
  + LinkedTransferQueue