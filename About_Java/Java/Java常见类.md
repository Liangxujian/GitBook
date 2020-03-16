#### Java常见类

##### 一、Java Object 超类

1. Object 超类自带的一些方法：

   `toString()`：默认看到的就是“类名@内存地址”的形式；
   `equals()`：默认比较两个对象的内存地址；
   `close()`；`finalize()`；`getClass()`；`hashCode()`

2. Object 超类部分方法声明：

````java
package java.lang;
public class Object {
    public Object();
    public String toString(Object obj);
    public boolean equals(Object obj);
    protected void finalize() throws Throwable;
    public fianl Class<?> getClass();
}
````

##### 二、String、StringBuffer、StringBuilder

1. 三者都是用来处理字符串的类
2. 三者执行速度比较（例如字符串的组合速度）：**StringBuilder > StringBuffer > String**
3. StringBuilder —— 非线程安全的
   StringBuffer —— 线程安全的
   *（因为 StringBuffer 在支持线程同步、保证线程安全的情况下，性能会有所下降。故当我们在单线程的情况下进行操作时，尽量常用 StringBuilder）*

总结：

* 如果要操作少量的数据 -> String
* 不考虑线程安全处理字符串缓冲区下大量数据 → StringBuilder
* 需考虑线程安全处理字符串缓冲区下大量数据 → StringBuffer

##### 三、特殊的 String 类

1. 虽然是引用类型，但是可以像基础数据类型一样直接赋值（`String s = "";`）
2. 不可被继承，String 类被`final`修饰
