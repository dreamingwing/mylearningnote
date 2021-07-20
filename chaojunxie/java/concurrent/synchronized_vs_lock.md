## Synchronized VS Lock

+ 等待可中断
+ 公平锁
+ 锁绑定多个条件

- 相同点
  - 都实现了多线程同步和内存可见性语义。
  - 都是可重入锁。
- 不同点
  - 同步实现机制不同
    - `synchronized` 通过 Java 对象头锁标记和 Monitor 对象实现同步。
    - ReentrantLock 通过CAS、AQS（AbstractQueuedSynchronizer）和 LockSupport（用于阻塞和解除阻塞）实现同步。
      *
  - 可见性实现机制不同
    - `synchronized` 依赖 JVM 内存模型保证包含共享变量的多线程内存可见性。
    - ReentrantLock 通过 ASQ 的 `volatile state` 保证包含共享变量的多线程内存可见性。
  - 使用方式不同
    - `synchronized` 可以修饰实例方法（锁住实例对象）、静态方法（锁住类对象）、代码块（显示指定锁对象）。
    - ReentrantLock 显示调用 tryLock 和 lock 方法，需要在 `finally` 块中释放锁。
  - 功能丰富程度不同
    - `synchronized` 不可设置等待时间、不可被中断（interrupted）。
    - ReentrantLock 提供有限时间等候锁（设置过期时间）、可中断锁（lockInterruptibly）、condition（提供 await、condition（提供 await、signal 等方法）等丰富功能
  - 锁类型不同
    - `synchronized` 只支持非公平锁。
    - ReentrantLock 提供公平锁和非公平锁实现。当然，在大部分情况下，非公平锁是高效的选择。