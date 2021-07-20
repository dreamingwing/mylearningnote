## Thread

+ 创建线程的方法

  1. 继承Thread类
  2. 实现Runnable接口

+ 线程状态

  + NEW

    还没调用start方法的线程，状态为NEW

  + TERMINATED

    线程运行结束以后，状态为TERMINATED

  + RUNNABLE

    线程调用了start方法，而且没有被阻塞，状态就为RUNNABLE。状态为RUNNABLE，不代表这个线程是一定在执行，也可能在等待某些条件

  + BLOCKED

    等待获取排他锁

  + WAITING

    等待某个条件。sleep，join, wait，LockSupport.park()方法会让线程进入这个状态

  + TIMED_WAITING

    等待某个条件或者超时。sleep，join, waint方法会让线程进入这个状态

+ sleep

  这静态方法会让当前线程睡眠指定的时间，并且让出cpu，但是不会让出锁

+ yield

  调用这个静态方法，会告诉操作系统，当前线程可以让出cpu，但是这个只是建议，操作系统可能会忽略

+ join

  这个方法是Thread类的实例方法，调用这个方法会让当前的线程等待调用join方法的线程完成了，才继续往下执行

  ```java
  public static void main(String...args)  throws InterruptedException {
         Thread thread = new Thread(new Runnable() {
             @SneakyThrows
             @Override
             public void run() {
                 Thread.sleep(1000);
                 System.out.println("child awake");
             }
         });
  
         thread.start();
         thread.join();//主线程会等待子线程结束以后，才会继续进行
  
         System.out.println("Main end");
      }
  ```

  上面这个例子，main方法的线程就是当前的线程，这个线程会等待thread这个被调用join方法的线程执行完了才会继续执行System.out.println("Main end")

+ 共享内存产生的问题

  1. 竞态条件

     两个线程对同一个资源进行操作，如果对资源的访问顺序是敏感的，那就存在竞态条件

  2. 内存可见性

     一个线程对一个变量进行了修改，另外一个线程不一定能马上看到修改，这句是内存可见性问题。因为一条线程读写数据，可能在寄存器或者缓存当中，而不一定都到内存。

+ 线程中断

  Thread类中提供了stop方法，但是这个方法已经被废弃。停止一个线程的主要机制是中断，中断只是一种协作机制，是给线程发送一个取消的信号，但是是否退出或者什么时候退出就由线程决定。

  Thread类提供的中断相关方法：

  ```java
  public boolean isInterrupted() {
          return isInterrupted(false);
       //本质是调用了当前线程的私有isInterrupted方法来返回是否设置中断标志位，这个私有的isInterrupted方法通过参数判断是否重置中断标志位，true为重置，false为不重置
  }
  public void interrupt() //中断这个线程
  public static boolean interrupted() {
          return currentThread().isInterrupted(true);
      //这个静态方法本质是调用了当前线程的私有isInterrupted方法来返回是否设置中断标志位，这个私有的isInterrupted方法通过参数判断是否重置中断标志位，true为重置，false为不重置
  }
  ```

  interrupt方法对线程在不同状态的影响：

  1. Runnable

     如果线程在运行，并且没有IO操作，interrupt方法只会设置中断标记位，没有别的操作，线程需要自己代码逻辑中在合适的是进行中断标记位判断，然后退出线程

  2. Waint / time_waitng

     线程是在这两个状态当中被调用interrupt的话，会抛出InterruptedException，同时清空中断标志位。

  3. blocked

     线程在等待锁的状体，如果被调用interrupt方法，只会设置中断标志位，然后线程还是一直处于blocked状态。所以这个方法是没法中断一个blocked状态的线程的。到线程获得锁了以后，线程还需要像Runnable状态那样，自己判断标记中断位，然后退出

  4. New/terminated

     线程如果处于这两个状态，interrupt方法不起任何作用，连中断标记位都不会设置。

+ 