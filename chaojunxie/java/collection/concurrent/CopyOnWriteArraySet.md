## CopyOnWriteArraySet

线程安全，用法和一般的Set一样

+ 存储结构

  ```java
  private final CopyOnWriteArrayList<E> al;
  ```

  本质上就是通过一个CopyOnWriteArrayList来实现所有的操作

+ 写入

  ```java
  public boolean add(E e) {
          return al.addIfAbsent(e);
  }
  ```

+ 特性和CopyOnWriteArraySet类似

