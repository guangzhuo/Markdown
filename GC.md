现在各大浏览器通常用采用的垃圾回收有两种方法：标记清除、引用计数

## 标记清除（老龄代）

现代浏览器大多数采用这种方式：当变量进入环境时，将变量标记"进入环境"，当变量离开环境时，标记为：“离开环境”。某一个时刻，垃圾回收器会过滤掉环境中的变量，以及被环境变量引用的变量，剩下的就是被视为准备回收的变量。

## 引用计数

引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是 1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减 1。当这个引用次数变成 0 时，则说明没有办法再访问这个值了，因而就可以将其所占的内存空间给收回来。

* 因为存在循环引用的情况会导致内存无法释放，需要手动值为 null，因此大多数的浏览器已经放弃这种回收方式。

## 什么时候触发垃圾回收

一般浏览器会自动触发 GC，我们不用太过关注。但是和其他语言一样，当触发 GC 的时候，浏览器就会停止工作。如果频繁触发 GC 页面就会发生抖动现象。

一般的 GC 耗时在 100ms 左右，对于一般的程序来说够了。但是对于一些流畅度要求高的程序来说就很麻烦，这就需要新引擎需要优化的地方。



V8 引擎的垃圾回收策略主要基于分代垃圾回收机制：

* 将整个堆内存分为新生代内存和老龄代内存，所有的内存分配操作发生在新生代

* 新生代内存又分成两部分，From（使用） 空间和 To（闲置） 空间，所有的内存分配操作发生在 From 空间

* **新生代空间发生 GC（复制算法）**（新生代）
From 空间中存活的对象复制到 To 空间，释放未存活的对象
转换两者的角色 From 空间变为 To 空间，To 空间变为 From 空间
如果某个对象已经经历过一次复制算法，就将该对象复制到老龄代空间
如果 To 空间的使用率超过了 25%，将整个空间的对象复制到老龄代空间。主要是为了角色转换之后留足分配内存的空间
* **老龄代空间发生 GC （标记清除与标记合并）**
主要采用标记清除算法，`通过标记清除算法清理未存活的对象
清除算法完成之后会使内存空间出现不连续的状态，这种内存碎片会对后续的内存分配造成问题。因此在内存空间不足的时候采用标记合并算法，将活着的对象移动到内存的一端，完成之后清除另外一端的对象
* **新生代的 GC 触发要比老龄代的频繁**
一般浏览器要求最高 60fps，算下来每帧 16.6ms。Chrome 为了缩短 GC 时间，它尝试将工作分摊到每个空闲时间。它将检查每个帧时间（16.6 ms）的剩余时间，并尝试为 GC 做一些工作。

* 如果垃圾收集事件可能很快发生，V8 GC 将检查每 n 个分配或 m 个时间单位。V8 GC 在任务调度程序中为事件注册空闲任务。
* 任务调度程序将调度空闲任务并使用可用空闲时间调用给定的回调。V8 GC 将检查任务是否仍处于待处理状态，以及是否有足够的空闲时间来处理任务。![在这里插入图片描述](https://img-blog.csdnimg.cn/20190320174356385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIwMjgyMjYz,size_16,color_FFFFFF,t_70)


## 为什么要设置两个Survivor区

首先看下复制算法：Survivor区，一块叫From，一块叫To，对象存在Eden和From块。当进行GC时，Eden存活的对象全移到To块，而From中，存活的对象按年龄值确定去向，当达到一定值（年龄阈值，通过-XX:MaxTenuringThreshold可设置）的对象会移到年老代中，没有达到值的复制到To区，经过GC后，Eden和From被清空。之后，From和To交换角色，新的From即为原来的To块，新的To块即为原来的From块，且新的To块中对象年龄加1。永远有一个survivor space是空的，另一个非空的survivor space无碎片。

为什么要这么做？

没有Survivor区，eden区对象直接去老年代，造成老年代频繁FGC。
一个Survivor容易使得Survivor的空间碎片化，因为eden区又存活对象，Survivor区也有存活对象，辅助的时候就会存在碎片化空间。
即若只分一块Survivor，在清除Survivor区已死亡的对象时，因为此刻的Survivor区还有存活的对象，清除要比分两块Survivor麻烦，两块的情况，回收时只需将存活的对象移走，剩下的对象直接清理即可。
另外，分成两块Survivor，From和To分工明确，逻辑理解和技术实现较简单。