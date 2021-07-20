## ThreadLocal

+ 线程本地变量

  每个线程都有一个变量的独有拷贝。

+ 使用场景

  适用于在多个线程共享一个变量，但是每个现在对于这个变量的值是不一样的，就需要使用到ThreadLocal。也可以理解为线程安全的一个手段。

  + Spring 事务会把数据量连接放到ThreadLocal

+ 例子

```java
public class ThreadLocalDemo {
    static ThreadLocal<Integer> local = new ThreadLocal<>();

    public static void main(String...args) throws InterruptedException{
        Thread child = new Thread(){
            @Override
            public void run() {
                System.out.println("child initial: " + local.get());
                local.set(200);
                System.out.println("child final: " + local.get());
            }
        };
        local.set(100);
        child.start();
        child.join();
        System.out.println("main thread final: " + local.get());
    }

}
```

输出的结果是

```java
child initial: null
child final: 200
main thread final: 100
```

通过结果可以看出，线程间ThreadLocal的值不一样。

+ 原理

  本质的原理是每个Thread类里面，都有一个ThreadLocalMap

  ```java
  ThreadLocal.ThreadLocalMap threadLocals = null;
  ```

  ThreadLocalMap本质上是个Map。ThreadLocal设置值的时候，值设到这个map当中，key是ThreadLocal的this，值就是需要设置的值。读出的时候就通过ThreadLocal的this读出。

+ 内存泄露

  ThreadLocalMap用了弱引用来存放ThreadLocal这key，但是value是强引用的。这样就可能出现value永远不会被回收的情况，所以ThreadLocal用完了应该调用一下remove方法。

根据上一节的内存模型图我们可以知道，由于ThreadLocalMap是以弱引用的方式引用着ThreadLocal，换句话说，就是**ThreadLocal是被ThreadLocalMap以弱引用的方式关联着，因此如果ThreadLocal没有被ThreadLocalMap以外的对象引用，则在下一次GC的时候，ThreadLocal实例就会被回收，那么此时ThreadLocalMap里的一组KV的K就是null**了，因此在没有额外操作的情况下，此处的V便不会被外部访问到，而且**只要Thread实例一直存在，Thread实例就强引用着ThreadLocalMap，因此ThreadLocalMap就不会被回收，那么这里K为null的V就一直占用着内存**。

可以看到，在set值的时候，有一定的几率会执行`replaceStaleEntry(key, value, i)`方法，其作用就是将当前的值替换掉以前的key为null的值，重复利用了空间。

如果频繁的在线程中new ThreadLocal对象，在使用结束时，最好调用ThreadLocal.remove来释放其value的引用，避免在ThreadLocal被回收时value无法被访问却又占用着内存