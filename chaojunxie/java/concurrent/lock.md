## 显式锁

### Lock接口

方法

```java
	void lock()
	void lockInterruptibly()  throws InterruptedException
	boolean tryLock()
	boolean tryLock(long time, TimeUnit unit) throws InterruptedException
	void unlock()
	Condition newCondition()
```

### ReentrantLock

ReentrantLock是可重入锁，可重入锁是线程在获取锁的情况下，可以重复进入临界区。实现了Lock接口。

ReentrantLock实现了和syncrhonized一样的语义。其核心的原理是通过AQS来对资源进行互斥管理。

通过继承AQS，ReentrantLock实现了两种锁模式

+ 公平锁

  先进入等待队列的会先获得资源

  优点：所有的线程都能获得资源，不出现一直等待的情况

  缺点：吞吐量低，cpu唤醒线程耗费的资源较多

+ 非公平锁

  唤醒的时候，所有的线程都会尝试获取锁，获取失败的就继续进入等待队列。

  优点：吞吐量高，而且不需要唤醒所有的线程，cpu的消耗较低

  缺点：可能有线程一直处于等待状态

### ReadWriteLock接口

提供读写锁的基础接口，提供了以下两个方法

```java
Lock readLock();//获取写锁
Lock writeLock();//获取读锁
```

读写锁中，读-读操作是可并行的，读-写、写-写操作不可并行。

写锁和别的任何锁互斥，意思是只有在没有任何线程持有任何锁的情况下才能获得写锁；在持有写锁的情况下，其他任何线程都无法获得任何锁。

### ReentrantReadWriteLock

可重入读写锁。

+ 使用例子

  ```java
  import java.util.HashMap;
  import java.util.Map;
  import java.util.concurrent.locks.Lock;
  import java.util.concurrent.locks.ReadWriteLock;
  import java.util.concurrent.locks.ReentrantReadWriteLock;
  
  public class MyCache {
  
      private Map<String, Object> map = new HashMap<>();
      private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
      private Lock readLock = readWriteLock.readLock();
      private Lock writeLock = readWriteLock.writeLock();
  
      public Object get(String key) {
          readLock.lock();
          try{
              return map.get(key);
          } finally {
              readLock.unlock();
          }
      }
  
      public Object put(String key, Object value) {
          writeLock.lock();
          try {
              return map.put(key, value);
          } finally {
              writeLock.unlock();
          }
      }
  
      public void clear() {
          writeLock.lock();
          try {
              map.clear();
          } finally {
              writeLock.unlock();
          }
      }
  
  }
  ```

+ 原理

  ReentrantReadWriteLock有一个读锁和写锁，但是等待队列都是共享一个的。当获取写锁的时候，需要确保没有任何别的线程获取到锁。写锁释放了以后，会唤醒等待队列里面的第一个线程，这个线程可能是等待读锁也可能是写锁。当获取读锁的时候，如果确定没有写锁存在，就成功获取，然后回依次唤醒后续的所有读锁，直到遇到第一个写锁。当所有读锁都释放了以后，会唤醒队列中的第一个等待线程。

  

