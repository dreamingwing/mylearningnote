## volatile关键字

这个关键字是为了解决两个问题

1. 内存可见性

   因为JMM，线程之间共享的一些变量是存在可见性问题的。volatile关键字会强制线程对这样的变量操作的时候，都必须同步到内存。这样就能解决内存可见性的问题。但是，volatile不能解决原子性问题。

2. 防止指令重排

   JVM执行指令的时候回对指令之间的顺序做一些优化，让执行效率更高，这句叫做指令重排。但是有的是，会带来一些问题。

   ```java
   public class Singleton {
       private static Singleton instance;
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
   ```

   上面的这个DCL单例模式，instance对象在创建的过程会大致经历三个步骤

   1. 分配内存空间
   2. 调用初始化方法
   3. 返回对象的引用给调用方

   这样如果出现了指令重排，就回出现问题。需要volatile 来修饰instance对象，来防止指令重排。

### synchronized和volatile对比

Synchronized->保证可见性(获取锁以后，会清空工作内存，重新从主存读取，释放所得时候，会强制刷入到主存)，保证原子性

volatile->保证可见性，防止指令重排