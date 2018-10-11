# Java面试题

## Java基础

### 对String的理解

String类是不可变类，当创建了这个类的实例后，就不允许修改它的值了，也就是说，一个对象一旦被创建出来，在其整个生命周期中，他的成员变量就不能被修改了。String s = “Hello”语句声明了一个可以指向String类型对象的引用，这个引用的名字为s，它指向了一个字符串常量“Hello”。s+=“ world”并没有改变s所指向的对象，这句代码运行后，s指向了另一个String类型的对象，该对象的内容为“Hello world”。原来的那个字符串常量“Hello”还存在于内存中，并没有被改变。
不可变类具有使用简单、线程安全、节省内存等优点。

### String、StringBuilder、StringBuffer

String是不可变类，也就是说，String对象一旦被创建，其值将不能被改变，而StringBuffer是可变类，当对象被创建后仍然可以对其值进行修改。由于String是不可变类，因此适合在需要被共享的场合中使用，而当一个字符串经常需要被修改时，最好使用StringBuffer来实现。
StringBuilder也是可以被修改的字符串，它与StringBuffer类似，都是字符串缓冲区，但StringBuilder不是线程安全的，如果只是在单线程中使用字符串缓冲区，那么StringBuilder的效率会更高些。因此在只有单线程访问时可以使用StringBuilder，当有多个线程访问时，最好使用线程安全的StringBuffer。因为StringBuffer必要时可以对这些方法进行同步，所以任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

### 内存管理

#### 01. JVM 内存划分

方法区（线程共享）：常量、静态变量、JIT(即时编译器) 编译后的代码也都在方法区；

堆内存（线程共享）：垃圾回收的主要场所；

程序计数器： 当前线程执行的字节码的位置指示器；

虚拟机栈（栈内存）：保存局部变量、基本数据类型变量以及堆内存中某个对象的引用变量；

本地方法栈 ：为 JVM 提供使用 native 方法的服务。

#### 02. 类似-Xms、-Xmn 这些参数的含义

答：堆内存分配：

JVM 初始分配的内存由-Xms 指定，默认是物理内存的 1/64；

JVM 最大分配的内存由-Xmx 指定，默认是物理内存的 1/4；

默认空余堆内存小于 40% 时，JVM 就会增大堆直到-Xmx 的最大限制；

空余堆内存大于 70% 时，JVM 会减少堆直到 -Xms 的最小限制；

因此服务器一般设置-Xms、-Xmx 相等以避免在每次 GC 后调整堆的大小。对象的堆内存由称为垃圾回收器的自动内存管理系统回收。

非堆内存分配：

JVM 使用-XX:PermSize 设置非堆内存初始值，默认是物理内存的 1/64；

由 XX:MaxPermSize 设置最大非堆内存的大小，默认是物理内存的 1/4；

-Xmn2G：设置年轻代大小为 2G；-XX:SurvivorRatio，设置年轻代中 Eden 区与 Survivor 区的比值。

#### 03. 垃圾回收算法有哪些？

引用计数 ：原理是此对象有一个引用，即增加一个计数，删除一个引用则减少一个计数。垃圾回收时，只用收集计数为 0 的对象。此算法最致命的是无法处理循环引用的问题；

标记-清除 ：此算法执行分两阶段。第一阶段从引用根节点开始标记所有被引用的对象，第二阶段遍历整个堆，把未标记的对象清除；此算法需要暂停整个应用，同时，会产生内存碎片；

复制算法 ：此算法把内存空间划为两个相等的区域，每次只使用其中一个区域。垃圾回收时，遍历当前使用区域，把正在使用中的对象复制到另外一个区域中；此算法每次只处理正在使用中的对象，因此复制成本比较小，同时复制过去以后还能进行相应的内存整理，不会出现 “碎片” 问题。当然，此算法的缺点也是很明显的，就是需要两倍内存空间；

标记-整理 ：此算法结合了 “标记-清除” 和 “复制” 两个算法的优点。也是分两阶段，第一阶段从根节点开始标记所有被引用对象，第二阶段遍历整个堆，把清除未标记对象并且把存活对象 “压缩” 到堆的其中一块，按顺序排放。此算法避免了 “标记-清除” 的碎片问题，同时也避免了 “复制” 算法的空间问题。

#### 04. root 搜索算法中，哪些可以作为 root？

被启动类（bootstrap 加载器）加载的类和创建的对象；

JavaStack 中的引用的对象 (栈内存中引用的对象)；

方法区中静态引用指向的对象；

方法区中常量引用指向的对象；

Native 方法中 JNI 引用的对象。

#### 05. GC 什么时候开始？
GC 经常发生的区域是堆区，堆区还可以细分为新生代、老年代，新生代还分为一个 Eden 区和两个 Survivor 区。对象优先在 Eden 中分配，当 Eden 中没有足够空间时，虚拟机将发生一次 Minor GC，因为 Java 大多数对象都是朝生夕灭，所以 Minor GC 非常频繁，而且速度也很快；Full GC，发生在老年代的 GC，当老年代没有足够的空间时即发生 Full GC，发生 Full GC 一般都会有一次 Minor GC。大对象直接进入老年代，如很长的字符串数组，虚拟机提供一个XX:PretenureSizeThreadhold 参数，令大于这个参数值的对象直接在老年代中分配，避免在 Eden 区和两个 Survivor 区发生大量的内存拷贝；发生 Minor GC 时，虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小，如果大于，则进行一次 Full GC，如果小于，则查看 HandlePromotionFailure 设置是否允许担保失败，如果允许，那只会进行一次 Minor GC，如果不允许，则改为进行一次 Full GC。06. 

#### 内存泄漏和内存溢出

概念：内存溢出指的是内存不够用了；

内存泄漏是指对象可达，但是没用了。即本该被 GC 回收的对象并没有被回收；

内存泄露是导致内存溢出的原因之一；内存泄露积累起来将导致内存溢出。

内存泄漏的原因分析：长生命周期的对象引用短生命周期的对象；没有将无用对象置为 null。

### GC

### 排序算法

## 数据库相关

### 事务

### 索引原则及优化

## 框架相关

### 谈谈对Spring的特性理解（AOP、IOC、DI）

### AOP需要配置的类

## 多线程
