## CyclicBarrier

CyclicBarrier类似于一个栅栏，线程到达栅栏以后需要等待别的线程，所有线程到达栅栏以后一起通过。它是循环的，可以重复使用。

+ 例子

  这个例子模拟了旅客到达节点等到别的节点。

  ```java
  import java.util.concurrent.BrokenBarrierException;
  import java.util.concurrent.CyclicBarrier;
  
  public class CyclicBarrierDemo {
  
      static class Tourist extends Thread {
          CyclicBarrier barrier;
  
          public Tourist(CyclicBarrier barrier) {
              this.barrier = barrier;
          }
  
          @Override
          public void run() {
             try{
                 Thread.sleep((int)(Math.random() * 1000));
                 barrier.await();
                 System.out.println(getName() + " arrived A " + System.currentTimeMillis());
                 Thread.sleep((int)(Math.random()*1000));
                 barrier.await();//可以重复使用
                 System.out.println(getName() + " arrived B " + System.currentTimeMillis());
             } catch (InterruptedException e) {
             } catch (BrokenBarrierException e) {
             }
          }
      }
  
      public static void main(String... args) {
          int num = 3;
          CyclicBarrier barrier = new CyclicBarrier(num, new Runnable() {
              @Override
              public void run() {
                  System.out.println("all arrived " + System.currentTimeMillis() + " executed by " + Thread.currentThread().getName()); //这个方法，由最后一个到达的线程执行
              }
          });
  
          for(int i=0; i< num; i++) {
              Tourist tourist = new Tourist(barrier);
              tourist.start();
          }
      }
  
  }
  ```

  