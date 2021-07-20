## ArrayDeque

ArrayDeque是基于数组来实现的双向队列。 (默认大小也是16)

+ 存储结构

  ```java
  transient Object[] elements; //实际存储元素的数组
  transient int head;
  transient int tail;//head,和tail是的这个数组在逻辑上构成了循环数组
  ```

+ 循环数组

|   Head & tail大小   |     首元素     |           尾元素            |   长度    |               索引范围               |
| :-----------------: | :------------: | :-------------------------: | :-------: | :----------------------------------: |
|      head=tail      |                |                             |     0     |                                      |
|      tail>head      | elements[head] |      elements[tail-1]       | tail-head |             head->tail-1             |
| tail<head && tail=0 | elements[head] | elements[elements.length-1] |           |       head->elements.length-1        |
| tail<head && tail>0 | elements[head] |      elements[tail-1]       |           | head->elements.length-1 && 0->tail-1 |

+ 添加元素

  核心代码如下，

  ```java
  public void addLast(E e) {
          if (e == null)
              throw new NullPointerException();
          elements[tail] = e;//插入的tail指向的位置
          if ( (tail = (tail + 1) & (elements.length - 1)) == head)//更新tail，然后判断是否需要扩容
              doubleCapacity();//扩容
      }
  ```

  

+ 特点

  1. 

