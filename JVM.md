# JVM

###### jvm内存结构

![1661847878015](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661847878015.png)

###### OOM内存溢出

![1661849389534](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661849389534.png)



###### 方法区 永久代  元空间

![1661849568506](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661849568506.png)



###### 内存划分

~栈内存：线程独有 

程序计数器（Program Counter Register）：记录的是正在执行的字节码指令的地址，字节 

码解释器读取一个指令后，将该指令“翻译”成固定的操作，并根据这些操作进行分支、循环、 

跳转等流程 

虚拟机栈（VM Stack）：生命周期与线程相同。虚拟机栈描述的是Java方法执行的内存模 

型，每一个方法被调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的 

过程 

本地方法栈（Native Method Stack）：为虚拟机使用到的Native方法服务 

~堆内存（Heap Space）：线程共享 

年轻代（Young Generation）：Eden空间、From Survivor空间、To Survivor空间，一般 

8：1：1， 

老年代（Old Generation）：存活时间较久的，大小较大的对象 

~方法区（Method Area）：java8中HotSpot使用元空间（MetaSpace）来实现方法区，7之前使用 

永久代（Permanent Generation）。用于存

##### JVM垃圾回收

###### JVM垃圾回收算法

1. 标记清除

![1661850319292](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661850319292.png)

GC root :静态变量  一定不能被回收的对象  

根据跟对象的引用链 加入标记

保留标记对象,清除未标记对象

问题:内存碎片 ,清除的内存不连续 



2.标记整理

![1661850490622](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661850490622.png)

让所有存活对象向内存空间一段移动,然后直接清理掉边界以外的内存,避免内存碎片问题

效率降低



3.标记复制

![1661850664695](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661850664695.png)

复制存活对象 ,空间换时间 .



###### GC和分代回收算法  

 ![1661855013550](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661855013550.png)



分代回收

① 伊甸园 eden ,最初对象都分配到这里, 与幸存区合称新生代

②幸存区 survivor , 当伊甸园内存不足,回收的幸存对象到这里,分成 from和 to ,采用标记复制算法

③老年代 old, 当幸存区的对象熬过几次回收(最多15次),晋升到老年代(幸存区内存不足或大对象会导致提前晋升)

![1661855303214](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661855303214.png)



![1661855552940](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661855552940.png)



###### 三色标记 和 并发漏标问题

<img src="C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661855677963.png" alt="1661855677963" style="zoom: 50%;" />

①用三种颜色标记对象的标记状态

黑色 : 已标记

灰色 : 标记中

白色 : 还未被标记



②漏标问题 - 记录标记过程中变化

· Incremental Update

​	只要赋值发生 ,被赋值对象就会被记录

· Snapshot At The Beginning

​	新增对象会被记录

​	被删除引用关系对象也会被记录



 

###### 垃圾回收器

![1661863149447](C:\Users\chen\AppData\Roaming\Typora\typora-user-images\1661863149447.png)

① Parallel GC

② Concurrent Mark Sweep

③ G1 GC

将堆内存,划分多个区域

新生代回收  Eden内存不足, 标记复制STW

并发标记阶段  old并发标记

混合收集

Failback Full GC