# Java 反射机制

Java 正射：当我们需要使用某个类时，必定知道它是什么类，是用来做什么的。于是我们直接对这个类进行实例化，之后使用这个类对象进行操作。

````java
Phone phone = new Phone();
phone.setPrice(4);
````

Java 反射：与「正射」相反，我们一开始并不知道要初始化的类对象是什么，自然也无法使用 new 关键字来创建对象了。这时可以通过 JVM 中的 class 对象，反向获取对象的属性、方法等各种信息。

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
3. 把堆的对象地址加到栈中（引用）
   Java 反射的本质就是得到 class 对象后，反向获取 Student 对象的各种信息（变量，方法等）

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