---
layout: post
title: GC
date: 2019-01-02
---

# 目录
<!-- toc -->

# 概念解析

### 元空间

-XX:MetaspaceSize=100m

随着JDK8的到来，JVM不再有PermGen。但类的元数据信息（metadata）还在，只不过不再是存储在连续的堆空间上，而是移动到叫做“Metaspace”的本地内存（Native memory）中。

元空间的本质和永久代类似，都是对JVM规范中方法区的实现。不过元空间与永久代之间最大的区别在于：元空间并不在虚拟机中，而是使用本地内存。因此，默认情况下，元空间的大小仅受本地内存限制，但可以通过以下参数来指定元空间的大小： 

• -XX:MetaspaceSize，初始空间大小，达到该值就会触发垃圾收集进行类型卸载，同时GC会对该值进行调整：如果释放了大量的空间，就适当降低该值；如果释放了很少的空间，那么在不超过MaxMetaspaceSize时，适当提高该值。 
• -XX:MaxMetaspaceSize，最大空间，默认是没有限制的。 

除了上面两个指定大小的选项以外，还有两个与 GC 相关的属性： 

• -XX:MinMetaspaceFreeRatio，在GC之后，最小的Metaspace剩余空间容量的百分比，减少为分配空间所导致的垃圾收集 
• -XX:MaxMetaspaceFreeRatio，在GC之后，最大的Metaspace剩余空间容量的百分比，减少为释放空间所导致的垃圾收集

-verbose参数是为了获取类型加载和卸载的信息

通过GC日志可以看到，old区离最大配置还很远，Metaspace区并没有真正释放空间，所以怀疑是Metaspace区不够用了。

以前只认为，Metaspace区是保存在本地内存中，是没有上限的，经查阅资料才发现，原来JDK8中，XX:MaxMetaspaceSize确实是没有上限的，最大容量与机器的内存有关；但是XX:MetaspaceSize是有一个默认值的：21M。问题就出在这里。 

### 压缩的类空间

Compressed Class space is part of the metaspace.

### Eden Space（heap区）

字面意思是伊甸园，对象被创建的时候首先放到这个区域，进行垃圾回收后，不能被回收的对象被放入到空的survivor区域。

### Young GC

当Eden区的空间占满之后，会触发Young GC，G1将Eden和Survivor中存活的对象拷贝到Survivor，或者直接晋升到Old Region中。Young GC的执行是多线程并发的，期间会停顿所有的用户线程（STW）。

### Survivor Space（heap区）

幸存者区，用于保存在eden space内存区域中经过垃圾回收后没有被回收的对象。Survivor有两个，分别为To Survivor、 From Survivor，这个两个区域的空间大小是一样的。执行垃圾回收的时候Eden区域不能被回收的对象被放入到空的survivor（也就是To Survivor，同时Eden区域的内存会在垃圾回收的过程中全部释放），另一个survivor（即From Survivor）里不能被回收的对象也会被放入这个survivor（即To Survivor），然后To Survivor 和 From Survivor的标记会互换，始终保证一个survivor是空的。

Eden Space和Survivor Space都属于新生代，新生代中执行的垃圾回收被称之为Minor GC（因为是对新生代进行垃圾回收，所以又被称为Young GC），每一次Young GC后留下来的对象age加1。

### Old Gen（heap区）

老年代，用于存放新生代中经过多次垃圾回收仍然存活的对象，也有可能是新生代分配不了内存的大对象会直接进入老年代。经过多次垃圾回收都没有被回收的对象，这些对象的年代已经足够old了，就会放入到老年代。

当老年代被放满的之后，虚拟机会进行垃圾回收，称之为Major GC。由于Major GC除并发GC外均需对整个堆进行扫描和回收，因此又称为Full GC。

### ZGC

ZGC来了 !!! Java程序员可以光荣的远离讨厌的GC停顿和调优了。ZGC的成绩是，无论你开了多大的堆内存(1288G？ 2T？)，硬是能保证低于10毫秒的JVM停顿。



# 参数解析

打印 GC 参数：

-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime -XX:+PrintSafepointStatistics -XX:PrintSafepointStatisticsCount=1

-XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC

### -XX:ParallelGCThreads=n

设置 STW 工作线程数的值。将 n 的值设置为逻辑处理器的数量。n 的值与逻辑处理器的数量相同，最多为 8。如果逻辑处理器不止八个，则将 n 的值设置为逻辑处理器数的 5/8 左右。这适用于大多数情况，除非是较大的 SPARC 系统，其中 n 的值可以是逻辑处理器数的 5/16 左右。

### -XX:ConcGCThreads=n

设置并行标记的线程数。将 n 设置为并行垃圾回收线程数 (ParallelGCThreads) 的 1/4 左右。


# Q&A

Q: 什么时候发生 YGC 呢？
A: 当 Eden 不够放入新创建的对象时，也就是Eden 区满了，JVM 就会清理Eden 区的空间，将存活的对象放入 to 区，如果 to 区放不下，则直接进入老年代。

# 参考资料

[JVM G1 垃圾回收器总结](http://www.leonlu.cc/profession/24-jvm-g1-gc/)
[G1 Garbage Collector is mature in Java 9, finally](http://blog.mgm-tp.com/2018/01/g1-mature-in-java9/)
[Make G1 the Default Garbage Collector](https://segmentfault.com/a/1190000013615459)
[Metaspace 之一: Metaspace整体介绍 (永久代被替换原因、元空间特点、元空间内存查看分析方法)](https://www.cnblogs.com/duanxz/p/3520829.html)
[深入浅出 JVM GC (2)](https://www.cnblogs.com/stateis0/p/9062188.html)