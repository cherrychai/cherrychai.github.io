---
layout: post
title: Garbage Collection
date: 2019-01-02
---

<!-- toc -->

## GC 迭代

Serial、ParNew、Parallel Scavenge 用于新生代，CMS、Serial Old、Paralled Old 用于老年代。并且他们相互之间以相对固定的组合使用（具体组合关系如上图）。G1是一个独立的收集器不依赖其他6种收集器。ZGC是目前JDK 11的实验收集器。

### Serial 收集器

Serial，是单线程执行垃圾回收的。当需要执行垃圾回收时，程序会暂停一切手上的工作，然后单线程执行垃圾回收。
因为新生代的特点是对象存活率低，所以收集算法用的是复制算法，把新生代存活对象复制到老年代，复制的内容不多，性能较好。

### ParNew 收集器

ParNew 同样用于新生代，是 Serial 的多线程版本，并且在参数、算法（同样是复制算法）上也完全和 Serial 相同。
Par 是 Parallel 的缩写，但它的并行仅仅指的是收集多线程并行，并不是收集和原程序可以并行进行。ParNew 也是需要暂停程序一切的工作，然后多线程执行垃圾回收。

### Parallel Scavenge 收集器 (jdk1.8 默认垃圾收集器)

新生代的收集器，同样用的是复制算法，也是并行多线程收集。与 ParNew 最大的不同，它关注的是垃圾回收的吞吐量。
这里的吞吐量指的是 总时间与垃圾回收时间的比例。这个比例越高，证明垃圾回收占整个程序运行的比例越小。

### Serial Old 收集器

老年代的收集器，与 Serial 一样是单线程，不同的是算法用的是标记-整理（Mark-Compact）。
因为老年代里面对象的存活率高，如果依旧是用复制算法，需要复制的内容较多，性能较差。并且在极端情况下，当存活为 100% 时，没有办法用复制算法。所以需要用 Mark-Compact，以有效地避免这些问题。

### Parallel Old 收集器

老年代的收集器，是Parallel Scavenge老年代的版本。其中的算法替换成Mark-Compact。

### CMS 收集器

CMS，Concurrent Mark Sweep，同样是老年代的收集器。它关注的是垃圾回收最短的停顿时间（低停顿），在老年代并不频繁 GC 的场景下，是比较适用的。

命名中用的是 concurrent，而不是 parallel，说明这个收集器是有与工作执行并发的能力的。MS则说明算法用的是 Mark Sweep 算法。

来看看具体地工作原理。CMS 整个过程比之前的收集器要复杂，整个过程分为四步：

初始标记 (initial mark)：单线程执行，需要『Stop The World』，但仅仅把 GC Roots 的直接关联可达的对象给标记一下，由于直接关联对象比较小，所以这里的速度非常快。
并发标记 (concurrent mark)：对于初始标记过程所标记的初始标记对象，进行并发追踪标记，此时其他线程仍可以继续工作。此处时间较长，但不停顿。
重新标记 (remark)：在并发标记的过程中，由于可能还会产生新的垃圾，所以此时需要重新标记新产生的垃圾。此处执行并行标记，与用户线程不并发，所以依然是『Stop The World』，时间比初始时间要长一点。
并发清除 (concurrent sweep)：并发清除之前所标记的垃圾，其他用户线程仍可以工作，不需要停顿。

由于最耗费时间的并发标记与并发清除阶段都不需要暂停工作，所以整体的回收是低停顿的。由于CMS以上特性，缺点也是比较明显的，

- Mark Sweep 算法会导致内存碎片比较多。
- CMS 的并发能力依赖于 CPU 资源，所以在 CPU 数少和 CPU 资源紧张的情况下，性能较差。
- 并发清除阶段，用户线程依然在运行，所以依然会产生新的垃圾，此阶段的垃圾并不会再本次 GC 中回收，而放到下次。所以 GC 不能等待内存耗尽的时候才进行 GC，这样的话会导致并发清除的时候，用户线程可以了利用的空间不足，所以这里会浪费一些内存空间给用户线程预留。

有人会觉得既然 Mark Sweep 会造成内存碎片，那么为什么不把算法换成 Mark Compact 呢？
答案其实很简答，因为当并发清除的时候，用 Compact 整理内存的话，原来的用户线程使用的内存还怎么用呢？要保证用户线程能继续执行，前提的它运行的资源不受影响嘛。Mark Compact 更适合『Stop The World』这种场景下使用。

### G1 收集器 (jdk1.9 默认垃圾收集器)

开启方式：
```
-XX:+UnlockExperimentalVMOptions -XX:+UseG1GC -Xmx10g -XX:ConcGCThread:4
```

G1，Garbage First，在 JDK 1.7 版本正式启用，是当时最前沿的垃圾收集器。G1 可以说是 CMS 的终极改进版，解决了 CMS 内存碎片、更多的内存空间登问题。虽然流程与 CMS 比较相似，但底层的原理已是完全不同。

- 高效益优先：G1 会预测垃圾回收的停顿时间，原理是计算老年代对象的效益率，优先回收最大效益的对象。
- 堆内存结构的不同：以前的收集器分代是划分新生代、老年代、持久代等，G1 则是把内存分为多个大小相同的区域 Region，每个 Region 拥有各自的分代属性，但这些分代不需要连续。这样的分区可以有效避免内存碎片化问题。但是这样同样会引申一个新的问题，就是分代的内存不连续，导致在GC搜索垃圾对象的时候需要全盘扫描找出引用内存所在。为了解决这个问题，G1对于每个Region都维护一个Remembered Set，用于记录对象引用的情况。当GC发生的时候根据Remembered Set的引用情况去搜索。

两种GC模式：

- Young GC：关注于所有年轻代的 Region，通过控制收集年轻代的 Region 个数，从而控制 GC 的回收时间。
- Mixed GC：关注于所有年轻代的 Region，并且加上通过预测计算最大收益的若干个老年代 Region。

整体的执行流程：

初始标记 (initial mark)：标记了从 GC Root 开始直接关联可达的对象，STW (Stop the World)执行。
并发标记 (concurrent marking)：并发标记初始标记的对象，此时用户线程依然可以执行。
最终标记 (Remark)：标记再并发标记过程中产生的垃圾，『Stop The World』执行
筛选回收 (Live Data Counting And Evacuation)：评估标记垃圾，根据GC模式回收垃圾，『Stop The World』执行。

在 Region 层面上，整体的算法偏向于 Mark-Compact。因为是 Compact，会影响用户线程执行，所以回收阶段需要 STW 执行。

### 令人惊叹的 ZGC

开启方式：
```
-XX:+UnlockExperimentalVMOptions -XX:+UseZGC
```

在 JDK 11 当中，加入了实验性质的 ZGC，它的回收耗时平均不到 2 毫秒，它是一款低停顿高并发的收集器。
ZGC 几乎在所有地方并发执行的，除了初始标记的是 STW 的。所以停顿时间几乎就耗费在初始标记上，这部分的实际是非常少的。那么其他阶段是怎么做到可以并发执行的呢？

ZGC主要新增了两项技术，一个是着色指针 Colored Pointer，另一个是读屏障 Load Barrier。

- 着色指针 Colored Pointer

ZGC 利用指针的 64 位中的几位表示 Finalizable、Remapped、Marked1、Marked0 (ZGC仅支持64位平台)，以标记该指向内存的存储状态，相当于在对象的指针上标注了对象的信息。注意，这里的指针相当于 Java 术语当中的引用。
在这个被指向的内存发生变化的时候 (内存在 Compact 被移动时)，颜色就会发生变化。
在 G1 的时候就说到过，Compact 阶段是需要 STW，否则会影响用户线程执行。那么怎么解决这个问题呢？

- 读屏障 Load Barrier

由于着色指针的存在，在程序运行时访问对象的时候，可以轻易知道对象在内存的存储状态 (通过指针访问对象)，若请求读的内存在被着色了。那么则会触发读屏障。读屏障会更新指针再返回结果，此过程有一定的耗费，从而达到与用户线程并发的效果。
把这两项技术联合下理解，引用 R 大 (RednaxelaFX) 的话

> 与标记对象的传统算法相比，ZGC 在指针上做标记，在访问指针时加入 Load Barrier (读屏障)，比如当对象正被 GC 移动，指针上的颜色就会不对，这个屏障就会先把指针更新为有效地址再返回，也就是，永远只有单个对象读取时有概率被减速，而不存在为了保持应用与 GC 一致而粗暴整体的 Stop The World。

ZGC 虽然目前还在 JDK 11 还在实验阶段，但由于算法与思想是一个非常大的提升，相信在未来不久会成为主流的 GC 收集器使用。

## 基本概念

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



## GC 参数

打印 GC 参数：

-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintTenuringDistribution -XX:+PrintGCApplicationStoppedTime -XX:+PrintSafepointStatistics -XX:PrintSafepointStatisticsCount=1

-XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC

### -XX:ParallelGCThreads=n

设置 STW 工作线程数的值。将 n 的值设置为逻辑处理器的数量。n 的值与逻辑处理器的数量相同，最多为 8。如果逻辑处理器不止八个，则将 n 的值设置为逻辑处理器数的 5/8 左右。这适用于大多数情况，除非是较大的 SPARC 系统，其中 n 的值可以是逻辑处理器数的 5/16 左右。

### -XX:ConcGCThreads=n

设置并行标记的线程数。将 n 设置为并行垃圾回收线程数 (ParallelGCThreads) 的 1/4 左右。


## Q&A

Q: 什么时候发生 YGC 呢？
A: 当 Eden 不够放入新创建的对象时，也就是Eden 区满了，JVM 就会清理Eden 区的空间，将存活的对象放入 to 区，如果 to 区放不下，则直接进入老年代。

## 参考资料

- [JVM G1 垃圾回收器总结](http://www.leonlu.cc/profession/24-jvm-g1-gc/)
- [G1 Garbage Collector is mature in Java 9, finally](http://blog.mgm-tp.com/2018/01/g1-mature-in-java9/)
- [Make G1 the Default Garbage Collector](https://segmentfault.com/a/1190000013615459)
- [Metaspace 之一: Metaspace整体介绍 (永久代被替换原因、元空间特点、元空间内存查看分析方法)](https://www.cnblogs.com/duanxz/p/3520829.html)
- [深入浅出 JVM GC (2)](https://www.cnblogs.com/stateis0/p/9062188.html)
- [一文了解JVM全部垃圾回收器，从Serial到ZGC](https://juejin.im/post/5bade237e51d450ea401fd71)
