## TreeMap

TreeMap存的键是有序的(也会对键进行排重)，对象之前的比较是TreeMap里面的comparator。如果没提供comparator，就需要key实现了Comparable接口。TreeMap实现的基础是红黑树。

```java
private final Comparator<? super K> comparator;
```



+ 存储结构

  每个键值对的存储结构如下:

  ```java
  static final class Entry<K,V> implements Map.Entry<K,V> {
          K key;	//键
          V value; //值
          Entry<K,V> left; //指向左子树
          Entry<K,V> right; //指向右子树
          Entry<K,V> parent; //指向父节点
          boolean color = BLACK; //是否黑节点
      //省略了方法
      }
  ```

  

+ 添加节点

  ```java
  public V put(K key, V value) {
          Entry<K,V> t = root;
          if (t == null) {
              compare(key, key); // type (and possibly null) check
  
              root = new Entry<>(key, value, null);
              size = 1;
              modCount++;
              return null;
          }//到这里为止，是添加第一个节点，compare方法的调用，本质是为了做null check.
          int cmp;
          Entry<K,V> parent;
          // split comparator and comparable paths
          Comparator<? super K> cpr = comparator;
          if (cpr != null) {
              do {
                  parent = t;
                  cmp = cpr.compare(key, t.key);
                  if (cmp < 0)
                      t = t.left;
                  else if (cmp > 0)
                      t = t.right;
                  else
                      return t.setValue(value);
              } while (t != null);
          }
          else {
              if (key == null)
                  throw new NullPointerException();
              @SuppressWarnings("unchecked")
                  Comparable<? super K> k = (Comparable<? super K>) key;
              do {
                  parent = t;
                  cmp = k.compareTo(t.key);
                  if (cmp < 0)
                      t = t.left;
                  else if (cmp > 0)
                      t = t.right;
                  else
                      return t.setValue(value);
              } while (t != null);
          }//以上的if else都是通过循环超找到父节点
          Entry<K,V> e = new Entry<>(key, value, parent);
          if (cmp < 0)
              parent.left = e;
          else
              parent.right = e;
          fixAfterInsertion(e);//调整树的结构，让这棵树大致平衡
          size++;
          modCount++;
          return null;
      }
  ```

  

+ 获取节点&&查询节点

  本质都是通过树的遍历来查找节点

+ 特点

  1. 按键有序
  2. 需要键实现Comparable接口或者提供Comparator
  3. 根据键保存、查找、删除的效率较高未O(h)，h是树的高度。