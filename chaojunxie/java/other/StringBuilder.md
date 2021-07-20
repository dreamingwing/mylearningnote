## StringBuilder

StringBuilder和StringBuffer用法基本是一致的，但是StringBuilder是线程不安全，StringBuffer是线程安全的。现在安全的，必然是性能相对差点。

+ 内部存储结构

  和String类似，StringBuilder使用了字符数组来存储内容，这个数组并非final，所以是可以改变的。新建一个StringBuilder的时候，回初始化这个字符数组，默认长度为16位。当插入新的内容到数组里面的时候，数组长度不够，就会进行扩容，扩容的算法是当前长度的两倍+2(这样是为了保证当前长度为0的时候都能正常扩容)，然后通过Arrays.copyOf复制本来的数组到新的数组当中去。

+ toString方法

  每次调用toString都会返回一个新的字符对象。

  代码如下：

  ```java 
  public String toString() {
      // Create a copy, don't share the array
      return new String(value, 0, count);
  }
  ```

+ String类的+方法

  编译器会把String操作编译成为StringBuilder的append。