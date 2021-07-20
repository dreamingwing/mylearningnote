## HashMap

+ 存储结构

  使用了数组-链表-红黑树的结构来存放键值对

  ```java
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //默认初始化数组大小，16
  static final int MAXIMUM_CAPACITY = 1 << 30;
  static final float DEFAULT_LOAD_FACTOR = 0.75f;//默认负载因子
  static final int TREEIFY_THRESHOLD = 8; //链表转换成红黑树阈值
  static final int UNTREEIFY_THRESHOLD = 6;//红黑树转换成链表阈值
  int threshold; // capacity * load factor 扩容的阈值
  transient int size; //键值对数量
  final float loadFactor; //自定义的负载均衡因子
  transient Node<K,V>[] table; //实际存放键值对的数组，这个数组的长度就是capacity，都是2的n次方
  ```

+ 哈希数组索引位置

  包括了两个步骤

  1. 通过key的对象的hashCode计算到一个整数

     ```java
     static final int hash(Object key) {
             int h;
             return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
     }
     ```

  2. 然后用这个整数对table.length-1进行取模

     ```java
     h & (length-1)
     ```

     这样的原因是，tabble.length都是2的n次方，h&(length-1)等价于h%length，但是&运算的效率更高

+ 写入过程

  写入的过程，核心在putVal方法当中

  ```java
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict)
  ```

  这个方法的整体流程如下

  1. 当table为空或者长度为0，调用resize方法初始化table
  2. 对key进行哈希，查找table里面是否包含这个key，不存在则创建一个node，插入到table对应位置
  3. 如果存在，查看table对应的位置的key是否和要加入的key一样，一样就进行替换
  4. 如果不一样，就查看这个结点是不是已经变成树了，如果已经变成树，就调用putTreeVal往树添加这个结点
  5. 如果不是树，就证明还是链表状态，遍历遍历链表匹配key，匹配成功就进行替换，匹配失败就往链表通过尾插法进行插入，然后查看这个查看这个链表的长度是否超过TREEIFY_THRESHOLD，如果超过，就要把这个链表转化成红黑树
  6. size+1，然后size和threshold比较，当size大于threshold就回触发扩容

+ Hash公式

  使用了位运算，但是本质时

  ```java
  index = HashCode（Key） & （Length - 1）
  ```

  所以扩容以后，需要进行重哈希，因为table的长度改变了

+ 扩容策略

  扩容的核心是调用了

  ```java
  final Node<K,V>[] resize()
  ```

  整体流程如下

  1. 计算新的数组长度，是原来数组长度的两倍
  2. 使用新的数组长度创建一个新的Node数组
  3. 把旧的数组里面的结点插入到新的数组中，每个结点都需要重新计算在新的数组的哪个桶里面，因为数组长度变了

+ 头插法 vs 尾插法

  插入链表的时候，jdk1.8之前使用的是头插法，1.8使用的是尾插法。

  头插入带来的问题主要是在扩容的时候，复制结点到新的数组的时候，在多线程的情况下，如果链表里面的结点还是被复制到同一个链表的话，可能出现循环链表而导致扩容失败。

  尾插法会保证了本来的顺序，所以就不会出现循环链表的问题。

  ~~~java
  
  void transfer(Entry[] newTable)
  {
      Entry[] src = table;
      int newCapacity = newTable.length;
      //下面这段代码的意思是：
      //  从OldTable里摘一个元素出来，然后放到NewTable中
      for (int j = 0; j < src.length; j++) {
          Entry<K,V> e = src[j];
          if (e != null) {
              src[j] = null;
              do {
                  Entry<K,V> next = e.next;
                  int i = indexFor(e.hash, newCapacity);
                  e.next = newTable[i];
                  newTable[i] = e;
                  e = next;
              } while (e != null);
          }
      }
  }
  ~~~

  

+ 特点

  1. 线程不安全
  2. 键值对没有顺序
  3. 查找效率高 

+ Collectors.toMap null value坑

  参考：<https://www.jianshu.com/p/aeb21dea87e0>

  核心原因是调用了HashMap里面的merge方法，而在merge方法当中，会对value做null check。

+ 