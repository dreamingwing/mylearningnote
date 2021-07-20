## CopyOnWriteArrayList

使用了写时复制机制、线程安全的ArrayList。用法和ArrayList一样。

+ 存储结构

  ```java
  private transient volatile Object[] array;
  ```

  本质上还是使用了Object数组来存储内容。

+ 写时复制

  写时复制机制是指所有的写入过程中，都会原子性的创建一个新的数组，把就得数组复制到新的数组当中，然后再对新数组进行需要的操作，然后新数组的引用返回。读操作，都是获得当前数组的引用，然后读取，等于读取一个快照里面的内容。

  + 写入

    整个写操作都被包裹在锁里面

    ```java
    public boolean add(E e) {
            final ReentrantLock lock = this.lock;
            lock.lock();
            try {
                Object[] elements = getArray();
                int len = elements.length;
                Object[] newElements = Arrays.copyOf(elements, len + 1);
                newElements[len] = e;
                setArray(newElements);
                return true;
            } finally {
                lock.unlock();
            }
        }
    ```

  + 读取

    所有的读取操作，都是先获得当前的数组的引用，然后通过这个快照来读取。

+ 特点

  + 线程安全
  + 原子方式支持一些复合操作
    + addIfAbsent
    + addAllIfAbsent
  + 写性能较差，因为都用了锁，读性能好，适用于大量并发读少量写的情况

