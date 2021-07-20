## String 类

+ 存储结构

  String类是通过以下的字符数组来存储。

  ```java
  private final char value[];
  ```

  String类中的大量方法，都是通过对这个字符数组的操作来实现的。

+ 不可变性

  String类定义为final，而且其中实际存储数据的是个final修饰的char数组。String类中的任何操作，本质都是产生一个新的字符数组，然后value指向这个新的字符数组。

+ 常量字符串

  ```java
  System.out.println("prepare".length()); 
  ```

  这里的"prepare"实际是个字符串常量，被放在常量池里面，所以以下代码为true，因为name1和name2都指向了同一个常量

  ```java
  String name1 = "test";
  String name2 = "test";
  System.out.println(name1=name2);
  ```

  一旦涉及到到new的，就不是true了，因为不可变性的原因，下面结果为false，虽然他们的value是指向同一个char数组，但是name1和name2分别指向两个不同的变量。

  ```java
  String name1 = new String("test");
  String name2 = new String("test");
  System.out.println(name1=name2);
  ```

+ 