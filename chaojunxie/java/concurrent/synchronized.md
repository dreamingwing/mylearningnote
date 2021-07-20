## synchronized关键字

+ 修饰范围

  1. 实例方法

     修饰在实例方法之前，本质上就是作用在this 对象上面。每个对象都会有一个锁和一个等待队列，锁只能被一个线程持有，没法获取锁的情况下，就回被放在等待队列，状态设置为BLOCKED。当线程释放锁了，就回唤醒等待队列里面的任意一个线程，不保证公平。由于synchronized关键字是可重入的，当一个线程持有了这个对象以后，能重入的访问被同一个对象保护的其他方法。

     ```java
     public synchronized void incr() {
         count++;
     }
     ```

  2. 静态方法

     修饰在静态方法之前，实际上是作用在class对象。

  3. 代码块

+ 可重入性

  Synchronized关键字是可重入的

+ 内存可见性

  synchronized关键字能保证在释放锁的时候，所有的操作结果都会被同步到内存，获得锁以后，所有的操作都会冲内存中读取最新的数据。单纯为了解决内存可见性问题，可以使用volatile。

+ 死锁

  为了防止死锁，应该保证多个不同的线程获取同一批资源的时候顺序是一致的。

+ 原理

  + 修饰代码块

    会被编译成为monitorenter和monitorexit包裹着。当开始执行monitorenter的时候，线程会去获取monitor(monitor存在对象头当中)。获取成功以后，锁计数器就+1。执行到monitorexit的时候，计数器会-1，当为0的时候就释放对象。

  + 修饰方法

    修饰方法的时候，会被编译成带ACC_SYNCHRONIZED标识符，这样表面是个同步方法。

+ monitor

  每个对象都会有一个与之相关的monitor，在java中这个体现为ObjectMonitor的实例。当synchronzied关键字执行到monitorenter的时候，会尝试获取锁对象的ObjectMonitor(在重量级锁的情况下，如果不是重量级锁，不会尝试获取)。ObjectMonitor中有以下几个重要变量：

  + _owner: 指向持有ObjectMonitor对象的线程
  + _WaitSet: 等待队列
  + _EntryList: 阻塞队列
  + _recursions: 记录持有ObjectMonitor对象线程获取锁的次数
  + _count: 

- 锁升级