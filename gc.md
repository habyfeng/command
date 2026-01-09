### Java17 GC日志配置
```
-Xlog:gc*:/home/admin/logs/gc-%t.log:time,level,tags:filecount=7,filesize=10m
```

说明：
- gc*：记录所有GC相关日志（如gc+heap、gc+age等标签)
- file=gc.log：输出到文件
- time,level,tags：日志包含时间戳、级别（INFO/WARN）、标签
- filecount=5,filesize=100m：滚动日志配置(保留7个日志文件，每个10MB)

### GC(0) Pause Young (Concurrent Start) (G1 Humongous Allocation)

这条日志表示G1垃圾收集器在执行Young GC（年轻代垃圾收集）时发生了一次暂停。暂停的目的是为了执行垃圾收集操作，清理年轻代中的垃圾对象。

在这个日志中，”(Concurrent Start)” 表示该次垃圾收集是与并发标记阶段同时进行的。G1垃圾收集器采用了分代收集和并发垃圾收集的策略，其中并发标记阶段会与应用程序的执行同时进行，以减少垃圾收集对应用程序的影响。

另外，”(G1 Humongous Allocation)” 表示在这次垃圾收集过程中，G1垃圾收集器还进行了一个巨型对象（Humongous Object）的分配操作。巨型对象是指超过了一定大小阈值的对象，G1垃圾收集器会对其进行特殊处理。

### G1HeapRegionSize

堆内存中一个区域 (Region) 的大小，可以通过 -XX:G1HeapRegionSize 参数指定，大小区间最小 1M 、最大 32M ，总之是 2 的幂次方。

> 说明 ：

    G1中的region的大小，由参数G1HeapRegionSize定义，如果没有定义，就由xms/2048计算region大小，如果小于1就取1，如果大于32就取32，如果是其他值，就取2,4,8,16相近的数值，总之是2的n次方。

    G1中的region的数量不一定是2048，如果内存小于2G，每个region最小为1M，那么数量就小于2048，比如内存超过64g，每个region最大为32M，那么数量也就超过2048，例如，128g，那么region数量就为4096个。

### 巨形对象Humongous Region

存储超过 50% 标准 region 大小的对象称为巨型对象(Humongous Object)。当线程为巨型分配空间时，不能简单在TLAB进行分配，因为巨型对象的移动成本很高，而且有可能一个分区不能容纳巨型对象。因此，巨型对象会直接在老年代分配，所占用的连续空间称为巨型分区(Humongous Region)。


### 优化建议
1. 减少线程栈内存
   
设置更小的线程栈大小（例如 512KB）：
-Xss512k

这可以显著减少线程栈内存占用。

2. 限制直接内存

设置直接内存的上限，例如 256MB：
-XX:MaxDirectMemorySize=256m

### Native Memory Tracking

除了 Native Memory Tracking 记录的内存使用，还有两种内存 Native Memory Tracking 没有记录，那就是：

- MMap Buffer：文件映射内存
- JNI 方法里的内存分配

### 非堆内存有哪些

1. Java Class，JVM 将类文件加载到内存中用于后续使用所占用的空间，注意是 JVM C++ 层面的内存占用，主要包括类文件中在 JVM 解析为 C++ 的 Klass 类（Klass 是 JVM 源码中的一个 C++ 类，可以理解为类在 JVM 中的内存形式）以及相关元素，对应的 Java 反射类 Class 还是在堆内存空间中。
2. Symbol：加载类的时候，有很多字符串信息，不同类的字符串信息可能会重复。所以统一放入符号表复用。 包含 SymbolTable:（存储 names signatures）和 StringTable:（存储 interned strings）。可以通过-XX:+PrintStringTableStatistics打印具体的信息
3. Thread：线程占用*内存，主要是每个线程的线程栈，一般也主要关注线程栈占用空间，其他的管理线程占用的空间很小，可以忽略不计。
4. Code：JIT编译器编译后（C1 C2编译器优化）的代码占用空间
5. Compiler：JIT 编译器(C1 C2 编译器)本身占用的空间和标记占用的内存，不受限制，一般比较小。
6. Arena 数据结构占用空间，所有通过Arena方式分配的内存。我们看到 Native Memory Tracking 中有很多通过 arena 分配的内存，这个就是管理 Arena 数据结构占用空间。
7. JVM Tracing 占用*内存，所有采集占用的内存。包括 JVM perf 以及 JFR 占用的空间，如果开启了 JFR 则主要是 JFR 占用的内存。
8. JVM GC 需要的数据结构与记录信息占用的空间，例如垃圾回收需要的 CardTable，标记数，区域划分记录，还有标记 GC Root 等等。这块内存可能会比较大，尤其是对于那种专注于低延迟的 GC，例如 ZGC。（ZGC 是一种以空间换时间的思路，提高 CPU 消耗与内存占用，但是消灭全局暂停）
9. Direct Buffer & Other：直接内存。在 NMT 中表现为 “Other”，意为：其他占用 (不是 JVM 本身而是操作系统的某些系统调用导致额外占的空间)。此项为我们大多数使用的非堆内存，也是「堆外内存」一词通常所指。
10. MMap Buffer：Java 文件映射内存大小，也是我们在 I/O 中经常用到的非堆内存，可以通过 JMX 监控
11. Native 分配：即直接调用 malloc 分配的，如 JNI 调用、磁盘与网络 io 操作等，可通过 pmap 命令、malloc_stats 函数观测。
12. 以下几种堆外内存比较小，我们一般不会关心：
    
    Logging：写 JVM 日志占用的内存

### 触发垃圾收集的原因

常见的有以下几种：

- System.gc()： 手动触发GC操作。

- CMS： CMS GC 在执行过程中的一些动作，重点关注 CMS Initial Mark 和 CMS Final Remark 两个 STW 阶段。

- Concurrent Mode Failure： CMS GC 运行期间，Old 区预留的空间不足以分配给新的对象，此时收集器会发生退化，严重影响 GC 性能。

- Heap Inspection Initiated GC：使用jmap -histo:live触发的GC。

- Allocation Failure：分配失败，JVM的Eden区中没有更多的空间来分配对象了。

- GCLocker Initiated GC： 如果线程执行在 JNI 临界区时，刚好需要进行 GC，此时 GC Locker 将会阻止 GC 的发生，同时阻止其他线程进入 JNI 临界区，直到最后一个线程退出临界区时触发一次 GC。

- Promotion Failure： Old 区没有足够的空间分配给 Young 区晋升的对象（即使总可用内存足够大）。

### 多久会触发一次新生代的 YoungGC

有一个参数 -XX:G1MaxNewSizePercent 默认值：60% ，限定了新生代最多占用堆内存 60% 的空间。

然后 新生代又有 Eden 和 两个 Survivor 组成 默认比例是： 8:1:1。



### jvm参数

#### -XX:G1MixedGCCountTarget=8

混合回收阶段会执行8次（默认值），一次只回收掉部分Region，然后系统继续运行一小段时间，之后再继续混合回收，重复8轮。混合回收通过间断操作，可以把每次的回收时间控制在指定的停顿时间之内，最终也达到了垃圾清理的效果。

Spread the old generation region reclamation across more garbage collections by increasing -XX:G1MixedGCCountTarget.

#### -XX:G1MixedGCLiveThresholdPercent=85(默认85%)

如果一个Region中的存活对象大于Region大小的85%的话（默认值），就不去回收这个Region。

Avoid collecting regions that take a proportionally large amount of time to collect by not putting them into the candidate collection set by using -XX:G1MixedGCLiveThresholdPercent. In many cases, highly occupied regions take a lot of time to collect.

#### -XX:G1HeapWastePercent=5

混合回收整理出来的空闲空间占heap的5%时（默认值），终止本次回收。

在混合回收的时候，对Region回收都是基于复制算法进行的，都是把要回收的Region里的存活对象放入其他Region，然后这个Region中的垃圾对象全部清理掉，这样的话在回收过程就会不断空出来新的Region，一旦空闲出来的Region数量达到了堆内存的5%，此时就会立即停止混合回收，意味着本次混合回收就结束了。


Stop old generation space reclamation earlier so that G1 won't collect as many highly occupied regions. In this case, increase -XX:G1HeapWastePercent.


> Note that -XX:G1MixedGCLiveThresholdPercent and -XX:G1HeapWastePercent decrease the amount of collection set candidate regions where space can be reclaimed for the current space-reclamation phase. This may mean that G1 may not be able to reclaim enough space in the old generation for sustained operation. However, later space-reclamation phases may be able to garbage collect them.