## 所有线程共享区域

1. 方法区域(method area)

   用于存放被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码。

   1. 运行时常量池(runtime constant pool)

2. 堆(heap)

   所有的java对象都是存放在堆中。在虚拟机启动的时候，堆就会存在。堆不要求物理内存是连续的。

   垃圾收集算法都是作用在堆，所有也被称为gc堆(garbage collected heap)。现在大多数的垃圾回收算法都是基于分代收集理论设计的，所以能分成以下几个部分

   1. 新生代(young generation)
      1. Eden区(8)
      2. Survivor区(1)
      3. Survivor区(1)
   2. 老年代(old generation)

3. 执行引擎

4. 本地库接口

## 线程独占区域

1. 虚拟机栈(virtual machine stack)

   虚拟机栈的生命周期是和线程一样的。线程每执行一个方法，都会在虚拟机栈里面创建一个对应的栈贞(stack frame)。这个stack frame里面保存了这个方法需要的一系列信息，其中最重要的是局部变量表(local variable table)。

   局部变量表存放了各个在编译器可知道的基本数据类型(boolean,byte,char,short,int,float,long,double)以及reference类型。本地变量表以局部变量槽(variable slot)作为最小的单位。每个slot是可以重用的。如果栈的深度太深，虚拟机栈会抛出StackOverflowError。

2. 本地方法栈(native method stack)

   作用和虚拟机栈非常相识，区别的地方在于，虚拟机栈是执行java方法，而本地方法栈用来执行本地方法。

3. 程序计数器(program counter register)

   每条线程通过修改程序计数器的值来选取下一条指令。由于java的多线程是通过轮训形式(pooling)实现的，所以程序计数器必须是线程独占的。

