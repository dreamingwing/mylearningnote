## 异常体系

所有的异常都继承自Throwable，主要分为两个子类

+ Error

  表示系统的错误，由java系统自己使用，程序不应该处理。例如VirtualMachineError、StackOverflowError等。

+ Exception

  表示应用程序的错误，主要的子类包括了IOException、RuntimeException、SQLException等。其中RuntimeException比较特别，它和Error一样，是属于unchecked exception。Exception的其他子类都是checked exception。Checked exception程序需要定义捕获，不捕获编译会报错。

## finally关键字

finally代码代码块无论是有有异常，都会执行

+ 没有发生异常，try代码块执行完就执行finally代码块
+ 发生了异常，并且被catch，在catch代码块执行后执行finally代码块
+ 发生了异常，没有被catch到，会在异常被抛向上一层之前执行
+ try->catch->finally中，catch不是必要的，这表面了不捕获异常，直接抛向上一层
+ try或者catch中有return，finally会在return之前执行，但是finally无法改变返回值
+ finally中如果有return，函数实际返回的会是finally中的return值
+ finally中的return和抛出的异常，会掩盖try和catch中的return或者抛出的异常

## throws关键字

+ checked exception必须声明
+ 不允许只抛出不声明
+ 允许只声明不抛出，是为了子类重写父类，子类需要抛出更多的错误，因为子类无法抛出父类未声明的错误