## 单例

+ DCL模式

  ```java
  public class Singleton {
  
      private volatile static Singleton instance;
  
      private Singleton() {
          //do nothing
      }
  
      public static Singleton getInstance() {
          if(instance==null) {
              synchronized (Singleton.class) {
                  if(instance==null) {
                      instance = new Singleton();
                  }
              }
          }
          return instance;
      }
  
      public static void main(String...args) {
          Singleton singleton = Singleton.getInstance();
      }
  
  }
  ```

  

+ 