## 线程间协作

+ 线程间协作场景

  + 生产者消费者模式
  + 同时开始
  + 等待结束
  + 异步结果
  + 集合点

+ Wait && Notify

  wait和notify是在Object中的方法，所以所有的类中都会有。

  例子：

  ```java
  public class WaitThread extends Thread {
  
      private volatile  boolean fire = false;
  
      @Override
      public void run() {
          try {
              synchronized (this) {
                  while(!fire) {
                      wait();
                  }
                  System.out.println("fired");
              }
          } catch (InterruptedException e) {
  
          }
      }
  
      public synchronized void fire() {
          this.fire = true;
          notify();
      }
  
      public static void main(String...args) throws InterruptedException{
          WaitThread waitThread = new WaitThread();
          waitThread.start();
          Thread.sleep(1000);
          System.out.println("fire");
          waitThread.fire();
      }
  
  }
  ```

  对于每个对象，除了有个等待队列以外，也会有个条件队列。当wait方法被调用的时候，这条线程会被放到synchronized对象保护对象的条件队列当中，状态尾WAITING或者TIMED_WAITING。当notify方法被调用的时候，等待队列里面的对象会被激活，然后竞争锁，得到锁的，就继续执行，得不到的会进入到等待队列，状态为BLOCKED。wait和notify必须在synchronized块内执行，而且需要时同一个保护对象。

+ 