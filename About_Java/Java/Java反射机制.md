#### Java 反射机制

Java 反射就是把 Java 类中的各种成分映射成一个个的 Java 对象。
这里需要先了解一个 Java 类的加载过程：

1. 我们在 new 一个 class 类的时候，Java 会将新的 .java 文件编译成 .class 文件
2. 当我们实例化一个对象`Student s1 = new Student();`的时候，JVM会找到`Student.class`文件，并加载内存（堆）中
3. 把堆的对象地址加到栈中（引用）
   Java反射的本质就是得到 class 对象后，反向获取 Student 对象的各种信息（变量，方法等）。

代码理解：

````java
Class<?> cls1 = new Student().getClass();
System.out.println(cls1.getName());// 这里是输出类的具体路径

Class<?> cls2 = Student.class;
System.out.println(cls2.getName());

Class<?> cls3 = Class.forName("mmc.liang.test.Student");// 取得class对象
System.out.println(cls3.getName());

// 这里注意的一个点是，实例化的类要存在一个无参的构造方法
Object obj = cls3.newInstance();// 实例化对象，和使用关键字new一样
Student stu = (Student) obj;
System.out.println(stu.getStuName());// 实例化后才能使用类方法
````

实际上 new 是造成耦合的关键元凶，因此在工厂模式中配合 Java 的反射特性，可以很好地实现解耦。