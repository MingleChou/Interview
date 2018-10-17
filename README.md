# Java面试题

## Java基础

### 为什么说Java是平台独立性语言

Java是解释性语言（C++是编译性语言），保证Java具有平台独立性的机制是“中间码（字节码）”和“JVM”，它可以一次编译，处处运行，只需要在平台上安装对应的JVM，就能把“中间码（字节码）”解译成平台能执行的代码。

### 对String的理解

String类是不可变类，当创建了这个类的实例后，就不允许修改它的值了，也就是说，一个对象一旦被创建出来，在其整个生命周期中，他的成员变量就不能被修改了。String s = “Hello”语句声明了一个可以指向String类型对象的引用，这个引用的名字为s，它指向了一个字符串常量“Hello”。s+=“ world”并没有改变s所指向的对象，这句代码运行后，s指向了另一个String类型的对象，该对象的内容为“Hello world”。原来的那个字符串常量“Hello”还存在于内存中，并没有被改变。
不可变类具有使用简单、线程安全、节省内存等优点。

### String、StringBuilder、StringBuffer

String是不可变类，也就是说，String对象一旦被创建，其值将不能被改变，而StringBuffer是可变类，当对象被创建后仍然可以对其值进行修改。由于String是不可变类，因此适合在需要被共享的场合中使用，而当一个字符串经常需要被修改时，最好使用StringBuffer来实现。
StringBuilder也是可以被修改的字符串，它与StringBuffer类似，都是字符串缓冲区，但StringBuilder不是线程安全的，如果只是在单线程中使用字符串缓冲区，那么StringBuilder的效率会更高些。因此在只有单线程访问时可以使用StringBuilder，当有多个线程访问时，最好使用线程安全的StringBuffer。因为StringBuffer必要时可以对这些方法进行同步，所以任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。

### volatile关键是什么作用？它跟synchronized方法有什么不同？

volatile关键字的作用是：保证变量的可见性。 
它被设计用来修饰被不同线程访问和修改的变量，它保证读取数据时只从主存空间读取，修改数据直接修改到主存空间中去，这样就保证了这个变量对多个操作线程的可见性了。换句话说，被volatile修饰的变量，能保证该变量的 单次读或者单次写 操作是原子的。

线程安全是两方面需要的： 原子性(指的是多条操作)和可见性。volatile只能保证可见性，synchronized是两个均保证的。 
volatile轻量级，只能修饰变量；synchronized重量级，还可修饰方法。 
volatile不会造成线程的阻塞，而synchronized可能会造成线程的阻塞。

### final, finally, finalize 的区别

（1）final是关键字，它可以修饰属性、方法、类，分别表示属性不可变、方法不可覆盖、类不能被继承。

（2）finally作为异常处理的一部分，只能用在try/catch语句中，并且附带一个语句块，表示这段语句最终一定被执行，通常用于释放资源。

（3）finalize是Object类的方法，在垃圾回收器执行时会调用对象的finalize方法，可以覆盖此方法实现对资源的回收。

### 抽象类和接口的区别

接口是公开的，里面不能有私有的方法或变量，是用于让别人使用的，而抽象类是可以有私有方法或私有变量的，另外，实现接口的一定要实现接口里定义的所有方法，而实现抽象类可以有选择地重写需要用到的方法，一般的应用里，最顶级的是接口，然后是抽象类实现接口，最后才到具体类实现。还有，接口可以实现多重继承，而一个类只能继承一个超类，但可以通过继承多个接口实现多重继承。

### HashMap的工作原理

HashMap是基于hashing的原理，我们使用put(key, value)存储对象到HashMap中，使用get(key)从HashMap中获取对象。当我们给put()方法传递键和值时，我们先对键调用hashCode()方法，返回的hashCode用于找到bucket位置来储存Entry对象。”这里关键点在于指出，HashMap是在bucket中储存键对象和值对象，作为Map.Entry。这一点有助于理解获取对象的逻辑。如果你没有意识到这一点，或者错误的认为仅仅只在bucket中存储值的话，你将不会回答如何从HashMap中获取对象的逻辑。
“如果两个键的hashcode相同，你如何获取值对象？” 面试者会回答：当我们调用get()方法，HashMap会使用键对象的hashcode找到bucket位置，找到bucket位置之后，会调用keys.equals()方法去找到链表中正确的节点
当创建 HashMap 时，有一个默认的负载因子（load factor），其默认值为 0.75，这是时间和空间成本上一种折衷：增大负载因子可以减少 Hash 表（就是那个 Entry数组）所占用的内存空间，但会增加查询数据的时间开销，而查询是最频繁的的操作（HashMap的get()与put()方法都要用到查询）；减小负载因子会提高数据查询的性能，但会增加Hash表所占用的内存空间。
“如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？”除非你真正知道HashMap的工作原理，否则你将回答不出这道题。默认的负载因子大小为0.75，也就是说，当一个map填满了75%的bucket时候，和其它集合类(如ArrayList等)一样，将会创建原来HashMap大小的两倍的bucket数组，来重新调整map的大小，并将原来的对象放入新的bucket数组中。这个过程叫作rehashing，因为它调用hash方法找到新的bucket位置。

JDK 1.8 以后哈希表的 添加、删除、查找、扩容方法都增加了一种 节点为 TreeNode 的情况
添加时，当桶中链表个数超过 8 时会转换成红黑树；
删除、扩容时，如果桶中结构为红黑树，并且树中元素个数太少的话，会进行修剪或者直接还原成链表结构；
查找时即使哈希函数不优，大量元素集中在一个桶中，由于有红黑树结构，性能也不会差。

### HashMap和Hashtable的区别

HashMap和Hashtable都实现了Map接口，但决定用哪一个之前先要弄清楚它们之间的分别。主要的区别有：线程安全性，同步(synchronization)，以及速度。

1、存储结构

HashMap的存储规则:

优先使用数组存储, 如果出现Hash冲突, 将在数组的该位置拉伸出链表进行存储(在链表的尾部进行添加), 如果链表的长度大于设定值后, 将链表转为红黑树。

HashTable的存储规则:

优先使用数组存储, 存储元素时, 先取出下标上的元素(可能为null), 然后添加到数组元素Entry对象的next属性中(在链表的头部进行添加)，出现Hash冲突时, 新元素next属性会指向冲突的元素，如果没有Hash冲突, 则新元素的next属性就是null。

2、扩容方式

HashMap：oldCap * 2

HashTable：oldCap * 2 + 1

3、关于null值

HashMap：线程不安全；HashTable：HashTable

HashMap虽然是线程不安全的, 但还是推荐使用, 因为 HashTable实现线程安全的方式太低效了, 直接在方法上加了 synchronized 关键字来实现的.
可以使用 ConcurrentHashMap 来实现线程安全(推荐使用)，或者使用 Collections.synchronizedMap(map); 来实现线程安全(不推荐, 因为内部是使用了synchronized代码块进行控制并发的)。

### 有哪些集合

Collection接口下的List、Set、Queue；它们有各自的特点，Set的集合里不允许对象有重复的值，List允许有重复，它对集合中的对象进行索引，Queue的工作原理是FCFS算法(First Come, First Serve)。

#### List的三个子类的特点

ArrayList:
　　底层数据结构是数组，查询快，增删慢。
　　线程不安全，效率高。
  
Vector:
　　底层数据结构是数组，查询快，增删慢。
　　线程安全，效率低。
Vector相对ArrayList查询慢(线程安全的)；
Vector相对LinkedList增删慢(数组结构)

LinkedList:
　　底层数据结构是链表，查询慢，增删快。
　　线程不安全，效率高。

#### Vector和ArrayList的区别
　　Vector是线程安全的,效率低；ArrayList是线程不安全的,效率高
　  共同点:底层数据结构都是数组实现的,查询快,增删慢
   
#### ArrayList和LinkedList的区别
　　ArrayList底层是数组结果,查询和修改快；LinkedList底层是链表结构的,增和删比较快,查询和修改比较慢
　  共同点:都是线程不安全的.
 
### 说说反射的用途及实现

在运行时构造一个类的对象；判断一个类所具有的成员变量和方法；调用一个对象的方法；生成动态代理。反射最大的应用就是框架。

spring 的 ioc/di 也是反射….

javaBean和jsp之间调用也是反射….

struts的 FormBean 和页面之间…也是通过反射调用….

JDBC 的 classForName()也是反射…..

hibernate的 find(Class clazz) 也是反射….

### equals 与 == 的区别

值类型（int,char,long,boolean等）都是用==判断相等性。对象引用的话，==判断引用所指的对象是否是同一个。equals是Object的成员函数，有些类会覆盖（override）这个方法，用于判断对象的等价性。例如String类，两个引用所指向的String都是”abc”，但可能出现他们实际对应的对象并不是同一个（String s="abc"和String s=new String("abc")），因此用==判断他们可能不相等，但用equals判断一定是相等的。

### NIO和IO的区别
NIO主要有三大核心部分：Channel(通道)，Buffer(缓冲区), Selector。传统IO基于字节流和字符流进行操作，而NIO基于Channel和Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector(选择区)用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个线程可以监听多个数据通道。

1、面向流与面向缓冲

Java IO和NIO之间第一个最大的区别是，IO是面向流的，NIO是面向缓冲区的。 Java IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。如果需要前后移动从流中读取的数据，需要先将它缓存到一个缓冲区。 Java NIO的缓冲导向方法略有不同。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动。这就增加了处理过程中的灵活性。但是，还需要检查是否该缓冲区中包含所有您需要处理的数据。而且，需确保当更多的数据读入缓冲区时，不要覆盖缓冲区里尚未处理的数据。

2、阻塞与非阻塞IO

Java IO的各种流是阻塞的。这意味着，当一个线程调用read() 或 write()时，该线程被阻塞，直到有一些数据被读取，或数据完全写入，该线程在此期间不能再干任何事情了。Java NIO的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。 非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。 线程通常将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。

3、选择器（Selectors）

Java NIO的选择器允许一个单独的线程来监视多个输入通道，你可以注册多个通道使用一个选择器，然后使用一个单独的线程来“选择”通道：这些通道里已经有可以处理的输入，或者选择已准备写入的通道。这种选择机制，使得一个单独的线程很容易来管理多个通道。

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
GC 经常发生的区域是堆区，堆区还可以细分为新生代、老年代，新生代还分为一个 Eden 区和两个 Survivor 区。对象优先在 Eden 中分配，当 Eden 中没有足够空间时，虚拟机将发生一次 Minor GC，因为 Java 大多数对象都是朝生夕灭，所以 Minor GC 非常频繁，而且速度也很快；Full GC，发生在老年代的 GC，当老年代没有足够的空间时即发生 Full GC，发生 Full GC 一般都会有一次 Minor GC。大对象直接进入老年代，如很长的字符串数组，虚拟机提供一个XX:PretenureSizeThreadhold 参数，令大于这个参数值的对象直接在老年代中分配，避免在 Eden 区和两个 Survivor 区发生大量的内存拷贝；发生 Minor GC 时，虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小，如果大于，则进行一次 Full GC，如果小于，则查看 HandlePromotionFailure 设置是否允许担保失败，如果允许，那只会进行一次 Minor GC，如果不允许，则改为进行一次 Full GC。 

#### 06. 内存泄漏和内存溢出

概念：内存溢出指的是内存不够用了；

内存泄漏是指对象可达，但是没用了。即本该被 GC 回收的对象并没有被回收；

内存泄露是导致内存溢出的原因之一；内存泄露积累起来将导致内存溢出。

内存泄漏的原因分析：长生命周期的对象引用短生命周期的对象；没有将无用对象置为 null。

### GC

### 排序算法

![blockchain](https://images2015.cnblogs.com/blog/739525/201605/739525-20160503202729044-614991035.jpg "排序算法")

+ 冒泡排序：平均O(n^2)，最好O(n)，最坏O(n^2)，辅助空间O(1)，稳定

第一层循环遍历数组中除了最后一位的所有数，第二层循环相当于定义一个大小为2的窗口，将窗口内的元素按大小排序。

```
void BubbleSort(int A[], int n)
{
    for (int j = 0; j < n - 1; j++)         // 每次最大元素就像气泡一样"浮"到数组的最后
    {
        for (int i = 0; i < n - 1 - j; i++) // 依次比较相邻的两个元素,使较大的那个向后移
        {
            if (A[i] > A[i + 1])            // 如果条件改成A[i] >= A[i + 1],则变为不稳定的排序算法
            {
                Swap(A, i, i + 1);
            }
        }
    }
}
```

+ 选择排序：平均O(n^2)，最好O(n^2)，最坏O(n^2)，辅助空间O(1)，不稳定

遍历数组找到最小的一个放在原数组前边已经有序的序列末尾。第一层循环控制遍历数组中出最后一位的所有数，第二层循环遍历数据找到数组中最小的数字，并与当前第一层便利到的数字做交换。

```
void SelectionSort(int A[], int n)
{
    for (int i = 0; i < n - 1; i++)         // i为已排序序列的末尾
    {
        int min = i;
        for (int j = i + 1; j < n; j++)     // 未排序序列
        {
            if (A[j] < A[min])              // 找出未排序序列中的最小值
            {
                min = j;
            }
        }
        if (min != i)
        {
            Swap(A, min, i);    // 放到已排序序列的末尾，该操作很有可能把稳定性打乱，所以选择排序是不稳定的排序算法
        }
    }
}
```

+ 插入排序：平均O(n^2)，最好O(n)，最坏O(n^2)，辅助空间O(1)，稳定

假定从数组的第一位开始数组是有序的，那么遍历数组中剩余的数字，与有序数组段从后向前作比较，并将其放在合适的位置上

```
void InsertionSort(int A[], int n)
{
    for (int i = 1; i < n; i++)         // 类似抓扑克牌排序
    {
        int get = A[i];                 // 右手抓到一张扑克牌
        int j = i - 1;                  // 拿在左手上的牌总是排序好的
        while (j >= 0 && A[j] > get)    // 将抓到的牌与手牌从右向左进行比较
        {
            A[j + 1] = A[j];            // 如果该手牌比抓到的牌大，就将其右移
            j--;
        }
        A[j + 1] = get; // 直到该手牌比抓到的牌小(或二者相等)，将抓到的牌插入到该手牌右边(相等元素的相对次序未变，所以插入排序是稳定的)
    }
}
```

+ 希尔排序：平均O(nlogn)~O(n^2)，最好O(n^1.3)，最坏O(n^2)，辅助空间O(1)，不稳定

采用不同的步长对原数组进行采样，对每一个采样序列进行插入排序，步长不断减少直至步长为1

```
void ShellSort(int A[], int n)
{
    int h = 0;
    while (h <= n)                          // 生成初始增量
    {
        h = 3 * h + 1;
    }
    while (h >= 1)
    {
        for (int i = h; i < n; i++)
        {
            int j = i - h;
            int get = A[i];
            while (j >= 0 && A[j] > get)
            {
                A[j + h] = A[j];
                j = j - h;
            }
            A[j + h] = get;
        }
        h = (h - 1) / 3;                    // 递减增量
    }
}

```

+ 堆排序：平均O(nlogn)，最好O(nlogn)，最坏O(nlogn)，辅助空间O(1)，不稳定

使用数组构建一个大顶堆，使堆顶（数组中的最大值）与数组尾进行交换，堆大小减一，对堆进行调整使其再次成为一个大顶堆，重复上述步骤直至堆大小为1

```
void Heapify(int A[], int i, int size)  // 从A[i]向下进行堆调整
{
    int left_child = 2 * i + 1;         // 左孩子索引
    int right_child = 2 * i + 2;        // 右孩子索引
    int max = i;                        // 选出当前结点与其左右孩子三者之中的最大值
    if (left_child < size && A[left_child] > A[max])
        max = left_child;
    if (right_child < size && A[right_child] > A[max])
        max = right_child;
    if (max != i)
    {
        Swap(A, i, max);                // 把当前结点和它的最大(直接)子节点进行交换
        Heapify(A, max, size);          // 递归调用，继续从当前结点向下进行堆调整
    }
}

int BuildHeap(int A[], int n)           // 建堆，时间复杂度O(n)
{
    int heap_size = n;
    for (int i = heap_size / 2 - 1; i >= 0; i--) // 从每一个非叶结点开始向下进行堆调整
        Heapify(A, i, heap_size);
    return heap_size;
}

void HeapSort(int A[], int n)
{
    int heap_size = BuildHeap(A, n);    // 建立一个最大堆
    while (heap_size > 1)    　　　　　　 // 堆（无序区）元素个数大于1，未完成排序
    {
        // 将堆顶元素与堆的最后一个元素互换，并从堆中去掉最后一个元素
        // 此处交换操作很有可能把后面元素的稳定性打乱，所以堆排序是不稳定的排序算法
        Swap(A, 0, --heap_size);
        Heapify(A, 0, heap_size);     // 从新的堆顶元素开始向下进行堆调整，时间复杂度O(logn)
    }
}
```

+ 归并排序：平均O(nlogn)，最好O(nlogn)，最坏O(nlogn)，辅助空间O(n)，稳定

采用分治思想，先将整个数组分成若干小序列（如长度为2的序列），对这些序列分别进行排序，然后将这些有序的子序列进行两两合并，直到生成完整的有序数组

```
void mergeSort(int[] array, int start, int end){
    if(start < end){
        int q = (start + end) / 2;
        mergeSort(array, start, q);
        mergeSort(array, q + 1, end);
        merge(array, start, q, end);
    }
}

void merge(int[] array, int p, int q, int r){
    int i, j, k, n1, n2;
    n1 = q - p + 1;
    n2 = r - q;
    int[] L = new int[n1];
    int[] R = new int[n2];
    for(i = 0, k = p; i < n1; i++, k++)
        L[i] = array[k];
    for(i = 0, k = q + 1; i < n2; i++, k++)
        R[i] = array[k];
    for(i = 0, j = 0, k = p; i < n1 && j < n2; k++){
        if(L[i] < R[j]){
            array[k] = L[i];
            i++;
        }
        else {
            array[k] = R[j];
            j++;
        }
    }
    if(i < n1){
        for(; i < n1; i++, k++)
            array[k] = L[i];
    }
    else if(j < n2){
        for(; j < n2; j++, k++)
            array[k] = R[j];
    }
}
```

+ 快速排序：平均O(nlogn)，最好O(nlogn)，最坏O(n^2)，辅助空间O(logn)~O(n)，不稳定

采用分治策略，在原数组中挑选一个基准，将小于基准的所有数放在基准值的左边，将大于基准的所有数放在基准值的右边，对上边的两个分区进行递归分区，直到序列的大小为0或1

```
void quickSort(int[] array, int low, int high){
    int i, j;
    int index;
    if(low > high)
        return;
    i = low;
    j = high;
    index = array[i];
    while(i < j){
        while(i < j && array[j] >= index)
            j--;
        if(i < j)
            array[i++] = array[j];
        while (i < j && array[i] < index)
            i++;
        if(i < j)
            array[j--] = array[i];
    }
    array[i] = index;
    quickSort(array, low, i - 1);
    quickSort(array, i + 1, high);
}
```

## 数据库相关

### 事务

事务是数据库中一个单独的执行单元，它通常由高级数据库操作语言或编程语言编写的用户程序的执行所引起。当在数据库中更改数据成功时，在事务中更改的数据便会提交，不再改变。否则，事务就取消或者回滚，更改无效。

事务必须满足4个属性，原子性（atomicity）、一致性（consistency）、隔离性（isolation）、持久性（durability），即ACID。

+ 原子性：事务是不可分割的，要么都执行，要么都不执行；

+ 一致性： 事务执行前后，数据库数据必须保持一致性状态。数据库的一致性状态应该满足模式锁制定的约束。例如银行转账时，转账前后两个账户金额之和应保持不变。**由于并发操作带来的数据不一致性包括**：丢失数据修改、读“脏”数据、不可重复读、产生“幽灵”数据；

+ 隔离性：隔离性也被称为独立性，当两个或多个事务并发执行时，为了保证数据的安全性，将一个事务内部的操作与事务的操作隔离起来，不被其他正在进行的事务看到。**数据库有4种类型的事务隔离级别**：不提交的读、提交的读、可重复读、串行化；

+ 持久性：也被称为永久性，事务完成后，DBMS保证它对数据库中的数据的修改时永久性的。

***读“脏”数据***：一个事务读取了另一个事务尚未提交的数据，例如，当事务A与事务B并发执行时，当事务A更新后，事务B查询读取到A尚未提交的数据，此时事务A回滚，则事务B读到的数据是无效的“脏”数据。

***不可重复读***：一个事务的操作导致另一个事务前后两次读取到不同的数据，例如，当事务A与事务B并发执行时，当事务B查询读取数据后，事务A更新操作更改事务B查询到的数据，此时事务B再次读取该数据，发现前后两次的数据不一样。

***虚读***：一个事务的操作导致另一个事务前后两次查询的结果数据量不同，例如，当事务A与B并发执行时，当事务B查询读取数据后，事务A新增或删除了一条满足事务B的查询条件的记录，此时，事务B再次查询，发现查询到前次不存在的记录，或前次的某个记录不见了。

### 什么是死锁？产生死锁的条件
死锁是指在一组进程中的各个进程均占有不会释放的资源，但因互相申请被其他进程所站用不会释放的资源而处于的一种永久等待状态。

（1）互斥条件：一个资源每次只能被一个进程使用。

（2）请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。

（3）不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺。

（4）循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。

### 乐观锁和悲观锁的区别
乐观锁：总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。在Java中java.util.concurrent.atomic包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。

悲观锁：总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程）。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。Java中synchronized和ReentrantLock等独占锁就是悲观锁思想的实现。

### 索引原则及优化

+ 选择唯一性索引

+ 索引的列为where后面经常作为条件的字段建立索引

+ 为经常需要进行排序、分组和联合操作的字段建立索引

+ 限制索引的数目，索引的数目多，对系统的资源也是一种消耗

+ 尽量使用数据量少的索引

+ 尽量使用前缀来索引

+ 删除不再使用的索引

+ 小表不应该建立索引

### 三大范式

+第一范式：字段是最小的的单元不可再分。
学生信息组成学生信息表，有年龄、性别、学号等信息组成。这些字段都不可再分，所以它是满足第一范式的

+第二范式：满足第一范式,表中的字段必须完全依赖于全部主键而非部分主键。
其他字段组成的这行记录和主键表示的是同一个东西，而主键是唯一的，它们只需要依赖于主键，也就成了唯一的
学号为1024的同学，姓名为Java3y，年龄是22岁。姓名和年龄字段都依赖着学号主键。

第三范式：满足第二范式，非主键外的所有字段必须互不依赖 
就是数据只在一个地方存储，不重复出现在多张表中，可以认为就是消除传递依赖。
比如，我们大学分了很多系（中文系、英语系、计算机系……），这个系别管理表信息有以下字段组成：系编号，系主任，系简介，系架构。那我们能不能在学生信息表添加系编号，系主任，系简介，系架构字段呢？不行的，因为这样就冗余了，非主键外的字段形成了依赖关系(依赖到学生信息表了)！正确的做法是：学生表就只能增加一个系编号字段。


## 框架相关

### 谈谈对Spring的特性理解（AOP、IOC、DI）

+ AOP：是面向切面的编程，动态封装一些可重用（与业务无关）的代码。比如说你每做一次对数据库操作，都要生成一句日志。如果，你对数据库的操作有很多类，那你每一类中都要写关于日志的方法。但是如果你用aop，那么你可以写一个方法，在这个方法中有关于数据库操作的方法，每一次调用这个方法的时候，就加上生成日志的操作。

原理：通过动态代理的方式为程序添加统一功能，集中解决一些公共问题。

优点：1.各个步骤之间的良好隔离性耦合性大大降低；2.源代码无关性，再扩展功能的同时不对源码进行修改操作 

应用场景：Authentication 权限、Caching 缓存、Context passing 内容传递、Error handling 错误处理、Lazy loading懒加载、Debugging调试、logging, tracing, profiling and monitoring 记录跟踪优化校准、Performance optimization　性能优化、Persistence 持久化、Resource pooling　资源池、Synchronization　同步、Transactions 事务

+ IOC：是控制翻转或是依赖注入。通俗的讲就是如果在什么地方需要一个对象，你自己不用去通过new 生成你需要的对象，而是通过spring的bean工厂为你长生这样一个对象。

+ DI：依赖注入，是IOC的一种重要实现。比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就这么来的。

### AOP的原理：动态代理及其源码解析

https://www.cnblogs.com/gonjan-blog/p/6685611.html

### 什么是Spring？

它是一个一站式（full-stack全栈式）框架，提供了从表现层（SpringMVC）到业务层（Spring）再到持久层（Springdata）的一套完整的解决方案。我们在项目中可以只使用Spring一个框架，它就可以提供表现层的mvc框架，持久层的Dao框架。它的两大核心IoC和AOP更是为我们程序解耦和代码简洁易维护提供了支持。

### Spring优点
1.降低了组件之间的耦合性 ，实现了软件各层之间的解耦
2.可以使用容易提供的众多服务，如事务管理，消息服务等 
3.容器提供单例模式支持 
4.容器提供了AOP技术，利用它很容易实现如权限拦截，运行期监控等功能 
5.容器提供了众多的辅助类，能加快应用的开发 
6.spring对于主流的应用框架提供了集成支持，如hibernate，JPA，Struts等 
7.spring属于低侵入式设计，代码的污染极低 
8.独立于各种应用服务器 
9.spring的DI机制降低了业务对象替换的复杂性 
10.Spring的高度开放性，并不强制应用完全依赖于Spring，开发者可以自由选择spring 的部分或全部 

### 什么是SpringMVC？

Spring MVC是一个基于MVC架构的用来简化web应用程序开发的应用开发框架，它是Spring的一个模块,无需中间整合层来整合，它和Struts2一样都属于表现层的框架。在web模型中，MVC是一种很流行的框架，通过把Model，View，Controller分离，把较为复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合。

### SpringMVC优点

1、它是基于组件技术的.全部的应用对象,无论控制器和视图,还是业务对象之类的都是java组件.并且和Spring提供的其他基础结构紧密集成.
2、不依赖于Servlet API(目标虽是如此,但是在实现的时候确实是依赖于Servlet的)
3、可以任意使用各种视图技术,而不仅仅局限于JSP
4、支持各种请求资源的映射策略
5、它应是易于扩展的

### SpringMVC的流程

（1）用户发送请求至前端控制器DispatcherServlet；

（2） DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handle；

（3）处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet；

（4）DispatcherServlet通过HandlerAdapter处理器适配器调用处理器；

（5）执行处理器(Handler，也叫后端控制器)；

（6）Handler执行完成返回ModelAndView；

（7）HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；

（8）DispatcherServlet将ModelAndView传给ViewReslover视图解析器进行解析；

（9）ViewReslover解析后返回具体View；

（10）DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）；

（11）DispatcherServlet响应用户。

![blockchain1](https://img-blog.csdn.net/20180708224853769?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E3NDUyMzM3MDA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70 "SpringMVC流程图")

### 比较一下JDBC、Hibernet、Mybatis

#### JDBC
JDBC是一种执行SQL的Java API，是用Java语言编写的类和接口组成的，为多种关系型数据库提供了统一的访问接口。

弊端：
①开发工作量大，需要先连接，然后处理JDBC底层事务，还需要操作Connection对象、Statement/PreparedStatement对象，ResultSet对象拿到数据，再关闭这些资源

②使用时连接、不使用就关闭，这样频繁的操作数据库，会浪费资源，降低数据库性能。

#### Hibernet
Hibernet是对JDBC的封装，是一个自动化的ORM框架（对象关系模型）它是POJO通过XML或注解的方式与数据库表映射，是全表映射的模型

优点：
①对JDBC的封装，不用写SQL语句，提高了开发效率

②使用HQL语言，方便数据库移植

③一个会话中，只需要操作一个session对象，关闭资源也只需要关闭一个session，有了Spring后，这些交给Spring自动管理

缺点：
①表更新时需要发送全部字段，查询时也会将我们不想查询的字段也查询出来，这就是全表映射的弊端。无法组装我们自己需要的SQL语句

②Hibernet不能很好的支持存储过程

③自带的HQL语言性能不高，当我们查询的数据量很大时，必然需要优化SQL，而Hibernet无法做到

④学习门槛高，不容易掌握

#### Mybatis
Mybatis也是对JDBC的封装，是一个半自动化的ORM框架，能自由控制SQL语句，支持动态SQL、支持存储过程，对于大型互联网应用和需要考虑查询优化的都可以采用Mybatis

## 多线程

###  实现多线程有哪些方法
（1）继承Thread类，重写run方法

本质上是一个实现了Runnable接口的一个实例，通过start()方法启动线程，并执行run()方法，需要注意的是，start()方法不是立刻执行多线程代码，而是使得线程进入可运行状态，什么时候执行由操作系统决定。

（2）实现Runnable接口，实现run方法

自定义类实现Runnable接口，并实现run方法；创建Thread对象，用实现Runnable接口的对象作为参数实例化该Thread对象；调用Thread的start()方法启动线程。

（3）实现Callable接口，实现call方法

Callable可以在任务结束后提供返回值，而Runnable无法提供这个功能；Callable可以在call()中抛出异常，而Runnable的run()方法不能抛出异常。

### 多线程同步的方法有哪些

（1）synchronized关键字
（2）wait()和notify()/notifyAll()
（3）Lock接口（lock()、tryLock()、tryLock(long time,TimeUnit unit)、lockInterruptibly()）

## Java Web

### JSP的内置对象

1. request  请求对象　

　　类型 javax.servlet.HttpServletRequest        作用域 Request

2. response  响应对象    

　　类型 javax.servlet.HttpServletResponse       作用域  Page

3. pageContext   页面上下文对象     

　　类型 javax.servlet.jsp.PageContext      作用域    Page

4. session会话对象  

　　类型 javax.servlet.http.HttpSession       作用域    Session

5. application  应用程序对象     

　　类型 javax.servlet.ServletContext          作用域    Application

6. out  输出对象                

　　类型 javax.servlet.jsp.JspWriter           作用域    Page

7. config  配置对象              

　　类型 javax.servlet.ServletConfig          作用域    Page

8. page  页面对象           

　　类型 javax.lang.Object                    作用域    Page

9. exception 例外对象         

　　类型 javax.lang.Throwable                 作用域    page
  


