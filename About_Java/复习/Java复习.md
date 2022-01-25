# Java 复习

## Java 基础

### 一、Java 数据类型

#### 1.1 Java 基本类型

也称原始类型：`short`；`long`；`int`；`float`；`double`；`byte`；`boolean`；`char`

#### 1.2 Java 引用类型

引用类型包括：类 + 接口 + 数组

#### 1.3 " == "与 equals 的区别

##### 1.3.1 数值、内存地址比较

###### 1.3.1.1 对于基本数据类型

* " == "比较的是他们的数值
* equals 方法不能用于基本数据类型，只能用于类变量，对于基本数据类型要用其包装类，比较的是数值

###### 1.3.1.2 对于引用类型

* " == "比较的是他们在内存中存放的地址（堆地址）
* 默认情况（即没有覆盖方法）下，equals 方法都是调用 Object 类的 equals 方法，而 Object 的 equals 方法主要用于判断对象的内存地址引用是不是同一个地址（是不是同一个对象），这时，equals 和" == "是等效的。
  要是类中覆盖了 equals 方法，则根据具体的代码来确定其作用，覆盖后一般都是通过对象的内容是否相等来判断对象是否相等。

###### 1.3.1.3 案例分析

````java
int int1 = 12;
Integer integer1 = new Integer(12);
int1 == integer1 // true
// 因为Integer会自动拆箱为int，所以为true
````

###### 1.3.1.4 浮点类型数值比较

浮点数之间的等值判断，基本数据类型不能用 == 来比较，包装数据类型不能用 equals 来判断。

````java
// 反例：
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
if (a == b) {
    // 预期进入此代码快，执行其它业务逻辑
    // 但事实上 a==b 的结果为 false
}
Float x = Float.valueOf(a);
Float y = Float.valueOf(b);
if (x.equals(y)) {
    // 预期进入此代码快，执行其它业务逻辑
    // 但事实上 equals 的结果为 false
} 
````

````java
// 正例：
// (1) 指定一个误差范围，两个浮点数的差值在此范围之内，则认为是相等的
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
float diff = 1e-6f;
if (Math.abs(a - b) < diff) {
    System.out.println("true");
}
// (2) 使用 BigDecimal 来定义值，再进行浮点数的运算操作
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");
BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);
if (x.equals(y)) {
    System.out.println("true");
}
````

###### 1.3.1.5 double类型转化BigDecimal对象

为了防止精度损失，禁止使用构造方法 BigDecimal(double) 的方式把 double 值转化为 BigDecimal 对象。因为 BigDecimal(double) 存在精度损失风险，在精确计算或值比较的场景中可能会导致业务逻辑异常。

````java
// 反例：这里的实际存储值为：0.10000000149
BigDecimal g = new BigDecimal(0.1f);
````

````java
/* 
 * 正例：使用入参为 String 的构造方法，或使用 BigDecimal 的 valueOf 方法
 * 此方法内部其实执行了 Double 的 toString，
 * 而 Double 的 toString 按 double 的实际能表达的精度对尾数进行了截断。
 */
BigDecimal recommend1 = new BigDecimal("0.1");
BigDecimal recommend2 = BigDecimal.valueOf(0.1);
````

##### 1.3.2 运行效率比较

" == "比 equals 方法运行速度快，因为" == "只是比较的引用。

##### 1.3.3. hashCode 补充说明

hashCode() 方法返回的就是一个数值，即生成一个 hash 码。hash 码的主要用途就是在对对象进行散列的时候作为 key 输入（每个对象的 hash 码不同），因此用于保证集合中元素的唯一性（set）或是 key 的唯一性（map）实现。
**（set 重复元素的判断正是基于 map 的 key 重复性判断，map 重写了 Object 的 hashCode() 和 equals() 方法，添加元素重复性校验的标准就是先取 hashCode 值，后判断 equals()）**

````java
public class PersonEntity {
    public PersonEntity(String name, int age) {
        this.name = name;
        this.age = age;
    }

    private String name;
    public int age;

    //......

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PersonEntity that = (PersonEntity) o;
        return age == that.age &&
                Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
````

##### 1.3.3 总结

1. 如果两个对象 equals 相等，他们的 hashCode 一定相等

2. 如果两个对象 hashCode 相等，他们 equals 不一定相等

3. 如果两个对象 hashCode 不相等，他们 equals 一定不相等

   （hashCode 速度较快，先执行，但是如果相等的话，不能准确判断最终结果，所以继续通过 equals 进行判断）

4. 若重写 equals(Object obj) 方法，就有必要重写  hashcode() 方法 —— 确保通过 equals(Object obj) 方法判断结果为 true 的两个对象具备相等的 hashcode() 返回值

5. 如果两个对象不相同，他们的 hashcode 可能相同

### 二、Java 字符串类

#### 2.1 特殊的 String 类

1. 虽然是引用类型，但是可以像基础数据类型一样直接赋值（`String s = "";`）
2. 不可被继承，String 类被`final`修饰

#### 2.2 Java 的字符串类比较

String、StringBuffer、StringBuilder

1. 执行速度比较（例如字符串的组合速度）：**StringBuilder > StringBuffer > String**
2. StringBuilder -- 非线程安全；StringBuffer -- 线程安全
   （因为 StringBuffer 为保证线程安全而牺牲了性能，所以当我们在不用考虑线程的情况下，尽量使用 StringBuilder）

#### 2.3 字符串类比较总结

* 如果要操作少量的数据 → String
* 不考虑线程安全处理字符串缓冲区下大量数据 → StringBuilder
* 需考虑线程安全处理字符串缓冲区下大量数据 → StringBuffer

#### 2.4 Java 常量池与 String 类

Java 中的常量池实际上分为两种形态：静态常量池和运行时常量池。
静态常量池即是 *.class 文件中的常量池，用来存放方法名称、字段名称等。
运行时常量池则是我们常说的常量池，用来存放常量（final 关键字修饰的变量）和字符串等，避免频繁的创建和销毁对象而影响系统性能，实现了对象的共享（即大家用的都是同一个东西）。

#### 2.5 字符串 String 常用方法

```java
// 返回指定字符第一次出现的字符串内的索引，以指定的索引开始搜索
String.indexOf(int ch, int fromIndex);
// int

// 返回指定子字符串第一次出现的字符串内的索引
String.indexOf(String str);
// int

// 返回指定子串的第一次出现的字符串中的索引，从指定的索引开始
String.indexOf(String str, int fromIndex);
// int


// 返回指定字符的最后一次出现的字符串中的索引
String.lastIndexOf(int ch);
// int

// 返回指定字符的最后一次出现的字符串中的索引，从指定的索引开始向后搜索
String.lastIndexOf(int ch, int fromIndex);
// int

// 返回指定子字符串最后一次出现的字符串中的索引
String.lastIndexOf(String str);
// int

// 返回指定子字符串的最后一次出现的字符串中的索引，从指定索引开始向后搜索
String.lastIndexOf(String str, int fromIndex);
// int

// 字符串是否匹配给定的正则表达式
String.matches(String regex);
// boolean

// 返回从替换所有出现的导致一个字符串 oldChar在此字符串 newChar
String.replace(char oldChar, char newChar);
// String

// 将与字面目标序列匹配的字符串的每个子字符串替换为指定的字面替换序列
String.replace(CharSequence target, CharSequence replacement);
// String

// 用给定的替换替换与给定的 regular expression匹配的此字符串的每个子字符串
String.replaceAll(String regex, String replacement);
// String

// 用给定的替换替换与给定的 regular expression匹配的此字符串的第一个子字符串
String.replaceFirst(String regex, String replacement);
// String

// 将此字符串分割为给定的 regular expression的匹配
String.split(String regex);
// String[]

// 将这个字符串拆分为给定的 regular expression的匹配
String.split(String regex, int limit);
// String[]

// 返回一个字符串，该字符串是此字符串的子字符串
String.substring(int beginIndex);
// String

// 返回一个字符串，该字符串是此字符串的子字符串
String.substring(int beginIndex, int endIndex);
// String
```

**案例分析**

````java
String s1 = "Hello";
String s2 = new String("Hello");
String s3 = s2.intern();
String s4 = "Hel" + "lo";
String sa = "Hel";
String sb = "lo";
String s5 = sa + sb;

System.out.println(s1 == s2);// false
System.out.println(s1 == s3);// true
System.out.println(s1 == s4);// true
System.out.println(s1 == s5);// false
````

* String 的 intern() 方法会查找在常量池中是否存在一份 equal 相等的字符串，如果有则返回该字符串的引用，如果没有则添加自己的字符串进入常量池。
  (因为 s1 已经在常量池中创建了"Hello"字符串，所以 s2.intern() 的时候就去常量池中查找是否已经存在"Hello"，存在则直接=原来的字符串，不存在则是一个新的存在，所以 s3=s1)

* 虽然 s4 是动态拼接出来的，但所有参与拼接的部分都是已知的字面量，在编译期间，这种拼接会被优化，编译器直接帮你拼好，因此相等。
  （只有使用这种全部带引号、使用“+”连接产生的新字符串对象才会被加入字符串池中）
  （s4 在初始化的过程中，产生了一个或〇个对象，若常量池中早已存在该字符串，则直接指向常量池，否则使用字符串拼接优化，产生一个 StringBuffer 对象进行拼接，然后 toString()，存放到常量池中）

  ````java
  String s4 = new StringBuffer().append("Hel").append("lo").toString()
  ````

* 虽然 s5 也是拼接，但 sa 和 sb 作为两个变量，都是不可预料的，并不会被优化，会存放到堆中。

### 三、Java 集合

#### 3.1 Collection 和 Collections

##### 3.1.1 Collection

**Collection 是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。**Collection 接口在 Java 类库中有很多具体的实现。Collection 接口的意义是为各种具体的集合提供最大化的统一操作方式。
`Collection = List + Set`

##### 3.1.2 Collections

**Collections 是一个包装类。它包含各种有关集合操作的静态多态方法。**此类不能实例化，就像一个工具类，服务于 Java 的 Collection 框架。

示例：

````java
Collections.sort(list);// 排序(sort)
Collections.shuffle(list);// 乱序(shuffling)
````

#### 3.2 Java 集合类

![1562914344229](.\assets\1562914344229.png)

<table border="0" cellpadding="0" cellspacing="0" style="border-collapse:collapse; table-layout:fixed; width:100%">
    <tbody>
        <tr height="19" style="height:14.25pt">
            <td height="19" class="xl66" width="88" style="height:14.25pt; width:66pt">
                比较
            </td>
            <td class="xl66" width="218" style="width:164pt">
                List
            </td>
            <td class="xl66" width="257" style="width:193pt">
                Set
            </td>
            <td class="xl66" width="215" style="width:161pt">
                Map
            </td>
        </tr>
        <tr height="19" style="height:14.25pt">
            <td height="19" class="xl66" style="height:14.25pt">
                继承接口
            </td>
            <td class="xl66">
                Collection
            </td>
            <td class="xl66">
                Collection
            </td>
            <td class="xl66">
            </td>
        </tr>
        <tr height="38" style="height:28.5pt">
            <td height="38" class="xl66" style="height:28.5pt">
                常见实现类
            </td>
            <td class="xl67" width="218" style="width:164pt">
                AbstractList：
                <br>
                ArrayList；LinkedList；Vector
            </td>
            <td class="xl67" width="257" style="width:193pt">
                AbstractSet：
                <br>
                HashSet；LinkedHashSet；TreeSet
            </td>
            <td class="xl66">
                HashMap；HashTable
            </td>
        </tr>
        <tr height="76" style="height:57.0pt">
            <td height="76" class="xl66" style="height:57.0pt">
                常见方法
            </td>
            <td class="xl67" width="218" style="width:164pt">
                add()；remove()；clear()；
                <br>
                get()；contains()；size()
            </td>
            <td class="xl67" width="257" style="width:193pt">
                add()；remove()；clear()；
                <br>
                contains()；size()
            </td>
            <td class="xl67" width="215" style="width:161pt">
                put()；get()；containsKey()；
                <br>
                containsValue()；remove()；
                <br>
                clear()；keySet()；values()；
                <br>
                size()
            </td>
        </tr>
        <tr height="19" style="height:14.25pt">
            <td height="19" class="xl66" style="height:14.25pt">
                元素
            </td>
            <td class="xl66">
                可重复
            </td>
            <td class="xl66">
                <span style="font-variant-ligatures: normal; font-variant-caps: normal; orphans: 2; white-space:pre-wrap; widows: 2; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial">不可重复（用equals()判断）
                </span>
            </td>
            <td class="xl66">
                key不可重复，value可重复
            </td>
        </tr>
        <tr height="19" style="height:14.25pt">
            <td height="19" class="xl66" style="height:14.25pt">
                顺序
            </td>
            <td class="xl66">
                有序
            </td>
            <td class="xl66">
                无序（实际上由HashCode决定）
            </td>
            <td class="xl66">
            </td>
        </tr>
        <tr height="19" style="height:14.25pt">
            <td height="19" class="xl66" style="height:14.25pt">
                线程安全
            </td>
            <td class="xl66">
                Vector线程安全
            </td>
            <td class="xl66">
            </td>
            <td class="xl66">
                HashTable线程安全
            </td>
        </tr>
		<tr height="0" style="display:none">
			<td width="88" style="width:66pt">
			</td>
			<td width="218" style="width:164pt">
			</td>
			<td width="257" style="width:193pt">
			</td>
			<td width="215" style="width:161pt">
			</td>
		</tr>
    </tbody>
</table>

#### 3.3 List 集合

##### 3.3.1 ArrayList vs LinkedList

ArrayList 是实现了基于动态数组的数据结构；LinkedList 是基于链表结构，带有两个指针，分别指向上一个节点和下一个节点。

ArrayList 增删慢，查询快；LinkedList 增删快，查询慢（LinkedList 增删只用修改两个指针，花销固定，因而更快）

##### 3.3.2 ArrayList 和数组转换

###### 3.3.2.1 ArrayList 转数组

````java
List<String> list = new ArrayList<>(2);
list.add("guan");
list.add("bao");
String[] array = list.toArray(new String[0]);
````

使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入类型完全一致、长度为 0 的空数组。
因为直接使用 toArray 的无参方法存在问题，此方法返回值只能是 Object[] 类型的数组，若强转其它类型数组将出现 ClassCastException 错误。

**ArrayList 转数组后，ArrayList 继续添加元素不会影响数组；ArrayList 修改元素不会影响数组。**

###### 3.3.2.2 数组转 ArrayList

````java
String[] str = new String[] { "yang", "hao" };
List list = Arrays.asList(str);
````

使用工具类 Arrays.asList() 把数组转换成集合时，不能使用其修改集合相关的方法，它的 add/remove/clear 方法会抛出 UnsupportedOperationException 异常。
因为 asList 的返回对象是一个 Arrays 内部类，并没有实现集合的修改方法。Arrays.asList 体现的是适配器模式，只是转换接口，后台的数据仍是数组。

**数组转 ArrayList 后，数组元素的修改会影响到 ArrayList。**

#### 3.4 Set 集合

##### 3.4.1 Set 集合去重原则

Set 集合存储系统类对象可以不用考虑去重原理，这里针对实体类对象进行讨论。
实体类对象的去重需要重写 equals() 和 hashCode() 方法。
当添加一个实体类到 set 集合时，会先执行 hashCode() 方法进行区分比较，默认的 hashCode() 方法是比较实体类对应的哈希值的，可以理解为对象在内存中的地址。
所以可以重写 hashCode() 方法中哈希值的生成逻辑，能加快去重的速度。
当 hashCode() 方法所比较的 hashCode() 相同的时候，才会继续调用 equals() 方法进行比较，如果还是相同，则不会存入到 set 集合中。

##### 3.4.2 Set vs List

* List 是有序且重复的
* HashSet 是无序不重复的
* TreeSet 能保证元素的自然顺序（a-z 排序）
* LinkedHashSet 能保证元素的添加顺序

#### 3.5 Map 集合

##### 3.5.1 HashMap vs HashTable

<table border="0" cellpadding="0" cellspacing="0" style="border-collapse: collapse; table-layout: fixed; width: 100%">
	<tbody>
		<tr height="17" style="height: 12.75pt">
			<td height="17" class="xl65" width="79" style="height: 12.75pt; width: 59pt">
			</td>
			<td class="xl65" width="284" style="width: 213pt">
				HashMap
			</td>
			<td class="xl65" width="284" style="width: 213pt">
				HashTable
			</td>
		</tr>
		<tr height="51" style="height: 38.25pt">
			<td height="51" class="xl65" style="height: 38.25pt">
				<font class="font7">
					线程安全
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				<font class="font7">
					函数非同步
					<br>
					非线程安全
					<br>
					多线程要额外同步处理
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				<font class="font7">
					几乎所有函数都是同步的
					<br>
					线程安全的
					<br>
					支持多线程
				</font>
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td height="17" class="xl65" style="height: 12.75pt">
				<font class="font7">
					存储
				</font>
				<font class="font6">
					null
				</font>
				<font class="font7">
					值
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				key
				<font class="font7">
					和
				</font>
				<font class="font6">
					value
				</font>
				<font class="font7">
					都可以为
				</font>
				<font class="font6">
					null
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				key
				<font class="font7">
					和
				</font>
				<font class="font6">
					value
				</font>
				<font class="font7">
					都不可以为
				</font>
				<font class="font6">
					null
				</font>
			</td>
		</tr>
		<tr height="34" style="height: 25.5pt">
			<td height="34" class="xl65" style="height: 25.5pt">
				<font class="font7">
					遍历种类
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				<font class="font7">
					只支持
				</font>
				<font class="font6">
					Iterator
				</font>
				<font class="font7">
					（迭代器）遍历
				</font>
			</td>
			<td class="xl67" width="284" style="width: 213pt">
				<font class="font7">
					支持
				</font>
				<font class="font6">
					Iterator
				</font>
				<font class="font7">
					（迭代器）和
				</font>
				<font class="font6">
					Enumeration
				</font>
				<font class="font7">
					（枚举器）遍历
				</font>
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td height="17" class="xl65" style="height: 12.75pt">
				<font class="font7">
					遍历顺序
				</font>
			</td>
			<td class="xl65">
				Iterator
				<font class="font7">
					（迭代器）
				</font>
				<font class="font6">
					“
				</font>
				<font class="font7">
					从前向后
				</font>
				<font class="font6">
					”
				</font>
				<font class="font7">
					地遍历数组
				</font>
			</td>
			<td class="xl65">
				Iterator
				<font class="font7">
					（迭代器）
				</font>
				<font class="font6">
					“
				</font>
				<font class="font7">
					从后往前
				</font>
				<font class="font6">
					”
				</font>
				<font class="font7">
					地遍历数组
				</font>
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td colspan="3" height="17" class="xl68" width="647" style="height: 12.75pt; width: 485pt">
				继承和实现不同
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td colspan="3" height="17" class="xl68" width="647" style="height: 12.75pt; width: 485pt">
				容量的初始值和增加方式不同
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td colspan="3" height="17" class="xl69" width="647" style="height: 12.75pt; width: 485pt">
				<font class="font8">
					添加
				</font>
				<font class="font6">
					key-value
				</font>
				<font class="font8">
					时的
				</font>
				<font class="font6">
					hash
				</font>
				<font class="font8">
					值算法不同
				</font>
			</td>
		</tr>
		<tr height="17" style="height: 12.75pt">
			<td colspan="3" height="17" class="xl69" width="647" style="height: 12.75pt; width: 485pt">
				<font class="font8">
					部分
				</font>
				<font class="font6">
					API
				</font>
				<font class="font8">
					不同
				</font>
			</td>
		</tr>
		<tr height="0" style="display: none">
			<td width="79" style="width: 59pt">
			</td>
			<td width="284" style="width: 213pt">
			</td>
			<td width="284" style="width: 213pt">
			</td>
		</tr>
	</tbody>
</table>

##### 3.5.2 Map 集合存储 null 情况

| 集合类            | Key           | Value         | Super       | 说明                   |
| ----------------- | ------------- | ------------- | ----------- | ---------------------- |
| HashTable         | 不允许为 null | 不允许为 null | Dictionary  | 线程安全               |
| ConcurrentHashMap | 不允许为 null | 不允许为 null | AbstractMap | 锁分段技术（JDK8:CAS） |
| TreeMap           | 不允许为 null | 允许为 null   | AbstractMap | 线程不安全             |
| HashMap           | 允许为 null   | 允许为 null   | AbstractMap | 线程不安全             |

##### 3.5.3 Map 集合遍历

当循环中只需要获取 Map 的主键 key 时，迭代 keySet() 是正确的；但是，当需要主键 key 和取值 value 时，迭代 entrySet() 才是更高效的做法，其比先迭代 keySet() 后再去通过 get 取值性能更佳。

反例：

````java
// Map 获取value 反例:
HashMap<String, String> map = new HashMap<>();
for (String key : map.keySet()){
    String value = map.get(key);
}
````

正例：

````java
// Map 获取key & value 正例:
HashMap<String, String> map = new HashMap<>();
for (Map.Entry<String,String> entry : map.entrySet()){
    String key = entry.getKey();
    String value = entry.getValue();
}

// JKD8 遍历 Map
map.forEach((k,v) -> System.out.println(k + "---" + v));
// 拓展：List<Map<String, Object>>遍历
list.forEach(map -> map.forEach((k, v) -> System.out.println(k + "---" + v)));
````

##### 3.5.4 ConcurrentHashMap

由于`HashMap`是线程不安全的，可以采用带有分段锁的`ConcurrentHashMap`
*（Java8 实现 —— 引入了红黑树）*

###### 3.5.4.1 HashMap 结构（桶）

HashMap 采取数组加链表的存储方式，即数组（散列桶）中每一个元素都是链表的一个条目（Entry）。

![1562914975790](.\assets\1562914975790.png)

例如：

创建一个初始容量为 16，加载因子为 1.5F 的 HashMap。
根据 threshold = (capacity x loadFactor)，
HashMap 的实际容量是根据桶的数量乘以加载因子，所以这个 HashMap 的实际容量为 24。
因此可以知道有桶存放了两个或以上的元素，而元素根据计算的 hash 值来存放桶。
*（其实元素的 hash 值是不相等的，只是运算后桶的位置相同，所以被放在同一个桶中，形成链表或红黑树）*

![1562915108321](.\assets\1562915108321.png)

###### 3.5.4.2 ConcurrentHashMap 处理高并发

每个分段就是一个小小的线程安全的 HashTable
分段锁的意思是：只有在同一个分段内才存在竞态关系，不同的分段锁之间没有锁竞争。
相比于对整个 Map 加锁的设计，分段锁能够大大的提高了高并发环境下的处理能力。

###### 3.5.4.3 ConcurrentHashMap vs HashMap

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

#### 3.6 集合关系思维导图

![1562914826916](.\assets\1562914826916.png)

### 四、Java 关键字

#### 4.1 final，finally，finalize

##### 4.1.1 final

* final 修饰类变成最终类，该类不可被继承；
* final 修饰方法，该方法不可重写；
* final 修饰变量变成常量，常量值初始化之后就不能被修改

##### 4.1.2 finally

finally 与 try-catch 语法搭配使用，用来进行类似资源释放、保证解锁等动作。

##### 4.1.3 finalize

finalize() 是 Object 类的一个方法，跟 JVM 的垃圾回收机制有关。

#### 4.2 public，protected，default，private

| 作用域    | 当前类 | 当前包 | 子孙类 | 其他包 |
| --------- | ------ | ------ | ------ | ------ |
| public    | √      | √      | √      | √      |
| protected | √      | √      | √      | ×      |
| default   | √      | √      | ×      | ×      |
| private   | √      | ×      | ×      | ×      |

### 五、查漏补缺

#### 5.1 Java 对象初始化顺序

````java
public class Son extends Father {
    // <7>子类构造代码块1
    // <2>子类静态代码块（子类实例化前会先实例化父类）
    // <8>子类构造代码块2
    // <9>子类构造方法
    public static void main(String[] args) {
        // <3>子类main方法打印："11"
        System.out.println("11");
        Son son = new Son();
        // <10>子类main方法打印："22"
        System.out.println("22");
    }
}

class Father {
    // <4>父类构造代码块1
    // <1>父类静态代码块
    // <5>父类构造代码块2
    // <6>父类构造方法
}
````

<table border="0" cellpadding="0" cellspacing="0" width="100%">
	<tbody>
		<tr height="32" style="height:24.0pt">
			<td height="32" class="xl66" width="307" style="height:24.0pt;width:230pt">
				<font class="font7">
					①父类静态代码块
					<br>
					②子类静态代码块（子类实例化前会先实例化父类）
				</font>
			</td>
		</tr>
		<tr height="17" style="height:12.75pt">
			<td height="17" class="xl67" style="height:12.75pt">
				<font class="font7">
					③子类
				</font>
				<font class="font6">
					main
				</font>
				<font class="font7">
					方法打印：
				</font>
				<font class="font6">
					"11"
				</font>
			</td>
		</tr>
		<tr height="50" style="height:37.5pt">
			<td height="50" class="xl66" width="307" style="height:37.5pt;width:230pt">
				<font class="font7">
					④父类构造代码块
				</font>
				<font class="font6">
					1
					<br>
				</font>
				<font class="font7">
					⑤父类构造代码块
				</font>
				<font class="font6">
					2
					<br>
				</font>
				<font class="font7">
					⑥父类构造方法
				</font>
			</td>
		</tr>
		<tr height="50" style="height:37.5pt">
			<td height="50" class="xl66" width="307" style="height:37.5pt;width:230pt">
				<font class="font7">
					⑦子类构造代码块
				</font>
				<font class="font6">
					1
					<br>
				</font>
				<font class="font7">
					⑧子类构造代码块
				</font>
				<font class="font6">
					2
					<br>
				</font>
				<font class="font7">
					⑨子类构造方法
				</font>
			</td>
		</tr>
		<tr height="17" style="height:12.75pt">
			<td height="17" class="xl67" style="height:12.75pt">
				<font class="font7">
					⑩子类
				</font>
				<font class="font6">
					main
				</font>
				<font class="font7">
					方法打印：
				</font>
				<font class="font6">
					"22"
				</font>
			</td>
		</tr>
		<tr height="0" style="display:none">
			<td width="307" style="width:230pt">
			</td>
		</tr>
	</tbody>
</table>

#### 5.2 抽象类 vs 接口

* 抽象类和接口都不能直接实例化，只能实例化其继承并实现了所有抽象方法的子类，或接口实现类
* 有抽象方法的一定是抽象类，但抽象类不一定含有抽象方法
* 抽象类中的变量可以是普通变量，但接口中的变量必须是**公共的静态的常量**
* 子类可以实现多个接口，但抽象类只能单继承

#### 5.3 Java 反射机制

Java 正射：当我们需要使用某个类时，必定知道它是什么类，是用来做什么的。于是我们直接对这个类进行实例化，之后使用这个类对象进行操作。

````java
Phone phone = new Phone();
phone.setPrice(4);
````

Java 反射：与「正射」相反，我们一开始并不知道要初始化的类对象是什么，自然也无法使用 new 关键字来创建对象了。这时可以**通过 JVM 中的 class 对象，反向获取对象的属性、方法等各种信息。**

````java
// 这里与直接new初始化，然后调用方法是一样的效果
Class clz = Class.forName("com.xxp.reflect.Phone");
Method method = clz.getMethod("setPrice", int.class);
Constructor constructor = clz.getConstructor();
Object object = constructor.newInstance();
method.invoke(object, 4);
````

Java 类的加载过程：

1. 我们在 new 一个 class 类的时候，Java 会将新的 .java 文件编译成 .class 文件
2. 当我们实例化一个对象`Student s1 = new Student();`的时候，JVM 会找到`Student.class`文件，并加载到内存（堆）中
3. 把堆的对象地址加到栈中（引用）。Java 反射的本质就是得到 class 对象后，反向获取 Student 对象的各种信息（变量，方法等）

代码理解：

````java
Class<?> cls1 = new Student().getClass();
System.out.println(cls1.getName());// 输出类的具体路径

Class<?> cls2 = Student.class;
System.out.println(cls2.getName());// 输出类的具体路径

Class<?> cls3 = Class.forName("mmc.liang.test.Student");// 通过类的具体路径，取得class对象
System.out.println(cls3.getName());

// 这里注意的一个点是，实例化的类要存在一个无参的构造方法
Object obj = cls3.newInstance();// 实例化对象，和使用关键字new一样
Student stu = (Student) obj;
System.out.println(stu.getStuName());// 实例化后才能使用类方法
````

实际上 new 是造成耦合的关键元凶，因此在工厂模式中配合 Java 的反射特性，可以很好地实现解耦。

#### 5.4 单例模式

##### 5.4.1 使用场景

一个全局使用的类频繁地创建与销毁，需要控制新建实例的数目，以节省系统资源的时候。

##### 5.4.2 注意事项

* 单例类只能有一个实例。
* 单例类必须自己创建自己的唯一实例。
* 单例类必须给所有其他对象提供这一实例。

##### 5.4.3 饿汉模式

````java
public class HungerSingleton {
	private static HungerSingleton singleton = new HungerSingleton();
	
	public static HungerSingleton getInstance() {
		return singleton;
	}
	
	private HungerSingleton() {}
}
````

##### 5.4.4 懒汉模式

*（这里注意判断两次 single 是否 == null，同时中间使用`synchronized(obj)`锁定资源，最后创建新的单例对象）*

````java
public class LazySingleton {
	private static LazySingleton singleton = null;
	private static Object obj = new Object();
	
	public static LazySingleton getInstance() {
		if (singleton == null) {
			synchronized (obj) {
				if (singleton == null) {
					singleton = new LazySingleton();
				}
			}
		}
		return singleton;
	}
	
	private LazySingleton() {}
}
````

#### 5.5 浅拷贝与深拷贝

![29032541](.\assets\29032541.jpg)

##### 5.5.1 浅拷贝

浅拷贝会对“主”对象进行拷贝，但不会复制主对象里面的对象。“里面的对象”在原来的对象和它的副本之间共享。这时修改浅拷贝对象的变量值，“主”对象对应的变量值也会一起改变。
（简单来说浅拷贝就是复制了“主”对象对堆的引用）

![29032542](.\assets\29032542.jpg)

##### 5.5.2 深拷贝

深拷贝是一个**整个独立的对象拷贝。**如果我们对整个 Person对象进行深拷贝，我们会对整个对象的结构都进行拷贝。
（简单来说深拷贝就是复制了“主”对象引用的堆中的值）

![29032543](.\assets\29032543.jpg)

## JVM 知识

### 一、JDK，JRE，JVM

![1557709091739](.\assets\1557711983254.png)


JDK（Java development toolkit）——相当于是 Java 的库函数，是编译、运行 Java 程序的工具包，是一切 Java 应用程序的基础，所有 Java 应用程序是构建在这个之上的。（汽车）

JRE（Java Runtime Environment）——Java 运行环境，也就是 Java 平台。所有的 Java 程序都要在 JRE 下才能运行。JDK 的工具也是 Java 程序，也需要 JRE 才能运行。（汽车动力系统）

JVM（Java Virtual Machine）——Java 虚拟机，是 JRE 的一部分。它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。JVM 有自己完善的硬件架构，如处理器、堆栈、寄存器等，还具有相应的指令系统。Java 语言最重要的特点就是跨平台运行。使用 JVM 就是为了支持与操作系统无关，实现跨平台。（汽车轮胎）

### 二、JVM 虚拟机

#### 2.1  JVM 基本结构

![1557710434221](.\assets\b2e6a2272094c3a0.png)

![1557728246480](.\assets\2c33c8b5415c6652.png)

#### 2.2 JVM 线程共享区和非线程共享区 

概括来说，**JVM 初始运行的时候都会分配好 Method Area（方法区） 和 Heap（堆） ，而 JVM 每遇到一个线程，就为其分配一个 Program Counter Register（程序计数器） , VM Stack（虚拟机栈）和Native Method Stack（本地方法栈），当线程终止时，三者（虚拟机栈，本地方法栈和程序计数器）所占用的内存空间也会被释放掉**。

非线程共享的三个区域的生命周期与所属线程相同，而线程共享的区域与 JAVA 程序运行的生命周期相同，所以这也是系统垃圾回收的场所只发生在线程共享的区域（实际上对大部分虚拟机来说只发生在 Heap（堆区）上）的原因。

案例：

```java
public class AppMain {
    /**
     * 运行时，JVM把AppMain的信息都放到方法区
     * main方法本身也放入方法区内
     * @param args
     */
    public static void main(String[] args) {
        // test1是引用，所以放到栈区中，而Sample是自定义对象，会被放到堆里
        Sample test1 = new Sample("测试1");
        Sample test2 = new Sample("测试2");

        test1.printName();
        test2.printName();
    }
}

class Sample {
    private String name;

    public Sample(String name) {
        this.name = name;
    }

    /**
     * printName方法本身放到方法区中
     */
    public void printName() {
        System.out.println(name);
    }
}
```

![1557710578795](.\assets\1557710578795.png)

#### 2.3 JVM 启动流程（略）

① Java 虚拟机启动的命令是通过 java + xxx(类名，这个类中要有 main 方法)或者 javaw 启动的。

② 执行命令后，系统第一步做的就是装载配置，会在当前路径中寻找 JVM 的 config 配置文件。

③ 找到 JVM 的 config 配置文件之后会去定位 jvm.dll 这个文件。这个文件就是 Java 虚拟机的主要实现。

④ 当找到匹配当前版本的 jvm.dll 文件后，就会使用这个 dll 去初始化 JVM 虚拟机。获得相关的接口。之后找到 main 方法开始运行。

### 三、Java 执行过程

#### 3.1 解释执行和编译执行

解释执行：一边将程序翻译成计算机可以执行的指令，一边交给计算机执行，翻译一句，执行一句。*（饭馆点菜，上一个吃一个）*

编译执行：将整个程序翻译成计算机及可以理解的指令，然后交给计算机执行。*（饭馆订桌，菜上齐后一并吃）*

#### 3.2 Java 的执行过程

Java 严格来讲是一种“半解释半编译”的语言

User.java

↓*（javac源码编译器解释执行，即静态编译）*

User.class 字节码（ByteCode），ByteCode 是 JVM 唯一能够识别的指令，JVM 将 ByteCode 翻译成真正能够执行的机器码
**（PS：在这一步同时还会有一些对我们编写的 Java 源码的优化操作）**

↓*（JVM 编译执行）*

机器码（machine code），这是电脑 CPU 可直接解读的数据，JVM 在不同的硬件平台上有不同实现，以达到所谓“一次编写，到处运行”的目标

#### 3.3 Java 运行比 C++ 慢？

这种说法是在 **JIT 编译器**出现之前的情况。

由于 Java 程序最初是仅仅通过解释器解释执行，即对字节码逐条解释执行，这种方式的执行速度相对会比较慢，**尤其当某个方法或代码块运行的特别频繁时，这种方式的执行效率就显得很低**。于是后来在虚拟机中引入了**JIT 编译器（即时编译器，即时编译被内嵌于 Java 字节码执行引擎之中，可以算的上是 jvm 的一个内存组件）**，当虚拟机发现某个方法或代码块运行特别频繁时，就会把这些代码认定为“Hot Spot Code”（热点代码），为了提高热点代码的执行效率，在运行时，虚拟机将会把这些代码编译成与本地平台相关的机器码，并进行特别的优化，完成这项任务的正是 JIT 编译器。

#### 3.4 JIT 编译器中的 C1、C2 即时编译器

* C1 编译：将字节码编译为本地代码，进行简单、可靠的优化，如有必要将加入性能监控的逻辑。
* C2 编译：也是将字节码编译为本地代码，但是会启用一些编译耗时较长的优化，**甚至会**根据性能监控信息进行一些不可靠的**激进优化**。C2 编译主要是针对**热点代码**，对之做出更优的编译。

*注：实施分层编译后，C1 和 C2 将会同时工作，许多代码会被**多次编译**，用 C1 获取更高的编译速度，用 C2 来获取更好的编译质量，且在解释执行的时候解释器也无须再承担收集性能监控信息的任务*

### 四、新生代、老年代、JavaCG 回收机制

Java 中的堆是 JVM 所管理的最大的一块内存空间，主要用于存放各种类的实例对象。

堆的内存模型大致为：

![1557716568296](.\assets\1557716568296.png)

从图中可以看出： 堆大小 = 新生代 + 老年代。其中，堆的大小可以通过参数 –Xms、-Xmx 来指定。

#### 4.1 新生代和老年代

默认的，新生代 ( Young ) 与老年代 ( Old ) 的比例的值为 1:2。其中，新生代 ( Young ) 被细分为 **Eden** 和 两个 Survivor 区域，这两个 Survivor 区域分别被命名为 **from** 和 **to**，以示区分。

*注：JVM 每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。因此，新生代实际可用的内存空间为 9/10 ( 即90% )的新生代空间。（ from 和 to 区域位置是平等的）*

#### 4.2 JavaCG 垃圾回收机制

* 对象会首先在 Eden 出生，同时还有一个 Survivor 区域在使用（from）

* 经过一次 Minor GC 后，如果对象还存活，并且能够被另外一块 Survivor 区域（to）所容纳，则使用复制算法，将这些还存活的对象复制到另外一块 Survivor 区域（to）中，并且将这些对象的年龄设置为1，然后清空所使用过的 Eden 和 from 区

* 之后对象在 Survivor 区域中每经过一次 Minor GC，年龄+1，当对象的年龄达到某个值时（默认是 15 岁，可通过参数设置），这些对象就会成为老年代。

  *注：对于一些较大的对象 ( 即需要分配一块较大的连续内存空间 )， 则是直接进入到老年代*

Full GC 是发生在**老年代**的垃圾收集动作，所采用的是**标记-清除算法**。

由于老年代的对象都是比较稳定的**热点代码**，所以不会那么容易被回收。因此，**Full GC 发生的次数不会有 Minor GC 那么频繁，并且做一次 Full GC 要比进行一次 Minor GC 的时间会更长。**

*另外，**标记-清除算法收集垃圾的时候会产生许多的内存碎片** ( 即不连续的内存空间 )，此后需要为较大的对象分配内存空间时，若无法找到足够的连续的内存空间，就会提前触发一次 GC 的收集动作。*

## 开源框架知识

### 一、Tomcat

### 二、Spring

#### 2.1 关于 Spring 框架

Spring 是一个轻量级的 Java 开发框架，其核心是 Bean 工厂，我们将对象之间的依赖关系交给 Spring，从而降低组件之间的耦合关系

#### 2.2 Spring 框架七大模块：

##### 2.2.1 Core 封装包

提供 IOC 特性（BeanFactory 工厂模式），用于构造所需的 M（Model）
（将类的创建和依赖关系写在配置文件中，由配置文件注入实现了松耦合）

##### 2.2.2 Context 封装包

构建于 Core 封装包基础上，提供了一种框架式的对象访问方式
（比如说通过 Servlet 容器，方便实现 J2EE 的应用）

##### 2.2.3 Dao 封装包

提供了 JDBC 抽象层，简化了 JDBC 编码（HQL），方便数据库开发

##### 2.5.4 ORM 封装包

即“对象关系映射”集成，提供简单的声明式事务管理（包括 Hibernate 等）

##### 2.5.5 AOP 封装包

即“切面编程”，定义`方法拦截器`（method-interceptors）和`切点`（pointcuts），将安全、事务等程序逻辑相对独立的功能抽取出来，利用 Spring 的配置文件将这些功能插进去，实现了按照方面编程，减弱了代码功能的耦合，提高了复用性

##### 2.5.6 Web`和`Web MVC 封装包

提供了 Java Web 应用的 MVC 框架实现或者与其他流行的 Web 框架集成

#### 2.3 Spring 框架的 IOC 控制反转

Spring 的 IOC 控制反转主要强调的是程序之间的关系是由容器控制的，由容器帮我们创建对象并注入依赖对象。而反转即为，在传统的编程中都是由我们创建对象获取依赖对象的，但在 Spring 中则是通过容器帮我们查找和注入对象，所以叫反转。
这其中使用了 Java 的工厂设计模式。

Java 的工厂模式分为三种：简单工厂模式，一般工厂模式和抽象工厂模式。

总的来说，就是提供一个用于创建对象的工厂接口，让其工厂实现类去决定实例化创建哪一个产品类。

#### 2.4 Spring 框架的 AOP 切面编程

Spring 的 AOP 即是将封装好的对象剖开，找出其中对多个对象产生影响的公共行为，并将其封装为一个可重用的模块，这个模块被命名为切面（aspect），然后利用 Spring 的配置文件将这些功能插入到指定位置中。切面将那些与业务逻辑无关，却被业务模块共同调用的逻辑提取并封装起来，减少了系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。

这其中使用了 Java 的代理设计模式。

Java 的代理模式分为两种：静态代理和动态代理。

这两种模式的区别是：静态代理的代理类实现的是代理对象的接口，所以当存在多个实现方法的时候，需要一一重写。

而动态代理所实现的接口是`InvocationHandler`，传入的实现类类型和方法名，实现功能，避免了静态代理重写多个方法的缺点。

#### 2.5 AOP、拦截器和过滤器

##### 2.5.1 Spring AOP

​	Spring AOP，是 AOP 的一种实现，使用的是代理模式。

##### 2.5.2 Filter（过滤器）

​	Filter 是 J2EE 的规范，Servlet2.3 开始引入/实现的是职责链模式。Filter 可以用来设置字符集、控制权限、控制转向等等。Filter 也是 AOP 的一种实现。

##### 2.5.3 Interceptor（拦截器）

​	Interceptor 是 Struct2 中的概念。同样是 AOP 的一种实现。

##### 2.5.4 拦截器和过滤器的区别

1. 拦截器是基于 Java 的反射机制，过滤器是基于 Java 的函数回调
2. 拦截器不依赖于 servlet 容器，而过滤器依赖于 servlet 容器
3. 拦截器只能对 action 请求起作用，过滤器几乎对所有的请求起作用
4. 拦截器可以访问 action 上下文，值栈里的对象，而过滤器不能访问
5. 在 action 生命周期中，拦截器可以被多次调用，过滤器只能在 servlet 初始化时调用一次
6. 拦截器可以获取 IOC 容器中的各个 bean，过滤器不行，在拦截器中注入一个 service 可以调用逻辑业务

#### 2.6 Spring 事务

##### 2.6.1 Spring事务传播机制

从一个方法进入另外一个方法的时候，事务和另外一个方法的关系问题。
例如：
REQUIRED 就是两个方法用的都是同一个事务。
REQUIRES_NEW 就是新开一个事务。

事务的七种传播级别：

1. PROPAGATION_**REQUIRED**：
   默认的 Spring 事务传播级别，该级别的特点是：如果上下文中已经存在事务，那么就加入到事务中执行；如果当前上下文中不存在事务，则新建事务执行。该级别能满足处理大多数业务场景。
2. PROPAGATION_SUPPORTS：
   supprots，即是“支持”的意思。该级别的特点是：如果上下文存在事务，则支持事务加入事务；如果没有事务，则使用非事务的方式执行。该级别通常用来处理那些并非原子性的非核心业务逻辑操作，应用场景较少。
3. PROPAGATION_MANDATORY：
   该级别的事务要求上下文中必须要存在事务，否则抛出异常。配置该级别事务能够有效防止上下文调用代码时遗漏添加事务控制。比如一段代码不能单独被调用执行，但是一旦被调用，就必须有事务包含的情况，就可以使用这个传播级别。
4. PROPAGATION_**REQUIRES_NEW**：
   new，即是每次都要新建一个事务。该级别的特点是：每次都会新建一个事务，并且同时将上下文中的事务挂起，执行当前新建事务完成后，上下文事务恢复再执行。
   该事物用处挺大，例如当我们发红包之前，需要进行系统的初始化、验证、数据记录等操作，然后再发红包，然后记录发送日志，如果日志不准确，则整个父事务逻辑需要回滚。当配置了该事务后，发红包的子事务就不会影响到父事务的提交和回滚。
5. PROPAGATION_NOT_SUPPORTED：
   not supported，即是“不支持”的意思。该级别事务的特点是：如果上下文存在事务，则挂起事务，执行当前的逻辑，结束后再恢复上下文的事务。
   该级别事务的好处就是将事务极可能缩小，降低了事务的风险。
6. PROPAGATION_NEVER：
   该事务更加严格，要求上下文中不能存在事务，否则就会报 runtime 异常，强制停止执行。
7. PROPAGATION_NESTED：
   nested，嵌套级别事务。该级别事务的特点是：如果上下文中存在事务，则嵌套事务执行；如果不存在事务，则新建事务。
   在该级别事务中，父事务会在进入子事务之前，建立一个 save point 的回滚点，子事务会回滚到这个回滚点，父事务之前的操作不受影响；而父事务则会连带子事务一并回滚，子事务不会提交。子事务是父事务的一部分，由父事务统一提交。

##### 2.6.2 Spring事务隔离级别

1. Serializable：最严格的级别，事务串行执行，资源消耗最大。
2. REPEATABLE READ：保证了一个事务不会修改已经由另一个事务读取但未提交（回滚）的数据。避免了“脏读”和“不可重复读”，但是带来了更多的性能损失。
3. READ COMMITTED：大多数主流数据库的默认事务等级，保证了一个事务不会读到另一个并行事务已修改但仍未提交的数据，避免了“脏读”。该级别适用大多数系统。
4. Read Uncommitted：保证了读取过程中不会读取到非法数据。

关系对照表

![1562836092081](.\assets\1562836092081.png)

|                  | 脏读<br>Dirty reads | 不可重复读<br>no-repeatable reads | 幻读<br>phantom |
| ---------------- | ------------------- | --------------------------------- | --------------- |
| Serializable     | ×                   | ×                                 | ×               |
| REPEATABLE READ  | ×                   | ×                                 | √               |
| READ COMMITTED   | ×                   | √                                 | √               |
| Read Uncommitted | √                   | √                                 | √               |

* 脏读：
  就是读到了别的事务回滚前的脏数据。
  例如事务 B 执行过程中修改了数据 X，在未提交前，事务 A 也读取了 X，但事务 B 却回滚了，这样事务 A 就形成了脏读。
* 不可重复读：
  例如事务 A 先读取了一条数据，然后再执行逻辑的时候，事务 B 将这条数据改变了，然后事务 A 再次读取该数据，发现数据不匹配，这就是所谓的不可重复读。
* 幻读：
  例如事务 A 先根据条件索引得到10条数据，然后事务 B 改变了数据库的一条数据，导致也符合事务 A 当时的搜索条件，这样事务 A 再次搜索发现有11条数据了，就产生了幻读。

#### 2.7 Spring 线程安全问题

由于 Spring 管理的 Bean 对象默认是单例模式，也就是说，面对不同的 request 请求，系统都会用原有的 instance 去处理，当多个线程调用它的时候，它里面的 instance 变量就不是线程安全的了，会发生窜数据的问题。

例如：有一个控制器是使用单例形式，且 Controller 中有一个私有的变量 a，所有请求到同一个 Controller 时，使用的 a 变量是共用的，若是某个请求中修改了这个变量 a，则在别的请求中能够读到这个修改的内容。

解决方法：

1. 在 Controller 中使用 ThreadLocal 变量，将变量 a 存储到一个线程安全的地方
2. 在 Spring 配置文件 Controller 中声明 scope="prototype"，每次都创建新的 controller

*PS：我们在使用 Spring 开发 web 时要注意：默认 Controller、Dao、Service 都是单例的。*

### 三、SpringMVC

#### 3.1 SpringMVC 处理请求流程

① 浏览器发送请求
② DispatcherServlet 接收请求并把访问路径送到`映射处理器`中进行解析
③ 经过解析后，通过处理器适配调用相应 Controller 中的处理方法
④ 视图解析
⑤ 视图渲染
⑥ 返回相应

首先是浏览器发送请求，由 SpringMVC 的 DispatcherServlet 接收请求并把请求路径送到映射处理器中进行解析；
处理器经过解析，调用对应的 Controller 方法进行逻辑处理；
后台程序执行完毕后，视逻辑进行转发或重定向，或者返回 json 格式的字符串给请求端。

#### 3.2 SpringMVC 和 Struts2 区别

##### 3.2.1 机制

**SpringMVC 的入口是 servlet，而 Struts2 的是 filter。**

##### 3.2.2 性能

SpringMVC 的性能会比 Struts2 稍快。

##### 3.2.3 设计

**Struts2 是基于类的设计**，每次发送请求都会实例一个 action，每个 action 都会被注入属性；
**SpringMVC 基于方法设计**，粒度更细。

**SpringMVC 是方法级别的拦截**，拦截到方法后`根据参数上的注释，把 request 数据注入`进去，一个方法对应一个 request 上下文；
**Struts2 是类级别的拦截**，每次来请求就创建一个 action，然后`调用 setter 和 getter 方法把 request 中的数据注入`，即通过 setter 和 getter 方法和 request 打交道，一个 action 对象对应一个 request 上下文。

##### 3.2.4 参数传递

**Struts2** 在接收参数的时候，可以用属性来接收参数，说明**参数是多个方法共享的**；
**SpringMVC 方法之间独立独享** request、response 数据，请求数据通过参数获取。

##### 3.2.5 intercepter 的实现机制

Struts2 有自己的 interceptor 机制；
SpringMVC 用的是独立的 AOP 方式。
因此 Struts2 的配置文件量比 SpringMVC 大，SpringMVC 更加简洁，开发效率也会更高。

##### 3.2.6 SpringMVC 验证

**SpringMVC 在处理 ajax 请求的时候很方便，只需一个注解 @ResponseBody，然后直接返回相应文本即可；**
**而Struts2 则需要导入相关json插件。**

##### 3.2.7 配置

**SpringMVC 和 Spring 是无缝的，因此项目的管理和安全比 Struts2 高；**
**Struts2 的实现需要 xml 配置更多。**

#### 3.3 SpringMVC 和 Struts2 区别口述总结

##### 3.3.1 组件级别方面

Java 三大组件：servlet、filter、listener
SpringMVC 的入口是 servlet，而 Struts2 的是 filter

##### 3.3.2 拦截等级方面

SpringMVC 是方法级别的拦截，请求的 URL 根据映射处理器的解析找到对应的处理方法，一个方法对应一个 request 上下文；
Struts2 是类级别的拦截，每次来请求就创建一个 action，通过 setter 和 getter 方法处理 request 请求，一个 action 对象对应一个 request 上下文

##### 3.3.3 请求参数方面

Struts2 在接收参数的时候，可以用属性来接收参数，因此它的参数是可以多个方法共享的；
SpringMVC 不同的处理方法之间独立独享 request 和 response 数据

##### 3.3.4 对 ajax 请求返回 json 数据方面

SpringMVC 在处理 ajax 请求的时候很方便，只需要一个 @ResponseBody 注释，然后直接返回相应文本即可，而Struts2 则需要相关的 json 插件

##### 3.3.5 和 Spring 框架兼容方面

SpringMVC 和 Spring 是无缝的，所以项目的管理和安全比 Struts2 高

### 四、SpringBoot

#### 4.1 关于 Spring Boot

##### 4.1.1 Spring Boot 简介

Spring Boot 并不是一种新的框架，而是在原来 Spring 框架的基础上进行了配置简化（它默认配置了很多框架的使用方式，就像 maven 整合了几乎所有的 jar 包，Spring Boot 整合了大部分的框架配置）。

多数的 Spring Boot 只需要很少的 Spring 配置，在“约定优于配置”的原则下，Spring Boot 框架使用了特定默认的方式来进行配置，从而使开发人员不再需要定义样板化的配置。所有项目所需要集成的常用框架，都有对应的组件支持。

##### 4.1.2 Spring Boot 特性

* 使用 Spring 项目引导页面可以在几秒构建一个项目
* 方便对外输出各种形式的服务，如 REST API、WebSocket、Web、Streaming、Tasks
* 非常简洁的安全策略集成
* 支持关系数据库和关系数据库
* 支持运行期内嵌容器，如 Tomcat、Jetty
* 强大的开发包，支持热启动
* 自动管理依赖
* 自带应用监控
* 支持各种 IDE，如 IntelliJ IDEA、NetBeans

#### 4.2 Spring Boot 的优势

##### 4.2.1 Spring Boot 让开发变得简单

使用 Spring Boot 可以简化我们开发的流程，提升开发效率：

Before：
	1)	配置 web.xml，加载 Spring 和 SpringMVC
	2)	配置数据库连接、配置 Spring 事务
	3)	配置加载配置文件的读取，开启注解
	4)	配置日志文件
	……
	N)  配置完成后部署到 Tomcat 中执行

同时还需要考虑各个版本的兼容性，jar 包冲突等各种问题。

After：
	1)	登录网址http://start.spring.io/ 选择对应的组件直接下载
	2)	导入项目，进行开发

由这可看出，使用 Spring Boot 大大地简化了开发环境搭建方面的准备工作。

##### 4.2.2 Spring Boot 让测试变得简单

在 Spring Boot 中内置了7种强大的测试框架：

* JUnit：一个 Java 语言的单元测试框架
* Spring Test & Spring Boot Test：为 SpringBoot 应用提供集成测试和工具支持
* AssertJ：支持流式断言的 Java 测试框架
* Hamcrest：一个匹配器库
* Mockito：一个 java mock 框架
* JSONassert：一个针对 JSON 的断言库
* JsonPath：JSON XPath 库

我们只需要在项目中引入 spring-boot-starter-test 依赖包，就可以对数据库、Mock、 Web 等各种情况进行测试

Spring Boot Test 中包含了我们需要使用的各种测试场景，满足我们日常项目的测试需求。

##### 4.2.3 Spring Boot 让配置变得简单

Spring Boot 让配置变简单，说到这里我们就需要了解一下 Spring Boot 的核心思想：约定优于配置。那么什么是约定优于配置呢？

约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

本质是说，开发人员仅需规定应用中不符约定的部分。例如，如果模型中有个名为 User 的类，那么数据库中对应的表就会默认命名为 user。只有在偏离这一约定时，例如将该表命名为“user_info”，才需写有关这个名字的配置。

##### 4.2.4 Spring Boot 让部署变得简单

说起 Spring Boot 让部署变简单，就不得不说 Spring Boot 内嵌容器。内嵌容器不只让部署变得简单，其实在开发调试阶段也会带来非常大的便利性，对比以往开发 Web 项目时配置 Tomcat 的繁琐，会让大家使用 Spring Boot 内嵌容器开发时有更深的感触。使用 Spring Boot 开发 Web 项目，让我们不需要关心容器的环境问题，专心写业务代码即可。

Jenkins 是目前持续构建领域使用最广泛的工具之一，Jenkins 是一个独立的开源自动化服务器，可用于自动化各种任务，如构建，测试和部署软件。Jenkins 可以通过本机系统包 Docker 安装，甚至可以通过安装 Java Runtime Environment 的任何机器独立运行。

说直白一点 Jenkins 就是专门来负责如何将代码变成可执行的程序包，将它部署到目标服务器中，并对其运营状态（日志）进行监控的软件。自动化、性能、打包、部署、发布、发布结果自动化验证、接口测试、单元测试等等关于我们打包测试部署的方方面面 Jenkins 都可以很友好的支持。

使用 Jenkins 部署 Spring Boot 项目非常简单，只需要前期做一些简单的配置，当我们需要发布项目时只需要点击项目对应的发布按钮，就可以将项目从版本库中拉取、打包、发布到目标服务器中，大大简化了运维后期的部署工作。

虚拟化技术的发展给我们带来了更多的可能性，我们可以利用容器化技术，将 Spring Boot 项目做成镜像，根据容器集群的策略来实现弹性扩容、动态部署等。所以 Spring Boot + Docker + Jenkins 会将 Spring Boot 项目的部署做得更简单化、智能化。

##### 4.2.5 Spring Boot 让监控变得简单

可以说 Spring Boot 就是一款自带监控的开源软件，在设计之初就考虑到应用的监控问题，专门提供了一款监控组件来完成这个工作，这个组件就是 Spring Boot Actuator。

Spring Boot Actuator 是 Spring Boot 提供的对应用系统监控的集成功能，可以查看应用配置的详细信息，例如自动化配置信息、创建的 Spring beans 以及一些环境属性等。

当然 Spring Boot Actuator 虽然可以监控一个 Spring Boot 应用的健康情况，实际上现在的系统都是需要很多的服务相互配合来完成工作，如何通过一个监控软件来监控所以的 Spring Boot 项目将变得比较紧迫。

在开源界也有人意识到了这个问题，并且基于 Spring boot actuator 做出了一款强大的监控软件，这个软件就是 Spring Boot admin。

Spring Boot Admin 是一个管理和监控 Spring Boot 应用程序的开源软件。每个应用都认为是一个客户端，通过 HTTP 或者使用 Eureka 注册到 admin server 中进行展示，Spring Boot Admin UI 部分使用 AngularJs 将数据展示在前端。

Spring Boot Admin 是一个针对 spring-boot 的 actuator 接口进行 UI 美化封装的监控工具。他可以：在列表中浏览所有被监控 spring-boot 项目的基本信息，详细的 Health 信息、内存信息、JVM 信息、垃圾回收信息、各种配置信息（比如数据源、缓存列表和命中率）等，还可以直接修改 logger 的 level。

使用 Spring Boot Admin 不仅可以监控 Spring Boot 项目，还可以监控 Spring Cloud 项目，因此使用了 Spring Boot 项目之后我们监控 Spring Boot 集群效果如下：

![1562837215715](.\assets\1562837215715.png)

简单、直观、易用是它的特点，针对一些特殊情况还可以提供报警服务。所以说使用 Spring Boot Actuator 解决了单个 Spring Boot 的监控问题，使用 Spring Boot Admin 就是解决了整个集群监控的问题。

#### 4.3 Spring，Spring Boot，Spring Cloud

Spring 最初最核心的两大核心功能 Spring IOC 和 Spring AOP 成就了 Spring，Spring 在这两大核心的功能上不断的发展，才有了 Spring 事务、Spring MVC 等一系列伟大的产品，最终成就了 Spring 帝国，到了后期 Spring 几乎可以解决企业开发中的所有问题。

Spring Boot 是在强大的 Spring 帝国生态基础上面发展而来，发明 Spring Boot 不是为了取代 Spring，是为了让人们更容易的使用 Spring。所以说没有 Spring 强大的功能和生态，就不会有后期的 Spring Boot 火热，Spring Boot 使用约定优于配置的理念，重新重构了 Spring 的使用，让 Spring 后续的发展更有生命力。

Spring Cloud 是一系列框架的有序集合。它利用 Spring Boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot 的开发风格做到一键启动和部署。

Spring 并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过 Spring Boot 风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。

根据上面的说明我们可以看出来，Spring Cloud 是为了解决微服务架构中服务治理而提供的一系列功能的开发框架，并且 Spring Cloud 是完全基于 Spring Boot 而开发，Spring Cloud 利用 Spring Boot 特性整合了开源行业中优秀的组件，整体对外提供了一套在微服务架构中服务治理的解决方案。

综上我们可以这样来理解，正是由于 Spring IOC 和Spring AOP 两个强大的功能才有了 Spring，Spring 生态不断的发展才有了 Spring Boot，使用 Spring Boot 让 Spring 更易用更有生命力，Spring Cloud 是基于 Spring Boot 开发的一套微服务架构下的服务治理方案。

### 五、Hibernate

#### 5.1 关于 Hibernate 框架

Hibernate 是一个 ORM 框架（对象关系映射）

#### 5.2 Hibernate 的一二级缓存

##### 5.2.1 一级缓存（内部缓存）：

一级缓存属于应用事务级缓存，也即“Session 的缓存”。
*之所以说是应用事务级缓存，因为 Session 对象的生命周期通常对应一个数据库事务或一个应用事务。*

##### 5.2.2 二级缓存（“SessionFactory 缓存”）：

SessionFactory 对象的生命周期与应用程序的整个过程对应，因此 Hibernate 二级缓存是进程范围或者集群范围的缓存有可能出现并发问题，需采用适当的并发访问策略，为被缓存的数据提供事务隔离级别。
二级缓存是可选的，是一个可配置的插件，默认情况下 SessionFactory 不会开启。

不适合存放二级缓存的数据：
① 经常被修改的数据
② 绝对不允许出现并发的数据（如财务数据）
③ 与其他应用共享的数据

一级缓存，也叫做“Session 缓存”，其生命周期通常对应一个数据库事务或一个应用事务。
二级缓存，也叫做“SessionFactory 缓存”，其生命周期与应用程序的整个过程对应。

#### 5.3 Hibernate 缓存机制的作用流程

当 Hibernate 根据 ID 访问数据对象的时候，会先从 Session 一级缓存中查；
查不到，如果配置了二级缓存的话，则会到二级缓存中查；
如果都查不到，再去查数据库，把结果按照 ID 放到缓存中。当有增删改操作的时候，更新缓存。

#### 5.4 Hibernate 的三种状态

|                       | 临时态 | 持久态 | 游离态 |
| --------------------- | :----: | :----: | :----: |
| 在数据表中有对应记录  |   ×    |   √    |   √    |
| 拥有一个持久化标识    |   ×    |   √    |   √    |
| 存在 session 的缓存中 |   ×    |   √    |   ×    |

##### 5.4.1 临时态（瞬时态）

**通过new关键字生产一个实体对象，即是临时态。**此时对象还未纳入Hibernate的缓存管理中（没有 session 关联），该状态的特点是——**在数据库中不存在一条与之对应的记录**。如果没有变量引入这个对象，则会被 JVM 的垃圾回收机制回收。

##### 5.4.2 持久态

通过 Session 的 save()、saveOrUpdate()、persist()、merge() 方法，进行对象的持久化。**持久化对象是已经被保存进数据库的实体对象，在数据库中有对应的记录，拥有一个持久化标识（ID），并且这个实体对象现在还处在 Hibernate 的缓存管理之中（session 还未关闭）。**对该实体对象的任何修改，并不会马上同步到数据库，直到数据库事务提交。

##### 5.4.3 游离态

当 Session 进行了 close()、clear()、evict() 或 flush() 后（session 关闭），实体对象从持久态变成游离态，对象虽然拥有持久和与数据库对应记录一致的标识值，但对象已从会话中清除，不在持久化管理之内。**此状态与临时态十分相似，但多了一个持久化的标识。**

![1562921301949](.\assets\1562921301949.png)

#### 5.5 Hibernate 和 MyBatis 的区别

##### 5.5.1 对数据库操作方面

Hibernate 入门门槛高，是一个标准的 ORM 框架（对象关系映射），不需要程序写 SQL，SQL 语句自动生成，但是对 **SQL 语句进行优化、修改比较困难**；
MyBatis 专注于 SQL 本身，需要程序员自己编写 SQL，**SQL 修改、优化比较方便**。是一个不完全的 ORM 框架，虽然程序员自己写 SQL，MyBatis 也可以实现映射（输入映射，输出映射）。

##### 5.5.2 适用场景方面

**Hibernate 适用于需求变化不多的中小型项目**，比如：后台管理系统，ORM、OA 等；
**MyBatis 适用于需求变化较多的项目**，比如：互联网项目。

### 六、Shiro

## 多线程

### 一、Java 线程

#### 1.1 线程的两种声明方式

继承类：

````java
public class Test02 extends Thread {
    @Override
    public void run() {
        // TODO Auto-generated method stub
        super.run();
    }
}
````

实现接口（实现接口的方式来创建线程会加灵活）：

````java
public class Test02 implements Runnable {
    @Override
    public void run() {
        // TODO Auto-generated method stub		
    }
}
````

#### 1.2 线程的五种状态/生命周期

新建 → 就绪 → 运行 → 阻塞 → 死亡

![1562916177659](.\assets\1562916177659.png)

##### 1.2.1 synchronized vs Lock

1. synchronized 是 Java 内置关键字；在 JVM 层面，Lock 是个 Java 类
2. synchronized 无法判断是否获取锁的状态，Lock 可以判断是否获取到锁
3. synchronized 会自动释放锁（线程执行完同步代码会释放锁，线程执行过程中发生了异常也会释放锁），Lock 需在 finally 中使用 unlock() 方法手工释放锁，否则容易造成线程死锁
4. 线程 A 获得锁，线程 B 会等待；若线程 A 阻塞，且线程 A 和线程 B 使用 synchronized 关键字，则线程 B 会一直等待；而 Lock 锁就不一定会等待下去，如果线程 B 尝试获取不到锁，线程可以不用一直等待就结束了
5. synchronized 锁适合代码少量的同步问题，Lock 锁适合大量同步的代码的同步问题

#### 1.3 线程与进程

简而言之，进程是程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至少有一个线程。进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切换次数，从而效率更高。线程是进程的一个实体，是 CPU 调度和分派的基本单位，是比程序更小的能独立运行的基本单位。同一进程中的多个线程之间可以并发执行。

### 二、线程死锁

有 AB 两个线程进行，线程 A 锁定了资源，同时执行操作的过程中需要请求线程 B 的资源，但是资源又被线程 A 占用，两个线程相互锁死

![1562916221444](.\assets\1562916221444.png)

### 三、乐观锁和悲观锁

#### 3.1 乐观锁

乐观锁即是总认为不会发生并发问题，每次取数据的时候总会认为不会有其他线程对数据进行修改，因此不会上锁。只有在更新数据的时候会判断数据有没有被其他线程修改过。（数据库修改数据的操作一般是①取出数据；②修改数据；③更新数据）
这种一般会通过版本号的机制或 CAS 去实现。
版本号机制即在数据库中有一个 version 的字段记录数据被修改的次数，每次更新数据的时候需要对比一下 version 的值，如果不相同，则重新进行取数据操作。
CAS 即对修改前的数据进行记录，在更新数据的时候如果发现跟修改前的数据不一致，则重新进行取数据操作。

#### 3.2 悲观锁

则是总是假设最坏的情况，认为每次取数据的时候总会有其他线程对数据进行修改，所以都会进行加锁，当其他线程想要访问数据的时候，都会阻塞挂起。在 Java 中，synchronized 的思想也是一个悲观锁。

### 四、Java 自带检测死锁工具（Jconsole）

1. 通过 cmd 窗口 -> 输入命令 jconsole
2. 选择需要检测的进程

### 五、volatile 关键字

> volatile 关键字经常在并发编程中使用，其特性是保证可见性以及有序性，但是关于 volatile 的使用仍然要小心，这需要明白 volatile 关键字的特性及实现的原理，这也是本篇文章的主要内容。

#### 5.1 Java 内存模型

先形成基础认识，了解下 Java 内存模型。

Java内存模型规定了：

* **所有的变量都存储在主内存中**
* **每条线程中还有自己的工作内存，线程的工作内存中保存了被该线程所使用到的变量**
  （这些变量是从主内存中拷贝而来）
* **线程对变量的所有操作（读取，赋值）都必须在工作内存中进行**
* **不同线程之间也无法直接访问对方工作内存中的变量，线程间变量值的传递均需要通过主内存来完成**

![8a3d9a1b94b97e83](.\assets\8a3d9a1b94b97e83.png)

由此可预知，当我们进行多线程编程时，变会产生数据“脏读”等问题。

举个例子：

````java
int i = 10;
````

**执行线程需要先在自己的工作线程中对变量 i 所在的缓存行进行赋值操作，然后再写入主存当中。**

而此时同时有两个线程执行这段代码，i 的初始值为 10，但是我们希望这两个线程执行完后，i 的值可以变为 12。

这时可能存在下面一种情况：

**初始时，两个线程分别读取 i 的值存入各自所在的工作内存中，接着线程1 进行 +1 操作，然后把 i 的最新值写入到内存。此时线程2 的工作内存当中 i 的值还是 10，也进行 +1 操作后，线程2 把 i 的值也写入到内存。**

这时最终的结果是 i = 11，而不是 12。这就是著名的缓存一致性问题。通常称这种被多个线程访问的变量为：**共享变量**。

我们要让 i = 12，就要解决共享变量的正确性问题，需要先了解并发编程的三大概念：**原子性，有序性，可见性**。

#### 5.2 原子性

##### 5.2.1 定义

原子性：即一个操作或者多个操作，要么全部执行，并且执行的过程不会被任何因素打断，要么就都不执行。

##### 5.2.2 实例

银行转账中途停电问题

##### 5.2.3 Java 中的原子性

在 Java 中，**对基本数据类型的变量的读取和赋值操作是原子性操作**，即这些操作是不可被中断的，要么执行，要么不执行。

案例理解：

````java
x = 10;    //语句1
y = x;     //语句2
x++;       //语句3
x = x + 1; //语句4
````

在上面的四个语句中，只有语句1 是原子性操作，其他三个都不是。

语句1 是直接将数值 10 赋值给 x，也就是说线程执行这个语句的会直接将数值 10 写入到工作内存中。

语句2 实际上包含两个操作，它先要去读取 x 的值，再将 x 的值写入工作内存。虽然``读取 x 的值``，以及``将 x 的值写入工作内存``，这2个操作都是原子性操作，但是合起来就不是原子性操作了。

同样的，**语句3（x++）和语句4（x = x+1）包括三个操作：读取 x 的值，进行 +1 操作，写入新的值**。

因此，**只有简单的读取、赋值（而且必须是将数字赋值给某个变量，变量之间的相互赋值不是原子操作）才是原子操作。**

Java 内存模型只保证了基本读取和赋值是原子性操作，**如果要实现更大范围操作的原子性，可以通过 synchronized 和 Lock 来实现。由于 synchronized 和 Lock 能够保证任一时刻只有一个线程执行该代码块，那么自然就不存在原子性问题了，从而保证了原子性。**

#### 5.3 可见性

##### 5.3.1 定义

可见性：即当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

##### 5.3.2 实例

````java
//线程1执行的代码
int i = 0;
i = 10;
 
//线程2执行的代码
j = i;
````

由上面的分析可知，当线程1 执行 i =10 时，会先把 i 的初始值加载到工作内存中，然后赋值为 10，那么在线程1 的工作内存当中 i 的值变为 10 了，却没有立即写入到主存当中。

此时线程2 执行 j = i，它会先去主存读取 i 的值并加载到线程2 的工作内存当中，注意此时内存当中i的值还是 0，那么就会使得 j = 0，而不是 10.

这就是可见性问题，线程1 对变量 i 修改了之后，线程2 没有立即看到线程1 修改的值。

##### 5.3.3 Java 中的可见性

对于可见性，Java 提供了 volatile 关键字。

**当一个共享变量被 volatile 修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。**

而普通的共享变量不能保证可见性，**是因为普通共享变量被修改之后，什么时候被写入主存是不确定的，当其他线程去读取时，此时内存中可能还是原来的旧值，因此无法保证可见性。**

另外，通过 synchronized 和 Lock 也能够保证可见性，synchronized 和 Lock 能保证同一时刻只有一个线程获取锁然后执行同步代码，并且**在释放锁之前会将对变量的修改刷新到主存当中**。因此可以保证可见性。

#### 5.4 有序性

##### 5.4.1 定义

有序性：即程序执行的顺序按照代码的先后顺序执行。

##### 5.4.2 实例

````java
int i = 0;              

boolean flag = false;

i = 1;                //语句1  
flag = true;          //语句2
````

例子中的语句1 并不一定会在语句2 之前执行（虽然看起来是这样），因为这里可能会有指令重排（Instruction Reorder）的现象。

指令重排：**一般来说，处理器为了提高程序运行效率，可能会对输入代码进行优化，它不保证程序中各个语句的执行先后顺序同代码中的顺序一致，但是它会保证程序最终执行结果和代码顺序执行的结果是一致的。**
（简单来说就是：①处理器进行代码优化；②优化处理会考虑指令之间的数据依赖性，不会影响到程序的最终逻辑）

**指令重排在单个线程内不会影响到程序执行的结果，但是会影响到线程并发执行的正确性**：

````java
//线程1:
context = loadContext();   //语句1
inited = true;             //语句2

//线程2:
while(!inited ){
   sleep()
}
doSomethingwithconfig(context);
````

在上面代码中，由于语句1 和语句2 没有数据依赖性，因此可能会被重排序。假如发生了重排序，在线程1 执行过程中先执行语句2，而此是线程2 会以为初始化工作已经完成，那么就会跳出 while 循环，去执行 doSomethingwithconfig(context) 方法，但此时 context 并没有被初始化，就会导致程序出错。

由此得出一个结论：**要想并发程序正确地执行，就必须要保证原子性、可见性以及有序性。只要有一个没有被保证，就有可能会导致程序运行不正确。**

##### 5.4.3 Java 中的有序性

由于在 Java 内存模型中，允许编译器和处理器对指令进行重排序，会影响到多线程并发执行的正确性。

因此我们可以使用 volatile 关键字来保证一定的“有序性”。另外，也可使用 synchronized 和 Lock。因为 synchronized 和 Lock 能保证每个时刻是只有一个线程在执行同步代码，相当于是让线程顺序执行同步代码，自然就保证了有序性。

Java 内存模型具备一些先天的“有序性”，即 Java 处理器能够维护单个线程的执行结果，这种处理称为：先行发生（happens-before）原则。**如果两个操作的执行次序无法从 happens-before 原则推导出来，那么它们就不能保证它们的有序性，虚拟机可以随意地对它们进行重排序。**

先行发生（happens-before）原则：

1. **程序次序规则**：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
   （该规则无法保证程序在多线程中执行的正确性）
2. **锁定规则**：一个 unLock 操作先行发生于后面对同一个锁的 lock 操作
   （先解锁，必须要先上锁；或者是，要上锁，必须要等程序先解锁）
3. **volatile 变量规则**：对一个 volatile 关键字修饰的变量的写操作先行发生于后面对这个变量的读操作
4. **传递规则**：如果操作 A 先行发生于操作 B，而操作 B 又先行发生于操作 C，则可以得出操作 A 先行发生于操作 C
   （该规则体现了 happens-before 原则具有传递性）
5. 线程启动规则：Thread 对象的 start() 方法，先行发生于此线程的每一个动作
6. 线程中断规则：对线程 interrupt() 中断方法的调用，先行发生于被中断线程的代码检测到中断事件的发生
7. 线程终结规则：线程中所有的操作，都先行发生于线程的终止检测，我们可以通过 Thread.join() 方法结束、Thread.isAlive() 方法的返回值，来检测线程是否已经终止执行
8. 对象终结规则：一个对象的初始化完成，先行发生于他的 finalize() 释放内存方法的开始

以上 8 条规则中，前 4 条规则比较重要，后 4 条规则都是显而易见的。

#### 5.5 深入理解 volatile 关键字

##### 5.5.1 volatile 保证可见性

一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：

1. 保证了**不同线程对这个变量进行操作时的可见性**
   （即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的）
2. **禁止进行指令重排序**

````java
//线程1
boolean stop = false;
while(!stop){
    doSomething();
}
 
//线程2
stop = true;
````

以上这段代码中，我们先执行线程1，然后执行线程2。

这时虽然理解上是，一旦执行线程2，变量 stop 的值就会发生改变，循环就会被中断。但是也会出现线程无法中断的情况（虽然是小概率，但是一旦发生，程序即陷入死循环）。

因为就算线程2 更改了 stop 变量的值，但是还没来得及写入主存当中，线程2 就转去做其他事情了，那么线程1 由于不知道线程2 对 stop 变量的更改，因此还会一直循环下去。

但是用 volatile 关键字进行修饰的话就不一样了：

1. volatile 关键字会**强制将线程2 修改的值立即写入主内存**
2. volatile 关键字会**在线程2 进行修改时，使得线程1 的工作内存中缓存变量 stop 的缓存行无效**
   （反映到硬件层的话，就是 CPU 的 L1 或者 L2 缓存中对应的缓存行无效）
3. 由于线程1 的工作内存中缓存变量 stop 的缓存行无效了，所以**线程1 再次读取变量 stop 的值时只能主内存找**

##### 5.5.2 volatile 不保证原子性

````java
public class Test {
    public volatile int inc = 0;
     
    public void increase() {
        inc++;
    }
     
    public static void main(String[] args) {
        final Test test = new Test();
        for(int i=0;i<10;i++){
            new Thread(){
                public void run() {
                    for(int j=0;j<1000;j++)
                        test.increase();
                };
            }.start();
        }
         
        while(Thread.activeCount()>1)  //保证前面的线程都执行完
            Thread.yield();
        System.out.println(test.inc);
    }
}
````

以上程序在理解上执行的结果为：test.inc = 10 * 1000 = 10000

但是在实际的运行中，会发现每次运行结果都不一致，都是一个小于 10000 的数字。

这种情况跟 volatile 关键字不保证原子性有关：

假如某个时刻中，inc 的值为 10。**线程1 要对变量进行自增操作，线程1 先读取了主内存的值，得到 inc = 10，然后立马转去做其他事了（这里要注意的是：线程1 并没有对inc 进行修改，因此不会导致线程2 的工作内存中缓存变量 inc 的缓存行无效，也不会导致主内存中的值刷新）。**这时，线程2 也要对变量进行自增操作，直接去主内存拿到 inc = 10，然后进行 +1 操作得到 11，并写到了主内存中。然后线程1 有空闲回来继续操作的时候，也是对原来的 10 进行 +1 操作，写到主内存中。

那么两个线程分别都进行了一次自增操作，但是 inc 只增加了 1。其根源就在于：**volatile 关键字不保证原子性**。这里可通过：**synchronized 或 lock，进行加锁处理，来保证操作的原子性。也可以通过 AtomicInteger。**

##### 5.5.3 volatile 保证有序性

由于 volatile 关键字能禁止指令重排序，所以 volatile 能在一定程度上保证有序性。（回看：volatile 变量规则）

这里继续使用有序性的例子：

````java
//线程1:
context = loadContext();   //语句1
inited = true;             //语句2
 
//线程2:
while(!inited){
  sleep()
}
doSomethingwithconfig(context);
````

这里可知语句2可能会在语句1 之前执行，导致线程1在初始化语句2后离开做其他事，没有初始化 context；而线程2中使用未初始化的context去进行操作，程序出错。

如果使用 volatile 关键字对 inited 变量进行修饰，就不会出现这种问题。因为语句2 的初始化必定在语句1后执行

#### 5.6 volatile 的实现原理

##### 5.6.1 可见性

处理器为了提高处理速度，不直接和内存进行通讯，而是将系统内存的数据独到内部缓存后再进行操作，但操作完后不知什么时候会写到内存。

但是如果是**对声明了 volatile 变量进行写操作的时候，JVM 会向处理器发送一条 Lock 前缀的指令，将这个变量所在缓存行的数据写会到系统内存。** 这一步确保了如果有其他线程对声明了 volatile 变量进行修改，会立即更新主内存中的数据。

而这时候其他处理器的缓存还是旧的，所以在多处理器环境下，为了保证各个处理器缓存一致，**每个处理会通过嗅探在总线上传播的数据来检查自己的缓存是否过期，当处理器发现自己缓存行对应的内存地址被修改了，就会将当前处理器的缓存行设置成无效状态，当处理器要对这个数据进行修改操作时，会强制重新从系统内存把数据读到处理器缓存里。**这一步确保了其他线程获得的声明了 volatile 变量都是从主内存中获取最新的。

##### 5.6.2 有序性

Lock 前缀指令实际上相当于一个内存屏障（也成内存栅栏），它确保**指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；**即在执行到内存屏障这句指令时，在它前面的操作已经全部完成。

#### 5.7 volatile vs synchronized

* volatile 本质是在告诉 JVM 当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取；synchronized 则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
* volatile 仅能使用在变量级别；synchronized 则可以使用在变量、方法、和类级别的。
* volatile 仅能实现变量的修改可见性，不能保证原子性；而 synchronized 则可以保证变量的修改可见性和原子性。
* volatile 不会造成线程的阻塞；synchronized 可能会造成线程的阻塞。
* volatile 标记的变量不会被编译器优化；synchronized 标记的变量可以被编译器优化。

### 六、ThreadLocal（线程局部变量）

#### 6.1 ThreadLocal 是什么

Java 提供 ThreadLocal 类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

ThreadLocal 是一个有点像 HashMap 的数据结构，可以保存"key : value"键值对，但是一个 ThreadLocal 只能保存一个，并且各个线程的数据互不干扰。

需要注意的是，ThreadLoalMap 的 Entry 是继承 WeakReference，和 HashMap 很大的区别是，Entry 中没有 next 字段，所以就不存在链表的情况了。

````java
ThreadLocal<String> objectThreadLocal = new ThreadLocal();
try {
    objectThreadLocal.set("占小狼");
    String name = objectThreadLocal.get();
    // 其它业务逻辑
} finally {
    objectThreadLocal.remove();
}
````

#### 6.2 ThreadLocal 方法摘要

| 作用域    | 类型 | 方法           | 说明                                             |
| --------- | ---- | -------------- | ------------------------------------------------ |
| public    | T    | get()          | 返回此线程局部变量的当前线程副本中的值           |
| protected | T    | initialValue() | 返回此线程局部变量的当前线程的“初始值”           |
| public    | void | remove()       | 移除此线程局部变量当前线程的值                   |
| public    | void | set(T value)   | 将此线程局部变量的当前线程副本中的值设置为指定值 |

#### 6.3 ThreadLocal 作用及示例

由于在并发编程的时候，一个类的成员变量不做处理是线程不安全的，各个线程操作的都是同一个对象。

##### 6.3.1 数据库连接类管理

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

#### 6.4 作用原理（摘自网上）

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

#### 6.5 ThreadLocal 的内存泄露问题

由 Entry 的源码可知，**ThreadLocalMap 是使用 ThreadLocal 的弱引用作为 Key 的**。

![1563442325615](.\assets\1563442325615.png)
（实线表示强引用，虚线表示弱引用）

如图所示，ThreadLocalMap 使用 ThreadLocal 的弱引用作为 key。

当把 ThreadLocal 实例置为 null 以后，没有任何强引用指向 ThreadLocal 实例，所以 ThreadLocal 将会被 gc 回收。这样一来，ThreadLocalMap 中就会出现 key 为 null 的 Entry，且这 Entry 的 value 永远不会被访问到了，而如果当前线程再迟迟不结束的话，这些 key 为 null 的 Entry 的 value 就会一直存在一条强引用链：

Thread Ref → Thread → ThreaLocalMap → Entry → value

永远无法回收，造成内存泄露。

#### 6.6 内存泄漏问题解决

其实，ThreadLocalMap 在设计中已经考虑到这种情况，也加上了一些防护措施，即：

**在 ThreadLocal 进行`get(),set(),remove()` 操作的时候都会清除线程 ThreadLocalMap 里所有 key 为 null 的 value**。

但是这些被动的预防措施还是不能保证不会发生内存泄漏：

1. 使用 static 的 ThreadLocal，延长了 ThreadLocal 的生命周期，可能导致内存泄漏。
2. 分配使用了 ThreadLocal ，但是又不再调用`get(),set(),remove()`方法进行清除，那么这块内存还会一直存在，依然可能导致内存泄漏。

所以，在很多的情况下，都需要使用者手动去调用 ThreadLocal 的 remove 函数，手动删除不再需要的 ThreadLocal，防止内存泄露。

而且， JDK 也建议将 ThreadLocal 变量定义成 private static 的，这样的话 ThreadLocal 的生命周期就更长，由于一直存在ThreadLocal 的强引用，所以 ThreadLocal 也就不会被回收，也就能保证任何时候都能根据 ThreadLocal 的弱引用访问到 Entry 的 value 值，然后在 Thread 中使用完 ThreadLocal 对象后调用 remove 方法，进行手动清除，防止内存泄露。

#### 6.7 ThreadLocalMap 初始容量（略）

初始容量16，负载因子2/3，解决冲突的方法是再 hash 法，也就是：在当前 hash 的基础上再自增一个常量。

### 七、缓存雪崩

缓存雪崩可能是因为数据未加载到缓存中，或者缓存同一时间大面积的失效，从而导致所有请求都去查数据库，导致数据库 CPU 和内存负载过高，甚至宕机。

解决思路：

1. 采用加锁计数，或者使用合理的队列数量来避免缓存失效时对数据库造成太大的压力。这种办法虽然能缓解数据库的压力，但是同时又降低了系统的吞吐量。
2. 分析用户行为，尽量让失效时间点均匀分布。避免缓存雪崩的出现。
3. 如果是因为某台缓存服务器宕机，可以考虑做主备，比如：redis 主备，但是双缓存涉及到更新事务的问题，update 可能读到脏数据，需要好好解决。

## Java Web 网络编程知识

### 一、TCP/IP 的三次握手和四次挥手

#### 1.1 TCP/IP 协议三次握手建立连接过程

第一次握手：客户端发出一个 SYN=1 的连接请求信号：“服务端你好，我想跟你说说话”
第二次握手：服务端接收到请求，回复一个确认信息（SYN + ACK）：“请求收到，我现在已经做好准备了”
第三次握手：客户端收到服务端的确认信息后，礼貌性地回复一下（ACK）：“哦哦，那我们开始吧”

#### 1.2 TCP/IP 协议四次挥手断开连接过程

第一次挥手：双方交互得差不多了，客户端想要断开连接：“我话说完了（FIN）”
第二次挥手：服务端知道客户端无话可说了，但还有几句心里话想说：“我知道你话完了（ACK），但我还想要补充几句，吧啦吧啦……”
第三次挥手：此时客户端洗耳恭听继续处于等待结束的状态，服务端也说完了，自身此时处于等待关闭连接的状态，所以告诉客户端：“我话也说完了，咱们断开吧（FIN）”
第四次挥手：客户端收到服务端也无话可说的信息，礼貌性地回复一下（ACK）：“哦哦，那我们断开吧”（这里客户端会为自己定义一个定时器，超过时间则默认服务端已经断开了）

* PS：客户端和服务端没有固定的一方，谁先发起建立/断开连接的请求，谁就为客户端

![1557477025948](.\assets\1557477025948.png)

### 二、TCP vs UDP

#### 2.1 TCP

##### 2.1.1 TCP 优点

可靠，稳定。

TCP 的可靠性体现在 TCP 在传递数据之前，会有三次握手来建立连接，而且在数据传递时，有确认、窗口、重传、拥塞控制机制，在数据传完后，还会断开连接用来节约系统资源。

##### 2.1.2 TCP 缺点

慢，效率低，占用系统资源高，易被攻击。

TCP 在传递数据之前，要先建连接，这会消耗时间，而且在数据传递时，确认机制、重传机制、拥塞控制机制等都会消耗大量的时间，而且要在每台设备上维护所有的传输连接，事实上，每个连接都会占用系统的 CPU、内存等硬件资源。 而且，因为 TCP 有确认机制、三次握手机制，这些也导致 TCP 容易被人利用，实现 DOS、DDOS、CC 等攻击。

#### 2.2 UDP

##### 2.2.1 UDP 优点

快，比 TCP 稍安全。

UDP 没有 TCP 的握手、确认、窗口、重传、拥塞控制等机制，UDP 是一个无状态的传输协议，所以它在传递数据时非常快。没有 TCP 的这些机制，UDP 较 TCP 被攻击者利用的漏洞就要少一些。但 UDP 也是无法避免攻击的，比如：UDP Flood 攻击……

##### 2.2.2 UDP 缺点

不可靠，不稳定。

因为 UDP 没有 TCP 那些可靠的机制，在数据传递时，如果网络质量不好，就会很容易丢包。

#### 2.3 TCP vs UDP

1. TCP 面向连接（如打电话要先拨号建立连接）；UDP 面向非连接，即发送数据之前不需要建立连接
2. TCP 要求系统资源较多；UCP 较少
3. TCP 提供可靠的服务。也就是说，通过 TCP 连接传送的数据，`无差错`，`不丢失`，`不重复`，且`按序到达`；UDP 只能尽最大努力交付，即`不保证可靠交付`
4. TCP 的可靠连接主要用于传输少量验证性数据；UDP 则主要应用在对网络通讯质量要求不高，并且要求网络通讯速度能尽量快的时候（每一条 TCP 连接只能是`点到点`的；UDP 支持`n对n`的交互通信）
5. TCP 首部开销20字节；UDP 的首部开销小，只有8个字节
6. TCP 的逻辑通信信道是全双工的可靠信道；UDP 则是不可靠信道

#### 2.4 UDP 内容拓展

主机可采用的通信方式有如下三种：

1. 单播：单台主机与单台主机之间的数据通信

2. 广播：单台主机向网络中所有主机发送数据报的过程

3. 多播：单台主机向选定的一组主机发送数据报的过程

   *（与广播和单播不同的是，要实现组播需要在接受组播的客户机上安装相应的客户端程序）*

   *（广播的缺点是网络中的所有主机都必须处理数据报，而多播不需要，减少了网络流量）*

### 三、HTTP 协议

HTTP（Hypertext transfer protocol）文本传输协议，是一种通过浏览器和服务器进行数据交互，进行超文本（文本、图片、视频等）传输的规定。

![1565658535676](.\assets\1565658535676.png)

#### 3.1 HTTP 响应码

````json
1xx：1开头表示消息，请求收到，继续处理
2xx：2开头表示成功，行为被成功地接受、理解和采纳
3xx：3开头表示重定向，为了完成请求，必须进一步执行的动作
4xx：4开头表示失败，请求包含语法错误或者请求无法实现
5xx：5开头表示服务器异常，服务器不能实现一种明显无效的请求

200：客户端请求成功
302：临时跳转，跳转的地址通过Location指定
400：客户端请求有语法错误，不能被服务器识别
403：服务器受到请求，但是拒绝提供服务
404：请求的资源不存在
500：服务器发生不可预期的错误
````

### 四、Cookie 和 Session

> 什么是 Cookie？什么是 Session？这两者的区别是什么？

#### 4.1 什么是 Cookie

HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）**是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。**通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）

#### 4.2 什么是 Session

Session 代表着服务器和客户端一次会话的过程。Session 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。当客户端关闭会话，或者 Session 超时失效时会话结束。

#### 4.3 Cookie  vs  Session

- 作用范围不同，Cookie 保存在客户端（浏览器），Session 保存在服务器端。
- 存取方式的不同，Cookie 只能保存 ASCII，Session 可以存任意数据类型，一般情况下我们可以在 Session 中保持一些常用变量信息，比如说 UserId 等。
- 有效期不同，Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭或者 Session 超时都会失效。
- 隐私策略不同，Cookie 存储在客户端，比较容易遭到不法获取，早期有人将用户的登录名和密码存储在 Cookie 中导致信息被窃取；Session 存储在服务端，安全性相对 Cookie 要好一些。
- 存储大小不同， 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie。

#### 4.4 Cookie 和 Session 在使用时的关联

![1563762638165](.\assets\1563762638165.png)

用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建创建对应的 Session ，请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器，浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名。

当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

根据以上流程可知，SessionID 是连接 Cookie 和 Session 的一道桥梁，大部分系统也是根据此原理来验证用户登录状态。

#### 4.5 用户浏览器禁止 Cookie 后的处理方案

##### 4.5.1 方案一

每次请求中都携带一个 SessionID 的参数，也可以 Post 的方式提交，也可以在请求的地址后面拼接 `xxx?SessionID=123456...`。

##### 4.5.2 方案二

Token 机制。

Token 的意思是“令牌”，是服务端生成的一串字符串，作为客户端进行请求的一个标识。Token 机制和 Cookie 和 Session 的使用机制比较类似。

当用户第一次登录后，服务器根据提交的用户信息生成一个 Token，响应时将 Token 返回给客户端，以后客户端每次请求只需带上这个 Token 前来请求数据即可，无需再次登录验证。

#### 4.6 分布式 Session 问题

有些 JavaWeb 项目为了可以支撑更大的流量，在后端往往配置了多台服务器共同来支撑前端用户请求，这时需处理解决：用户在 A 服务器登录，但是第二次请求跑到 B 服务器，导致出现登录失效问题。

分布式 Session 一般会有以下几种解决方案：

##### 4.6.1 方案一：Nginx ip_hash 策略

服务端使用 Nginx 代理，每个请求按访问 IP 的 hash 分配，这样来自同一 IP 固定访问一个后台服务器，避免了在服务器 A 创建 Session，第二次分发到服务器 B 的现象。

##### 4.6.2 方案二：Session 复制

在任何一个服务器上的 Session 信息发生改变（增删改），该节点会把这个 Session 的所有内容序列化，然后广播给所有其它节点。

##### 4.6.3 方案三：共享 Session

将用户的 Session 等信息使用缓存中间件（Redis）来统一管理，保障分发到每一个服务器的响应结果都一致。

Redis 是基于内存存储数据的，性能很高，尤其是高并发的情况下尤为合适。主要是因为从内存中读取数据要比从磁盘读取数据快很多。

而且 Redis 内存数据库还支持数据过期失效的机制，正好与 Session 的过期机制对应。且支持内存数据备份到磁盘。

由于内存存储数据的高性能和高适用性，因此推荐使用方案三。

# 数据库复习

## 一、数据库三大范式

### 1.1 第一范式（1NF）

任何一个关系型数据库中，第一范式（1NF）是对关系模式的基本要求，不满足第一范式（1NF）的数据库就不是关系型数据库。
第一范式（1NF）是指**数据库表的每一列都是不可分割的基本数据项**，同一列中不能有多个值，即实体中某个属性不能有多个值或者不能有重复的属性。表的每一行只包含一个实例的信息。

### 1.2 第二范式（2NF）

第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，满足第二范式（2NF）必须先满足第一范式（1NF）。
第二范式（2NF）要求数据库表中的每个实例或行必须可以被唯一地区分。通常需要为表加上一列用于存储，该唯一属性列被称为主关键字、主码（userId）。（**非主属性非部分依赖于主关键字**）

### 1.3 第三范式（3NF）

满足第三范式（3NF）必须先满足第二范式（2NF）。
第三范式（3NF）要求数据库表中不包含已在其他表中已包含的非主关键字信息，否则会有大量的数据冗余。简而言之，第三范式（3NF）就是**属性不依赖于其他非主属性**，清除表中的传递依赖关系。

## 二、传统数据库优化

面对**海量数据（1000万）**和**高并发**，处理瓶颈一般是在数据库上面，可合理使用缓存技术和队列技术进行处理，静态资源可以用 Nginx，apache 等静态服务器加动态服务器处理。

### 2.1 使用缓存机制

由于网站访问数据的特点大都呈现为“二八定律”：即80%的业务访问集中在 20% 的数据上。

总的来说，就是用户只用到了总数据条目的一小部分，当网站发展到一定的规模，数据库 IO 成为性能瓶颈的时候，使用缓存的方法，将热门的数据缓存到内存中，不但可以减轻数据库的压力，还可以提高整体网站的数据访问速度。

这种缓存的方式可以通过程序代码将数据直接保存到内存中，例如 Map 或者 ConcurrentHashMap；另外一种就是使用缓存框架：Redis、Ehcache、Mencache 等。

由于内存的资源有限，因此如何创建缓存是一个值得考虑的问题。另外，对于缓存待定失效机制也是需要好好设计的，失效机制可以通过设置失效时间的方式进行设置；也可以通过对热门数据设置优先级，根据优先级设置不同的失效时间。

而且需要注意的是，当我们 delete 掉一条数据的时候，要考虑到一并删除数据所对应的缓存中的记录，还要考虑在删除这条缓存之前这条数据是否已经到达了缓存失效的时间等各种情况。

最后，使用缓存的时候还要考虑到缓存服务器发生故障时如何进行容错处理，是使用 N 多台服务器缓存相同的数据，然后通过分布式部署的方式对缓存数据进行控制（当一台发生故障的时候自动切换到其他的机器上去）；还是通过 Hash 一致性的方式，等待缓存服务器恢复正常使用的时候重新指定到该缓存服务器。Hash 一致性的另一个作用就是在分布式缓存服务器下对数据进行定位，将数据分布在不同缓存服务器上。关于 Hash 一致性算法可参考：[深入解读缓存（二）——一致性Hash算法](https://blog.csdn.net/liu765023051/article/details/49408099)

### 2.2 页面静态化技术（略）

在传统的 JSP 界面，前端界面的显示是通过后台服务器进行渲染后返回给前端浏览器进行解析执行。而当下所倡导的前后端分离，前端界面基本都是 HTML 网页代码，通过 Angular JS 或者 NodeJS 提供的路由向后端服务器发出请求获取数据，然后在浏览器对数据进行渲染，这样能很大程度上降低后端服务器的压力。

同时，我们还可以将这些静态的 HTML、CSS、JS、图片等静态资源放置在缓存服务器上，或者 CDN 服务器上，一般使用最多的应该是 CDN 服务器或者 Nginx 服务器提供的静态资源功能。

### 2.3 数据库优化★

#### 2.3.1 表结构优化

#### 2.3.2 SQL语法优化★

对于语法可使用 explain 关键字辨别慢查询，至少要达到 range 级别，要求是 ref 级别，如果可以是 consts 最好

**ALL、index、range、 ref、eq_ref、const、system、NULL（从左到右，性能从差到好）**

##### 2.3.2.1 避免索引失效

* 尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描

  ````sql
  SELECT id FROM t WHERE num is null
  -- 可以考虑设置一个默认值，例如“0”，然后非空限制，以确保列中没有null值
  SELECT id FROM t WHERE num=0
  ````

* 尽量避免在 where 子句中使用 != 或 <> 等比较符号，否则引擎将放弃使用索引而进行全表扫描

* 尽量避免在 where 子句中使用 or 来连接条件，因为如果一个字段有索引，一个字段没有索引，将导致引擎放弃使用索引而进行全表扫描

  （UNION 包含去重操作，UNION ALL 不会去重）

  ````sql
  SELECT id FROM t WHERE num=10 or num=20
  -- 修改为
  SELECT id FROM t WHERE num=10
  UNION all
  SELECT id FROM t WHERE num=20
  ````

* in 和 not in 要慎用，否则会导致全表扫描

  ````sql
  SELECT id FROM t WHERE num IN(1,2,3)
  -- 对于连续的数值，能用 between 就不要用 in
  SELECT id FROM t WHERE num BETWEEN 1 AND 3
  
  -- 同时，用 exists 代替 in 也是一个选择
  SELECT num FROM a WHERE num IN(SELECT num FROM b)
  -- 改为：
  SELECT num FROM a WHERE exists(SELECT 1 FROM b WHERE num=a.num)
  ````

* 尽量避免在 where 中对字段进行左模糊或者全模糊查询，将导致引擎放弃使用索引而进行全表扫描。若要提高效率，可以考虑使用搜索引擎

* 尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描

  ````sql
  SELECT id FROM t WHERE num/2=100
  -- 应改为：
  SELECT id FROM t WHERE num=100*2
  ````

* 尽量避免在 where 子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描

  ````sql
  SELECT id FROM t WHERE substring(name,1,3)=‘abc’
  -- 应改为：
  SELECT id FROM t WHERE name LIKE ‘abc%’
  -- mysql
  SELECT id FROM t WHERE name LIKE ‘abc%’
  -- oracle 中时间条件应该把 char 转换成 date
  SELECT id from t WHERE createdate >= to_date(‘2005-11-30’,‘yyyy-mm-dd’)
  ````

#### 2.3.3 SQL技巧优化★

##### 2.3.3.1 避免查询冗余字段

尽量避免使用`select * from`和全表扫描，只查询需要的字段信息。减少数据的获取量，可减轻 IO 和网络的压力

##### 2.3.3.2 分页查询提速

代码中写分页查询逻辑时，若 count 为 0 应直接返回，避免执行后面的分页语句。

同时还可以利用延迟关联或者子查询优化超多分页场景。

````sql
-- 先快速定位需要获取的 id 段，然后再关联
SELECT a.* FROM 表1 a, (select id from 表1 where 条件 LIMIT 100000,20 ) b where a.id=b.id
````

（说明：MySQL 并不是跳过 offset 行，而是取 offset+N 行，然后返回放弃前 offset 行，返回 N 行，那当 offset 特别大的时候，效率就非常的低下，要么控制返回的总页数，要么对超过特定阈值的页数进行 SQL 改写。）

##### 2.3.3.3 使用索引

考虑在`where`或`order by`涉及的列上建立索引，提高速度。
如果是多个字段的组合条件查询，则可考虑使用组合索引（查询一个组合索引比查询多个单索引快）；同时还要注意导致索引失效的情况。

* 索引：a_b_c

  ````sql
  -- 索引有效
  WHERE a=? AND b=? ORDER BY c
  WHERE a=? AND b IN(?,?) AND c>?
  WHERE a>?
  -- 索引无效
  WHERE b=? AND c=?
  WHERE b>?
  -- 索引部分有效
  WHERE a>? AND b=?
  WHERE a=? AND b>? AND c>?
  ````

* 索引：a_b

  ````sql
  -- 索引有效
  ORDER BY a
  ORDER BY a DESC, b DESC
  WHERE a=? AND b>?
  WHERE a>? ORDER BY a
  -- 索引无效
  ORDER BY b
  ORDER BY a ASC, b DESC
  WHERE a>? ORDER BY b -- 两字段组合索引不存在索引部分有效
  ````

#### 2.3.4 分表（冷热字段分离）●

分表是将一个大表按照一定的规则分解成多张具有独立存储空间的实体表，我们可以称为子表，每个表都对应三个文件，MYD 数据文件，.MYI 索引文件，.frm 表结构文件。这些子表可以分布在同一块磁盘上，也可以在不同的机器上。数据库读写操作的时候根据事先定义好的规则得到对应的子表名，然后去操作它。

例如：用户表 

用户的角色有很多种，可以通过枚举类型的方式将用户分为不同类别 category：学生、教师、企业等，这样的话，我们就可以根据类别 category 来对数据库进行分表，这样的话每次查询的时候现根据用户的类型锁定一个较小的范围。

不过分表之后，如果需要查询完整的顺序就需要使用多表操作了。

#### 2.3.5 分区

数据库分区是一种物理数据库设计技术，DBA 和数据库建模人员对其相当熟悉。虽然分区技术可以实现很多效果，但其主要目的是为了在特定的 SQL 操作中减少数据读写的总量以缩减响应时间。

分区和分表相似，都是按照规则分解表。不同在于分表将大表分解为若干个独立的实体表，而分区是将数据分段划分在多个位置存放，可以是同一块磁盘也可以在不同的机器。分区后，表面上还是一张表，但数据散列到多个位置了。数据库读写操作的时候操作的还是大表名字，DMS 自动去组织分区的数据。

当一张表中的数据变得很大的时候，读取数据，查询数据的效率非常低下，如果使用分表的方案，将数据分到不同的数据表中进行保存，会使得操作起来比较麻烦，同时，将同类的数据分别放在不同的表中的话，在搜索数据的时候需要遍历查询这些表中的数据。想进行 CRUD 操作还需要先找到对应的所有表，如果涉及到不同的表的话还要进行跨表操作，这样操作起来还是很麻烦的。

使用分区的方式可以解决这个问题，分区是将一张表中的数据按照一定的规则分到不同的区中进行保存，这样进行数据查询的时候如果数据的范围在同一个区域内那么就可以只对一个区中的数据进行操作，这样的话操作起来数据量更少，操作速度更快，而且该方法是对程序透明的，程序不需要进行任何的修改。

#### 2.3.6 使用索引★

### 2.4 冷热数据分离

正如前边提到的“二八定律”一样，网站的数据虽然很多，但是经常被访问的数据还是有限的，因此可以将这些相对活跃的数据进行分离，单独进行保存来提高处理效率。

其实前边使用缓存的思想就是一个很明显的分离数据库中活跃的数据的使用案例，将热门数据缓存在内存中。

还有一种场景就是：例如一个网站的注册用户量很大（千万级别），但是经常登录的用户只有百万级别，剩下的基本都是很长时间都没有进行登录操作，如果不把这些“僵尸用户”单独分离出去，那么我们每次查询其他登录用户的时候，就白白浪费了这些僵尸用户的查询操作。

### 2.5 使用NoSQL和Hadoop等技术

NoSQL 是一种非结构化的非关系型数据库，由于其灵活性，突破了关系型数据库的条条框框，可以灵活的进行操作，另外，因为 NoSQL 通过多个块存储数据的特点，其操作大数据的速度也是相当快的。

### 2.6 业务拆分●

为什么进行业务的拆分，归根结底上是将不同的业务数据表部署到不用的服务器上，分别查找对应的数据以满足网站的需求。各个应用之间用过指定的URL连接获取不同的服务。

例如：一个大型的购物网站就会将首页、商铺、订单、买家、卖家等拆分为不通的子业务，一方面将业务模块分归为不同的团队进行开发，另外一方面不同的业务使用的数据库表部署到不通的服务器上，使用了拆分的思想。当一个业务模块使用的数据库服务器发生故障也不会影响其他业务模块的数据库正常使用。另外，当其中一个模块的访问量激增的时候还可以动态的扩展这个模块使用到的数据库的数量从而满足业务的需求。

## 三、数据库一些特殊查询语句汇总

### 3.1 员工职位自身关联

![143821986](.\assets\143821986.png)![143902190](.\assets\143902190.png)

````sql
SELECT e.ename 上级, m.ename 下级 
FROM emp e 
LEFT JOIN emp m 
ON e.id = m.leaderid 
ORDER BY e.deptid,e.id
````

### 3.2 学生成绩查询

* 用一条 SQL 语句查询出每门课成绩都大于80分的学生姓名

  ![144158720](.\assets\144158720.png)

  ````sql
  SELECT s.sname FROM student s,grade g 
  WHERE s.id = g.sid 
  GROUP BY s.sname 
  HAVING MIN(g.score)>=80;
  ````

* 删除数据表中，内容重复但 ID 不重复的数据

  ![144242423](.\assets\144242423.png)

  ````sql
  DELETE FROM student WHERE id NOT IN 
  (SELECT d FROM (SELECT MIN(id) d FROM student GROUP BY sno,sname,sex,birthday) temp);
  ````

### 3.3 查询建立新表

用一个 SQL 语句从 table1，table2 中取出如 table3 所列格式的数据

![144448618](.\assets\144448618.png)![144622682](.\assets\144622682.png)![144754055](.\assets\144754055.png)

````sql
SELECT tb.dep 部门dep,
(SELECT yj FROM table1 ta WHERE ta.dep=tb.dep AND ta.mon='一月份') 一月份,
(SELECT yj FROM table1 ta WHERE ta.dep=tb.dep AND ta.mon='二月份') 二月份,
(SELECT yj FROM table1 ta WHERE ta.dep=tb.dep AND ta.mon='三月份') 三月份 
FROM table2 tb;
````

````sql
SELECT `name`,
MAX(CASE source WHEN '语文' THEN fenshu ELSE 0 END ) 语文,
MAX(CASE source WHEN '数学' THEN fenshu ELSE 0 END ) 数学,
MAX(CASE source WHEN '英语' THEN fenshu ELSE 0 END ) 英语,
SUM(fenshu) '总分'
FROM stu
GROUP BY `name`;
````

## 四、数据库事务

数据库事务就是一组 SQL 语句，这组 SQL 语句是一个逻辑工作单元。该单元**作为一个整体永久性地修改**或**作为一个整体取消**对数据库的修改。

### 4.1 数据库事务的ACID特性

* A —— Atomicity（原子性），一个事务中所包含的全部 SQL 语句是一个执行整体，不可分割，要么全执行，要么全取消。
* C —— Consistency（一致性），即数据库在事务操作前和事务处理后，其中的数据必须都要满足业务规范约束。如果在事务中出现错误，那么系统中的所有变化将自动地回滚，系统返回到原始状态。（例如银行转账，应先减后加，如果减后断电没有加上，不满足业务规范的约束，事务就要回滚，转账取消）
* I —— Isolation（隔离性），指的是多个事务并发地独立运行，而不能互相干扰，事务提交时根据当前数据库状态进行操作。
* D —— Durability（持久性），指的是事务在处理结束后，对数据库的修改是永久性的，即使系统故障也不会丢失。