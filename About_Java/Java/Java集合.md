# Java 集合

### 一、Java集合类接口继承关系和实现

List：有序，值可重复 -- `ArrayList.add("泽三炮");`
Set：无序，值不可重复 -- `TreeSet.add("提百万");`
Map：键值对结构，键不可重复，值可以重复 -- `HashMap.put("上单", "五杀姐");`

![1562914344229](D:\GitBook\About_Java\Java\assets\1562914344229.png)

### 二、Collection、Collections

#### Collection：

**是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。**Collection 接口在 Java 类库中有很多具体的实现。Collection 接口的意义是为各种具体的集合提供最大化的统一操作方式。
`Collection = List + Set`

#### Collections：

**是一个包装类。它包含各种有关集合操作的静态多态方法。**此类不能实例化，就像一个工具类，服务于 Java 的 Collection 框架。

示例：

````java
Collections.sort(list);// 排序(sort)
Collections.shuffle(list);// 乱序(shuffling)
````

### 三、set 集合的无序不重复

* List 是有序且重复的
* HashSet 是无序不重复的
* TreeSet 能保证元素的自然顺序（a-z 排序）
* LinkedHashSet 能保证元素的添加顺序

### 四、set 集合去重原则

系统类对象可以不用考虑去重原理，这里针对实体类对象进行讨论。

实体类对象的去重需要重写 equals() 和 hashCode() 方法。

当添加一个实体类到 set 集合时，会先执行 hashCode() 方法进行区分比较，默认的 hashCode() 方法是比较实体类对应的哈希值的，可以理解为对象在内存中的地址。

所以可以重写 hashCode() 方法中哈希值的生成逻辑，能加快去重的速度。

当 hashCode() 方法所比较的 hashCode() 相同的时候，才会继续调用 equals() 方法进行比较，如果还是相同，则不会存入到 set 集合中。

### 五、HashMap 和 HashTable 区别

#### 1. **线程安全不同**

HashTable 的几乎所有函数都是同步的，即线程安全的，支持多线程；
HashMap 的函数是非同步的，非线程安全的，在多线程中使用需要额外的同步处理。

#### 2. **对 null 值的处理不同**

HashMap 的 key、value 都可以为 null；
HashTable 的 key、value 都不可以为 null。

#### 3. **支持的遍历种类不同**

HashMap 只支持 Iterator（迭代器）遍历；
HashTable 支持 Iterator（迭代器）和 Enumeration（枚举器）两种方式遍历。

#### 4. 通过 Iterator 迭代器遍历时，遍历的顺序不同

HashMap 时“从前向后”地遍历数组；再对数组具体某一项对应的链表，从表头开始进行遍历；
HashTable 是“从后往前”地遍历数组；再对数组具体某一项对应的链表，从表头开始进行遍历。

#### 5. 继承和实现不同

#### 6. 容量的初始值和增加方式都不一样

#### 7. 添加 key-value 时的 hash 值算法不同

#### 8. 部分 API 不同

![1562914826916](D:\GitBook\About_Java\Java\assets\1562914826916.png)

### 六、Map 集合遍历

> 当循环中只需要获取 Map 的主键 key 时，迭代 keySet() 是正确的；但是，当需要主键 key 和取值 value 时，迭代 entrySet() 才是更高效的做法，其比先迭代 keySet() 后再去通过 get 取值性能更佳。

反例：

````java
//Map 获取value 反例:
HashMap<String, String> map = new HashMap<>();
for (String key : map.keySet()){
    String value = map.get(key);
}
````

正例：

````java
//Map 获取key & value 正例:
HashMap<String, String> map = new HashMap<>();
for (Map.Entry<String,String> entry : map.entrySet()){
    String key = entry.getKey();
    String value = entry.getValue();
}
````

