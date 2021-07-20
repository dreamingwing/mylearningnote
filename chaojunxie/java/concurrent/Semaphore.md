## Semaphore

Semaphore主要用来限制资源的获取次数，达到限制次数以后，再获取会失败。而且Semaphore是不可重入的，也就是每次获取都会扣减限制次数。

+ 例子

  ```java
  import java.util.concurrent.Semaphore;
  
  public class AccessControlService {
  
      public static class ConcurrentLimitException extends RuntimeException {
          private static final long serialVersionUID = 1l;
      }
  
      private static final int MAX_PERMITS = 100;
      private Semaphore permits = new Semaphore(MAX_PERMITS);
  
      public boolean login(String name, String password) {
          if(!permits.tryAcquire()) {
              throw new ConcurrentLimitException();
          }
          //其他验证内容
          return true;
      }
  
      public void logout(String name) {
          permits.release();
      }
  }
  
  ```

  