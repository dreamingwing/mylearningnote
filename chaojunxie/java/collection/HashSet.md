## HashSet

+ 存储结构

  ```java
  private transient HashMap<E,Object> map;
  private static final Object PRESENT = new Object();//HashSet是基于HashMap来实现的，传入的值都放在map字段的key里面，value都用这个PRESENT
  ```

  基本上所有的操作都是借助于HashMap

+ 特点

  1. 没有重复元素
  2. 无序
  3. 删除，添加，判断元素存在效率都是O(1)