# ThreadLocal（线程局部变量）

##### 一、ThreadLocal 是什么

首先，它是一个数据结构，有点像 HashMap，可以保存"key : value"键值对，但是一个 ThreadLocal 只能保存一个，并且各个线程的数据互不干扰。

需要注意的是，ThreadLoalMap 的 Entry 是继承 WeakReference，和 HashMap 很大的区别是，Entry 中没有 next 字段，所以就不存在链表的情况了。

````java
ThreadLocal<String> localName = new ThreadLocal();
try {
    localName.set("占小狼");
    String name = localName.get();
    // 其它业务逻辑
} finally {
    localName.remove();
}
````

##### 二、ThreadLocal 方法摘要

| 作用域    | 类型 | 方法           | 说明                                             |
| --------- | ---- | -------------- | ------------------------------------------------ |
| public    | T    | get()          | 返回此线程局部变量的当前线程副本中的值           |
| protected | T    | initialValue() | 返回此线程局部变量的当前线程的“初始值”           |
| public    | void | remove()       | 移除此线程局部变量当前线程的值                   |
| public    | void | set(T value)   | 将此线程局部变量的当前线程副本中的值设置为指定值 |

##### 三、ThreadLocal 作用及示例

由于在并发编程的时候，一个类的成员变量不做处理是线程不安全的，各个线程操作的都是同一个对象。

###### 数据库连接类管理

在数据库连接的时候，DAO 连接使用单例，那么他的属性 Connection 就不是一个线程安全的变量。
 *（而且 volatile 这个关键字也是不能保证线程安全的）*

````java
public class ConnectionUtil {
    private ConnectionUtil() {}

    private static final ThreadLocal<Connection> conn = new ThreadLocal<>();

    public static Connection getConn() {
        Connection con = conn.get();
        if (con == null) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
                con = DriverManager.getConnection("url", "userName", "password");
                conn.set(con);
            } catch (ClassNotFoundException | SQLException e) {
                // ...
            }
        }
        return con;
    }
}
````

使用了 ThreadLocal 后，能够保证变量是同一个，但是每个线程都使用同一个初始值，也就是使用同一个变量的一个新的副本。

##### 四、作用原理（摘自网上）

ThreadLocal 源码片段：

````java
/** 
  * Sets the current thread's copy of this thread-local variable 
  * to the specified value.  Most subclasses will have no need to 
  * override this method, relying solely on the {@link #initialValue} 
  * method to set the values of thread-locals. 
  * 
  * @param value the value to be stored in the current thread's copy of 
  *        this thread-local. 
  */  
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
````

> 在源码中我们看到，set 方法首先通过 `getMap(Thread t)`，把当前的线程对象传递进去，获取一个和当前线程相关的 ThreadLocalMap，然后将变量的值设置到这个 ThreadLocalMap 对象中。
>
> 当然如果获取到的 ThreadLocalMap 对象为空，就通过 createMap 方法创建。
>
> 线程隔离的秘密，就在于 ThreadLocalMap 这个类。ThreadLocalMap 是 ThreadLocal 类的一个静态内部类，它实现了键值对的设置和获取（类似 Map 对象的存在），每个线程中都有一个独立的 ThreadLocalMap 副本，它所存储的值，只能被当前线程读取和修改。
>
> 因此，ThreadLocal 类通过操作每一个线程特有的 ThreadLocalMap 副本，从而实现了变量访问在不同线程中的隔离。因为每个线程的变量都是自己特有的，所以完全不会有并发错误。
>
> **map.set(this, value)：ThreadLocalMap 存储的键值对中的 key 是 this 对象指向的 ThreadLocal 对象，而 value 就是要设置的值/对象，说得直白一点就是，ThreadLocal 存储值，是通过线程自己内部的 ThreadLocalMap 存储的 —— 这就是 ThreadLocal 的实现原理。**

源码实现片段：getMap、createMap

````java
/** 
  * Get the map associated with a ThreadLocal. Overridden in 
  * InheritableThreadLocal. 
  * 
  * @param  t the current thread 
  * @return the map 
  */  
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
  
/** 
  * Create the map associated with a ThreadLocal. Overridden in 
  * InheritableThreadLocal. 
  * 
  * @param t the current thread 
  * @param firstValue value for the initial entry of the map 
  * @param map the map to store.
  */
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
````

源码实现片段：get

````java
/**
  * Returns the value in the current thread's copy of this
  * thread-local variable.  If the variable has no value for the
  * current thread, it is first initialized to the value returned
  * by an invocation of the {@link #initialValue} method.
  * 
  * @return the current thread's value of this thread-local
  */
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null)
            return (T)e.value;
    }
    return setInitialValue();
}
````

源码实现片段：setInitialValue

```java
/**
  * Variant of set() to establish initialValue. Used instead
  * of set() in case user has overridden the set() method.
  * 
  * @return the initial value
  */
private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;  
}
```

ThreadLocalMap 中用于存储数据的 entry 定义：

````java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
````

##### 五、ThreadLocal 的内存泄露问题

由 Entry 的源码可知，**ThreadLocalMap 是使用 ThreadLocal 的弱引用作为 Key 的**。

![1563442325615](D:\GitBook\About_Java\Java高并发拓展\assets\1563442325615.png)
（实线表示强引用，虚线表示弱引用）

如图所示，ThreadLocalMap 使用 ThreadLocal 的弱引用作为 key。

当把 ThreadLocal 实例置为 null 以后，没有任何强引用指向 ThreadLocal 实例，所以 ThreadLocal 将会被 gc 回收。这样一来，ThreadLocalMap 中就会出现 key 为 null 的 Entry，且这 Entry 的 value 永远不会被访问到了，而如果当前线程再迟迟不结束的话，这些 key 为 null 的 Entry 的 value 就会一直存在一条强引用链：

Thread Ref -> Thread -> ThreaLocalMap -> Entry -> value

永远无法回收，造成内存泄露。

##### 六、内存泄漏问题解决

其实，ThreadLocalMap 在设计中已经考虑到这种情况，也加上了一些防护措施，即：

**在 ThreadLocal 进行`get(),set(),remove()` 操作的时候都会清除线程 ThreadLocalMap 里所有 key 为 null 的 value**。

但是这些被动的预防措施还是不能保证不会发生内存泄漏：

1. 使用 static 的 ThreadLocal，延长了 ThreadLocal 的生命周期，可能导致内存泄漏。
2. 分配使用了 ThreadLocal ，但是又不再调用`get(),set(),remove()`方法进行清除，那么这块内存还会一直存在，依然可能导致内存泄漏。

所以，在很多的情况下，都需要使用者手动去调用 ThreadLocal 的 remove 函数，手动删除不再需要的 ThreadLocal，防止内存泄露。

而且， JDK 也建议将 ThreadLocal 变量定义成 private static 的，这样的话 ThreadLocal 的生命周期就更长，由于一直存在ThreadLocal 的强引用，所以 ThreadLocal 也就不会被回收，也就能保证任何时候都能根据 ThreadLocal 的弱引用访问到 Entry 的 value 值，然后在 Thread 中使用完 ThreadLocal 对象后调用 remove 方法，进行手动清除，防止内存泄露。

##### 七、ThreadLocalMap 初始容量（略）

初始容量16，负载因子2/3，解决冲突的方法是再 hash 法，也就是：在当前 hash 的基础上再自增一个常量。