## ConcurrentHashMap(对应HashMap)

+ 存储结构

  和HashMap一样，ConcurrentHashMap使用的也是数组+链表+红黑树这样的结构来存放数据

  ```java
  private static final int MAXIMUM_CAPACITY = 1 << 30;
  private static final int DEFAULT_CAPACITY = 16;
  static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
  private static final int DEFAULT_CONCURRENCY_LEVEL = 16;
  private static final float LOAD_FACTOR = 0.75f;
  static final int TREEIFY_THRESHOLD = 8;
  static final int UNTREEIFY_THRESHOLD = 6;
  static final int MIN_TREEIFY_CAPACITY = 64;
  private static final int MIN_TRANSFER_STRIDE = 16;
  private static int RESIZE_STAMP_BITS = 16;
  private static final int RESIZE_STAMP_SHIFT = 32 - RESIZE_STAMP_BITS;
  static final int MOVED     = -1; // hash for forwarding nodes
  static final int TREEBIN   = -2; // hash for roots of trees
  static final int RESERVED  = -3; // hash for transient reservations
  static final int HASH_BITS = 0x7fffffff; // usable bits of normal node hash
  static final int NCPU = Runtime.getRuntime().availableProcessors();
  transient volatile Node<K,V>[] table;//实际存放数据节点的数组
  private transient volatile Node<K,V>[] nextTable;
  ```

+ 读写过程

  + 写入
    1. 根据 key 计算出 hashcode 。
    2. 判断是否需要进行初始化。
    3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功。
    4. 如果当前位置的 `hashcode == MOVED == -1`,则需要进行扩容。
    5. 如果都不满足，则利用 synchronized 锁写入数据。
    6. 如果数量大于 `TREEIFY_THRESHOLD` 则要转换为红黑树。
  + 读取

+ 特点

  + 线程安全
  + 支持一些原子复合操作
  + 支持高并发，读操作完全并行，写操作支持一定程度的并行
  + 迭代不用加锁
  + 弱一致性