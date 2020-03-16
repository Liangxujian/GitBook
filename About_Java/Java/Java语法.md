#### Java语法

##### 一、" == "与 equals 的区别

###### 1. 数值、内存地址比较

Java中的数据类型，可分为两类：

① 基本数据类型（原始数据类型）：**short, int, long, float, double, byte, boolean, char**

* " == "比较的是他们的数值
* equals 方法不能用于基本数据类型，只能用于类变量，对于基本数据类型要用其包装类，比较的是数值

② 引用类型（类、接口、数组）

* " == "比较的是他们在内存中存放的地址（堆地址）
* 默认情况（即没有覆盖方法）下，equals 方法都是调用 Object 类的 equals 方法，而 Object 的 equals 方法主要用于判断对象的内存地址引用是不是同一个地址（是不是同一个对象），这时，equals 和" == "是等效的。
  要是类中覆盖了 equals 方法，则根据具体的代码来确定其作用，覆盖后一般都是通过对象的内容是否相等来判断对象是否相等。

###### 案例分析

````java
int int1 = 12;
Integer integer1 = new Integer(12);
int1 == integer1 // true
// 因为Integer会自动拆箱为int，所以为true
````

###### 2. 运行效率比较

" == "比 equals 方法运行速度快，因为" == "只是比较的引用。

###### 3. hashCode

hashCode() 方法返回的就是一个数值，即生成一个 hash 码。hash 码的主要用途就是在对对象进行散列的时候作为 key 输入（每个对象的 hash 码不同），因此用于保证集合中元素的唯一性（set）或是 key 的唯一性（map）实现。（set 重复元素的判断正是基于 map 的 key 重复性判断，map 重写了 Object 的 hashCode() 和 equals() 方法）

* 注：

  ① 如果两个对象 equals，Java 运行时环境会认为他们的 hashCode 一定相等
  ② 如果两个对象 hashCode 相等，他们不一定 equals
  ③ 如果两个对象 hashcode 不相等，他们一定不 equals
  ④ 若重写 equals(Object obj) 方法，有必要重写  hashcode() 方法，确保通过 equals(Object obj) 方法判断结果为 true 的两个对象具备相等的 hashcode() 返回值
  ⑤ 如果两个对象不相同，他们的 hashcode 可能相同

##### 二、Java自动装箱和拆箱

###### 自动装箱

执行`Integer i = 100;`时，系统就会自动执行装箱：`Integer i = Integer.valueOf(100);`
这里要分两种情况，当数值范围在类型的范围内时，会从高速缓存中返回实例，即常量池，执行" == "判断返回 true；当超出范围时，相当于创建了两次实例，执行" == "会返回 false。

###### 自动拆箱

自动拆箱即是将对象中的基本数据从对象中自动取出。

````java
Integer i = 10;// 装箱
int t = i;// 拆箱，实际上执行了：int t = i.intValue();
````

或

````java
Integer i = 10;
System.out.println(i++);
````

###### 案例汇总

````java
String a = "ab";
String b = new String("ab");
	--> a == b? false

// 如果值在-128到127之间，它就会返回该缓存的实例，超出则返回地址
int a = 100;
Integer b = 100;
Integer c = 100;
System.out.println(a==b);// true
System.out.println(a==c);// true
System.out.println(b==c);// true

int x = 1000;
Integer y = 1000;
Integer z = 1000;
System.out.println(x==y);// true
System.out.println(x==z);// true
System.out.println(y==z);// false
````

