## Serial收集器(young generation)

单线程收集器，采用标记-复制算法，在进行垃圾收集的时候，回stop the world。适用于客户端程序。

## ParNew收集器(young generation)

多线程并行版本的serial收集器，一般都和cms搭配使用

## Parallel Scavenge收集器(young generation)

使用标记-复制算法。和ParNew非常相识，但是paralle scavenge收集器的目标是吞吐量优先。

## Serial Old收集器(old generation)

是Serial收集器的老年代版本。使用标记-整理算法。

## Parallel Old收集器(old generation)

Paralle Scavenge的老年代版本，使用标记-整理算法，和Paralle Scavenge配合使用，提高吞吐量。

## CMS收集器(old generation)

全名是concurrent mark sweep。这个收集器的目标是为了获得最短的回收停顿时间。适合响应速度比较快的系统。

分为4个步骤

1. 初始标记(CMS initial mark)

   仅仅标记和GC ROOTS直接相连的对象，这个过程会stop the world。

2. 并发标记(CMS concurrent mark)

   通过阶段1标记的对象，开始遍历整个对象图。这个过程和用户线程同时进行。

3. 重新标记(CMS  remark)

   修正上个阶段中，用户线程改变的对象。这个过程也会stop the world，时间比阶段1要长点

4. 并发清除(CMS concurrent sweep)

   并发的清除标记的对象。

缺点：

1. 和用户线程并发运行，会占用机器资源。
2. 由于回收过程是用户线程并发的，所以需要预留部分内存，让用户现在在回收阶段也能正常的创建对象。这个阶段如果内存预留不住，就回导致''并发失败''(concurrent mode failure)。这个时候，需要使用serial old来进行补救，从而导致了更长的停掉时间。
3. 用于使用标记-清除算法，可能导致大量的碎片空间而促发full gc.

## Garbage First

G1垃圾收集器，会把堆分成大小相等的region。在不通的阶段，region会扮演eden区，survivor区或者说老年代。每次垃圾收集的是，G1垃圾收集器会优先回收价值最高的region。









