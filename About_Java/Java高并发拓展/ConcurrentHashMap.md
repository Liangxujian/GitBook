# 分段锁 HashMap -- ConcurrentHashMap

*（Java8 实现 —— 引入了红黑树）*

由于`HashMap`是线程不安全的，可以采用带有分段锁的`ConcurrentHashMap`

##### 一、HashMap 结构（桶）：

​		HashMap 采取数组加链表的存储方式，即数组（散列桶）中每一个元素都是链表的一个条目（Entry）。

![1562914975790](D:\GitBook\About_Java\Java高并发拓展\assets\1562914975790.png)

例如：

创建一个初始容量为 16，加载因子为 1.5F 的 HashMap。
根据 threshold = (capacity * loadFactor)，
HashMap 的实际容量是根据桶的数量乘以加载因子，所以这个 HashMap 的实际容量为 24。
因此可以知道有桶存放了两个或以上的元素，而元素根据计算的 hash 值来存放桶。
*（其实元素的 hash 值是不相等的，只是运算后桶的位置相同，所以被放在同一个桶中，形成链表或红黑树）*

![1562915108321](D:\GitBook\About_Java\Java高并发拓展\assets\1562915108321.png)

##### 二、ConcurrentHashMap 处理高并发（分段锁）：

每个分段就是一个小小的线程安全的 HashTable
分段锁的意思是：只有在同一个分段内才存在竞态关系，不同的分段锁之间没有锁竞争。
相比于对整个 Map 加锁的设计，分段锁能够大大的提高了高并发环境下的处理能力。

##### 三、ConcurrentHashMap vs HashMap：

HashEntry 中的 value 以及 next 都被 volatile 修饰，这样在多线程读写过程中能够保持它们的可见性

````java
static final class HashEntry<K, V> {
    final int hash;
    final K key;
    volatile V value;
    volatile HashEntry<K, V> next;
}
````

**可见性即是不同线程间能够看到各自的变量，具体操作是变量变为共享属性，每个线程只是复制共享变量；**
**每次线程改变变量，则会刷新共享变量，然后把最新的共享变量的值更新到每个线程中。**

在这还需要理解一个`并发度`的概念，即设置分段锁的个数。
ConcurrentHashMap 默认的并发度为 16，但用户也可以在构造函数中设置并发度。
当用户设置并发度时，ConcurrentHashMap 会使用大于等于该值的最小 2 幂指数作为实际并发度（假如用户设置并发度为 17，实际并发度则为 32）。

* 并发度过小，会带来严重的锁竞争问题；
* 并发度过大，会导致 CPU cache 命中率下降，从而降低程序性能。