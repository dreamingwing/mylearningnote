## 方法调用

方法调用分为两大类

1. 解析(resolution)

   这个解析就是在类加载过程中，能够成功的把所有的符号引用转化为直接引用的方法。所以，这个就体现出编译期可知，运行期不可变。主要包括静态方法和私有方法、final方法、构造方法、父类方法。

2. 分派(dispatch)

   1. 静态分派(static dispatch，也有被称为Method Overload Resolution)

      静态分派最典型的应用表现就是方法重载(overload)。

      ```java
      Human man = new Man()
      ```

      在上面的代码中Human是静态类型(static type)，而Man是实际类型(actual type)。在运行过程中，静态类型虽然是会变，但是都能在编译期间就能确定。

      ```java
      Human man = new Man(); //静态类型为Human
      sayHello((Man)man);//静态类型为Man
      ```

      当出现重载的时候，虚拟机是通过参数数量以及参数类型来确定执行哪个版本。虚拟机(更准确的说是编译器)是通过静态类型作为判断的依据。所以，静态分派在编译期就确定了是执行哪个版本。

   2. 动态分派(dynamic dispatch)

      方法重写(override)是动态分派的一个体现，如下

      ```java
      public Class Father{
          public void sayHello() {
              System.out.println("Father say hello");
          }
      }
      
      public Class Son extends Father{
          @Override
          public void sayHello() {
              System.out.println("Son say hello");
          }
      }
      
      Father father = new Father();
      Father son = new Son();
      father.sayHello();
      son.sayHello();
      ```

      结果是

      Father say hello

      So say hello

      动态分配可以理解为基于实际类型来确定调用哪个方法。这个核心原理是因为使用invokevirtual指令。

      invokevirtual首先会根据实际类型来寻找调用的方法，如果能找到就进行调用，如果找不到向上寻找父类是否有这个方法。

      

      

      