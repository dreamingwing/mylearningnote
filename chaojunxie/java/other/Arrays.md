## Arrays类

Arrays类中包含了一系列对数组操作的静态方法。

+ toString方法，会把参数里面的数组转换成一个字字符串数组输出

  ```java
  String[] strArr = {"hello", "world"};
  System.out.println(strArr);//输出的是数组的地址
  System.out.println(Arrays.toString(strArr));//输出形式 [hello, world]
  ```

  

+ sort方法，对传入的数组进行排序，也能接受一个comparator

+ binarySearch方法，对传入的数组(需要已经有序的)进行二分查找

+ copyOf方法

+ equas方法，判断两个数组是否相等(长度一样，每个元素的equals方法为true)