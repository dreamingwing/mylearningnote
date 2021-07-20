## CountDownLatch

CountDownLatch类似于一个门栓，线程到达门栓以后需要等待，另外一个角色进行倒计时，当倒计时为0，线程就会一起通过门栓。它是一次性的，不能重复使用

+ 例子

  + 同步开始

    这个例子里面，子线程都会进行等待，主线程进行倒计时，主线程倒计时完以后，子线程同步开始。

    ```java
    import java.util.concurrent.CountDownLatch;
    
    public class RacerWithCountDownLatch {
    
        static class Racer extends Thread {
    
            CountDownLatch latch;
    
            public Racer(CountDownLatch latch) {
                this.latch = latch;
            }
    
            @Override
            public void run() {
                try {
                    this.latch.await();
                    System.out.println(getName() + " start run " + System.currentTimeMillis());
                } catch (InterruptedException e) {
                }
            }
        }
    
        public static void main(String...args) throws InterruptedException{
            int num = 10;
            CountDownLatch latch = new CountDownLatch(1);
    
            for(int i=0; i<num; i++) {
                Racer racer = new Racer(latch);
                racer.start();
            }
            Thread.sleep(1000);
            latch.countDown();
        }
    }
    ```

  + 主从协作

    主线程等待，每个子线程到达以后就countDown。

    ```java
    import java.util.concurrent.CountDownLatch;
    
    public class MasterWorkerDemo {
    
        static class Worker extends Thread {
            CountDownLatch latch;
            public Worker(CountDownLatch latch) {
                this.latch = latch;
            }
    
            @Override
            public void run() {
                try{
                    Thread.sleep((int)(Math.random() * 1000));
                    if(Math.random()<0.02) {
                        throw new RuntimeException("bad luck");
                    }
                } catch (InterruptedException e) {
                } finally {
                    this.latch.countDown();
                }
            }
        }
    
        public static void main(String...args) throws InterruptedException{
            int workerNum = 100;
            CountDownLatch latch = new CountDownLatch(workerNum);
            for(int i=0; i<workerNum; i++) {
                Worker worker =new Worker(latch);
                worker.start();
            }
            latch.await();
            System.out.println("collect worker result");
        }
    }
    ```

    