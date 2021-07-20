# JVM参数调优

堆的参数配置
-XX:+PrintGC 每次触发GC的时候打印相关日志
-XX:+UseSerialGC 串行回收
-XX:+PrintGCDetails 更详细的GC日志
-Xms 堆初始值
-Xmx 堆最大可用值
-Xmn 新生代堆最大可用值
-XX:SurvivorRatio 用来设置新生代中eden空间和from/to空间的比例.
含以-XX:SurvivorRatio=eden/from=den/to
总结:在实际工作中，我们可以直接将初始的堆大小与最大堆大小相等，
这样的好处是可以减少程序运行时垃圾回收次数，从而提高效率。
-XX:SurvivorRatio 用来设置新生代中eden空间和from/to空间的比例.

设置最大堆内存
参数: -Xms5m -Xmx20m -XX:+PrintGCDetails -XX:+UseSerialGC -XX:+PrintCommandLineFlags

设置新生代与老年代优化参数
-Xmn 新生代大小，一般设为整个堆的1/3到1/4左右
-XX:SurvivorRatio 设置新生代中eden区和from/to空间的比例关系n/1

设置新生代比例参数
参数: -Xms20m -Xmx20m -Xmn1m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC

设置新生与老年代代参数
-Xms20m -Xmx20m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC
-Xms20m -Xmx20m -XX:SurvivorRatio=2 -XX:+PrintGCDetails -XX:+UseSerialGC
-XX:NewRatio=2
总结:不同的堆分布情况，对系统执行会产生一定的影响，在实际工作中，应该根据系统的特点做出合理的配置，基本策略：尽可能将对象预留在新生代，减少老年代的GC次数。除了可以设置新生代的绝对大小(-Xmn),可以使用(-XX:NewRatio)设置新生代和老年代的比例:-XX:NewRatio=老年代/新生代

错误原因: java.lang.OutOfMemoryError: Java heap space
解决办法:设置堆内存大小 -Xms1m -Xmx70m -XX:+HeapDumpOnOutOfMemoryError

设置栈内存大小

错误原因: java.lang.StackOverflowError
栈溢出 产生于递归调用，循环遍历是不会的，但是循环方法里面产生递归调用， 也会发生栈溢出。
解决办法:设置线程最大调用深度
-Xss5m 设置最大调用深度

Tomcat内存溢出在catalina.sh 修改JVM堆内存大小
JAVA_OPTS="-server -Xms800m -Xmx800m -XX:PermSize=256m -XX:MaxPermSize=512m -XX:MaxNewSize=512m"

## JVM参数调优总结

在JVM启动参数中，可以设置跟内存、垃圾回收相关的一些参数设置，默认情况不做任何设置JVM会工作的很好，但对一些配置很好的Server和具体的应用必须仔细调优才能获得最佳性能。通过设置我们希望达到一些目标：

GC的时间足够的小
GC的次数足够的少
发生Full GC的周期足够的长
前两个目前是相悖的，要想GC时间小必须要一个更小的堆，要保证GC次数足够少，必须保证一个更大的堆，我们只能取其平衡。

针对JVM堆的设置，一般可以通过-Xms -Xmx限定其最小、最大值，为了防止垃圾收集器在最小、最大之间收缩堆而产生额外的时间，我们通常把最大、最小设置为相同的值
年轻代和年老代将根据默认的比例（1：2）分配堆内存，可以通过调整二者之间的比率NewRadio来调整二者之间的大小，也可以针对回收代，比如年轻代，通过 -XX:newSize -XX:MaxNewSize来设置其绝对大小。同样，为了防止年轻代的堆收缩，我们通常会把-XX:newSize -XX:MaxNewSize设置为同样大小
年轻代和年老代设置多大才算合理？这个我问题毫无疑问是没有答案的，否则也就不会有调优。我们观察一下二者大小变化有哪些影响
 更大的年轻代必然导致更小的年老代，大的年轻代会延长普通GC的周期，但会增加每次GC的时间；小的年老代会导致更频繁的Full GC
 更小的年轻代必然导致更大年老代，小的年轻代会导致普通GC很频繁，但每次的GC时间会更短；大的年老代会减少Full GC的频率
 如何选择应该依赖应用程序对象生命周期的分布情况：如果应用存在大量的临时对象，应该选择更大的年轻代；如果存在相对较多的持久对象，年老代应该适当增大。
但很多应用都没有这样明显的特性，在抉择时应该根据以下两点：（A）本着Full GC尽量少的原则，让年老代尽量缓存常用对象，JVM的默认比例1：2也是这个道理 （B）通过观察应用一段时间，看其他在峰值时年老代会占多少内存，在不影响Full GC的前提下，根据实际情况加大年轻代，比如可以把比例控制在1：1。但应该给年老代至少预留1/3的增长空间
