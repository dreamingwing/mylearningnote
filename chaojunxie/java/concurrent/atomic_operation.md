## 原子变量

在原子变量中，所有的操作都是原子性的，也就意味着线程安全

+ 包括

  1. AtomicBoolean
  2. AtomicInteger
  3. AtomicLong
  4. AtomicReference

+ 实现原理

  本质的实现都是基于unsafe类的CAS