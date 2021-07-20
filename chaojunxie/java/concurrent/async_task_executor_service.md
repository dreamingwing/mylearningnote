## 异步任务执行服务

+ 例子

  ```java
  import java.util.Random;
  import java.util.concurrent.*;
  
  public class BasicDemo {
      //定义任务
      static class Task implements Callable<Integer> {
          @Override
          public Integer call() throws Exception {
              int sleepSeconds = new Random().nextInt(1000);
              Thread.sleep(sleepSeconds);
              return sleepSeconds;
          }
      }
  
      public static void main(String...args) throws InterruptedException{
          //创建任务执行服务
          ExecutorService executor = Executors.newSingleThreadExecutor();
          //提交任务
          Future<Integer> future = executor.submit(new Task());
          Thread.sleep(100);
          try {
              //获取任务返回值
              System.out.println(future.get());
          } catch (ExecutionException e) {
              e.printStackTrace();
          }
          //关闭任务执行服务
          executor.shutdown();
      }
  }
  ```

+ 基础接口

  主要涉及到以下接口

  + Runnable和Callable

    表示需要执行的任务

  + Executor和ExecutorService

    任务执行服务

  + Future

    异步任务结果

+ Executor

  ```java
  void execute(Runnable command);
  ```

+ ExecutorService

  继承Executor

  ```java
  void shutdown();
  List<Runnable> shutdownNow();
  boolean isShutdown();
  boolean isTerminated();
  boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;
  <T> Future<T> submit(Callable<T> task);
  <T> Future<T> submit(Runnable task, T result);
  Future<?> submit(Runnable task);
  <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;
  <T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;
  <T> T invokeAny(Collection<? extends Callable<T>> tasks,long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;
  ```

  

+ AbstractExecutorService

  继承了ExecutorService，实现了submit，invokeAll，invokeAny方法

+ ThreadPoolExecutor

  继承了AbstractExecutorService

  ```java
  public ThreadPoolExecutor(int corePoolSize,//核心线程数
                                    int maximumPoolSize,//最大线程数
                                    long keepAliveTime,//
                                    TimeUnit unit,
                                    BlockingQueue<Runnable> workQueue,//等待队列
                                    ThreadFactory threadFactory, //产生线程的工厂
                                    RejectedExecutionHandler handler //拒绝策略
                                    );
  ```

  + 线程数

    + 线程个数小于corePoolSize，即使有空闲线程，都会创建一个新线程来执行任务
    + 线程个数大于等于corePoolSize，会尝试排队，如果队满或者其他原因无法入队，会检查线程个数是否达到maximumPoolSize，如果没有达到，创建一条新线程
    + 非核心线程(超出corePoolSize的部分线程)，空闲等待时间如果超过keepAliveTime，机会被终止。如果keepAliveTime为0，则表示所有线程都不会超时

  + 等待队列

    + LinkedBlockingQueue

      链表实现的等待队列，可以有界或者无界，默认无界

    + ArrayBlockingQueue

      数组实现的有界队列

    + PriorityBlockingQueue

      基于堆的无界优先级队列

    + SynchronousQueue

      没有存储空间的同步阻塞队列

    在使用无界队列的情况下，线程数达到corePoolSize以后，新的任务总会排队，所以maximumPoolSize不起作用。

    对于SynchronousQueue, 当尝试排队的时候，只有正好有空闲线程在等待接受任务时，才能入队成功，否则，总会创建新线程，直到maximumPoolSize。

  + 拒绝策略

    对于有界队列，当队列满了，线程数量也达到了maximunPoolSize，新的任务再加进来，会触发拒绝策略。

    + ThreadPoolExecutor.AbortPolicy

      默认方式，会抛出异常。并发量会比较低，但是能相对及时通过异常发现问题。关键任务，建议用这个。

    + ThreadPoolExecutor.DiscardPolicy

      忽略新任务，不抛出异常，也不执行。无法发现异常，非关键任务可以选择这个。

    + ThreadPoolExecutor.DiscardOldestPolicy

      抛弃等待时间最长的任务，任何新任务排队

    + ThreadPoolExecutor.CallerRunsPolicy

      使用提交线程执行任务，而不是线程池里面的线程执行任务。资源消耗不大，但是不允许失败的场景。

+ Executors

  ```java
  public static ExecutorService newFixedThreadPool(int nThreads);//创建一个线程数量固定的线程池
  public static ExecutorService newWorkStealingPool();//创建一个使用Fork-Join的线程池
  public static ExecutorService newSingleThreadExecutor();//创建一个单线程的线程池
  public static ExecutorService newCachedThreadPool();
  public static ScheduledExecutorService newSingleThreadScheduledExecutor();
  public static ScheduledExecutorService newSingleThreadScheduledExecutor()
  ```

  