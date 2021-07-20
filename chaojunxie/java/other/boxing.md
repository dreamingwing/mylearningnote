## 包装类(boxing->auto boxing)

+ 包装类创建

  1. 通过valueOf()方法构建，然后通过xxxValue()方法获取对应的基本类型
  2. 通过new方法

  一般都是使用valueOf()方法，new方法每次都会创建一个新对象，除了Float和Double以外的包装类，都会缓存包装类对象。

+ 包装类重写Object类的方法

  1. equals方法

     Object中的equals方法，比较的是两个变量是否指向同一个对象。包装类重写了equals方法，比较的是对应的基本类型的值。下面是Long的equals：

     ```java
     public boolean equals(Object obj) {
             if (obj instanceof Long) {
                 return value == ((Long)obj).longValue();
             }
             return false;
      }
     ```

  2. hashCode方法

  3. toString方法

+ 不可变性

  包装类都是不可变类(线程安全的原因)

  1. 都声明为final
  2. value也都被设置为final
  3. 没有定义setter方法

+ 
