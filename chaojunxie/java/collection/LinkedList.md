## LinkedList

+ 存储结构

  内部是通过一个双向队列来存放元素。

  ```java
  	transient int size = 0;//长度
      transient Node<E> first;//指向第一个节点
      transient Node<E> last;//指向最后一个节点
  	private static class Node<E> {
          E item; //存放一个元素
          Node<E> next; //指向之前的节点
          Node<E> prev; //指向之后的节点
  
          Node(Node<E> prev, E element, Node<E> next) {
              this.item = element;
              this.next = next;
              this.prev = prev;
          }
      }
  ```

  

+ 实现接口

  实现了Deque，Queue，所以可以用来作为栈(通过Deque实现)和队列(通过Queue来实现)来使用

+ 根据索引查找元素

  查找的核心方法如下。这个查找，使用了一个类似二分法查找的方式，小于长度的一半就重头开始向后查，否则就重后向前查。

  ```java
  Node<E> node(int index) {
     		if (index < (size >> 1)) {
              Node<E> x = first;
              for (int i = 0; i < index; i++)
                  x = x.next;
              return x;
          } else {
              Node<E> x = last;
              for (int i = size - 1; i > index; i--)
                  x = x.prev;
              return x;
          }
      }
  ```

  

+ 插入

  插入方法可以分为两种，第一种是在链表的末尾插入，第二种是指定插入为止插入。对于第二种情况，首先通过指定的index查找到需要插入到哪个元素之前，由于每个node指向了之前和之后的一个node，所以通过两个node的前后指针就能快速的插入。

+ 删除

  删除方法可以分为两类

  1. 根据index删除

     先调用node方法查好到要删除的节点，然后调用unlink方法删除

  2. 感觉元素内容删除

     遍历查找到要被删除的节点，然后调用unlink方法删除

+ 特点

  1. 按需分配空间
  2. 不能随机访问，效率为O(n/2)
  3. 根据内容查找，效率较低，为O(n)
  4. 两端插入删除效率高，为O(1)
  5. 中间插入删除元素效率较低