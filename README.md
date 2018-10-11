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
GC 经常发生的区域是堆区，堆区还可以细分为新生代、老年代，新生代还分为一个 Eden 区和两个 Survivor 区。对象优先在 Eden 中分配，当 Eden 中没有足够空间时，虚拟机将发生一次 Minor GC，因为 Java 大多数对象都是朝生夕灭，所以 Minor GC 非常频繁，而且速度也很快；Full GC，发生在老年代的 GC，当老年代没有足够的空间时即发生 Full GC，发生 Full GC 一般都会有一次 Minor GC。大对象直接进入老年代，如很长的字符串数组，虚拟机提供一个XX:PretenureSizeThreadhold 参数，令大于这个参数值的对象直接在老年代中分配，避免在 Eden 区和两个 Survivor 区发生大量的内存拷贝；发生 Minor GC 时，虚拟机会检测之前每次晋升到老年代的平均大小是否大于老年代的剩余空间大小，如果大于，则进行一次 Full GC，如果小于，则查看 HandlePromotionFailure 设置是否允许担保失败，如果允许，那只会进行一次 Minor GC，如果不允许，则改为进行一次 Full GC。 

#### 06. 内存泄漏和内存溢出

概念：内存溢出指的是内存不够用了；

内存泄漏是指对象可达，但是没用了。即本该被 GC 回收的对象并没有被回收；

内存泄露是导致内存溢出的原因之一；内存泄露积累起来将导致内存溢出。

内存泄漏的原因分析：长生命周期的对象引用短生命周期的对象；没有将无用对象置为 null。

### GC

### 排序算法

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



### 索引原则及优化

## 框架相关

### 谈谈对Spring的特性理解（AOP、IOC、DI）

### AOP需要配置的类

## 多线程
