## 对象是否存活

使用了可达性分析(reachability analysis)算法了判断对象是否存活。这个算法，是通过定义了一系列的gc roots对象作为根对象，然后通过引用关系构建引用链(reference chain)。如果某个对象不属于任何一个引用链，那么这个对象就是不可达的，也就意味着这个对象是不需要存活的。

## 引用分类

1. 强引用(strong reference)
2. 软引用(soft reference)
3. 弱引用(weak reference)
4. 虚引用(phantom reference)

## 垃圾回收的过程

1. 基于GC roots进行可达性分析，标记所有未和gc roots行程应用链的对象
2. 判断被标记对象是否需要调用finalize方法(未覆盖finalize方法，或者finalize方法已经被执行过一次的对象，不需要执行finalize方法)
3. 需要执行finalize方法的的对象，会被放置进FQueue队列当中，一条低优先级的Finalizer线程会在后台逐一执行FQueue队列里对象的finalize方法
4. 垃圾收集器对FQueue队列中的对象进行第二次标记，如果对象在finalize方法中和GC roots连上了，将会被移出即将回收集合
5. 进行垃圾回收

## 分代收集理论

分代手收集理论的假说基础

1. 弱分代假说(weak generation hypothesis)
2. 强分代假说(strong generation hypothesis)
3. 跨代引用假说(intergeneration reference hypothesis)

## 标记-清除算法(mark-sweep)

这个算法分成了两个动作，先标记需要回收的对象，然后收集需要被回收的对象。这个算法有两个问题，1.效率随着对象的增加而降低，2.内存空间碎片化，可能会导致再一次垃圾收集。

## 标记-复制算法(mark-copy)

这个算法将内存区域划分成相等的两个区域，每次只使用其中一个区域，当需要垃圾回收的时候，就将存活的对象复制到另外的一个区域。带来的问题是，会有部分的内存浪费。现在常见的新生代垃圾回收算法，会把新生代分成Eden区和survivor区。对象都被创建在Eden区域，当需要垃圾回收的时候，就把存活的对象，从一个survivor区域以及Eden区复制到另外一个survivor区。

## 标记-整理算法(mark-compact)

这个算法的标记阶段和标记-清除算法是一样的。到了整理阶段，垃圾收集器会把需要存活的对象移动到内存的一端，在把边界外的区域全部清除调。由于移动存活对象需要耗费大量资源，所以会引起虚拟机停顿(stop the world)。这个算法，多用在老年代的回收。

