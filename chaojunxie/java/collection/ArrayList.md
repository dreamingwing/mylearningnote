## ArrayList

+ 存储结构（默认大小为0  添加第一个元素时给了个默认大小10）

  ```java
  transient Object[] elementData; //实际存储元素的数组
  private int size;//包含了多少个元素
  ```

  

+ 扩容策略

  ```java
  private void grow(int minCapacity) {
          int oldCapacity = elementData.length;
          int newCapacity = oldCapacity + (oldCapacity >> 1); //右移一位等于除以二，所以是1.5倍扩容
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;//扩容后，还是小于需要的长度，就按照需要的长度来扩容
          if (newCapacity - MAX_ARRAY_SIZE > 0) //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
              newCapacity = hugeCapacity(minCapacity);//这里的hugeCapacity方法是为了防止整数溢出
          // minCapacity is usually close to size, so this is a win:
          elementData = Arrays.copyOf(elementData, newCapacity);//通过复制到一个新的数组
      }
  ```

  

+ 迭代

  1. 增强for循，本质是被编译成使用iterator（迭代删除会报错 java.util.ConcurrentModificationException）
  2. 通过listIterator()方法可以获得一个能向前向后循环的ListIterator

+ 特点

  1. 随机访问，效率为O(1)，因为是访问数组
  2. 根据内容查询效率较低，为O(N)，因为是遍历查找
  3. 插入和删除效率较低，为O(N)，因为需要移动元素
  4. 线程不安全

  java关于list集合做删除操作时的坑

  1、普通for循环删除重复数据，出现删除不干净的问题

  ​    解决方案：访问list从后往前，从i=size-1至i=0，可以避免上述问题；使用迭代器

  当执行remove方法时，将当前位置赋值给游标cursor，再进行next时，还是获取当前位置的元素，也就是下一个位置移动过来的元素。这样就避免的遗漏元素。关键点是删除时，当前位置是i，游标的位置是i+1，修改游标的位置为i。

  

  