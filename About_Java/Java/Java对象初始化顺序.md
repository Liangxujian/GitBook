# Java对象初始化顺序

````java
public class Son extends Father {
    // 子类构造代码块1
    // 子类静态代码块
    // 子类构造代码块2
    // 子类构造方法
    public static void main(String[] args) {
        System.out.println("11");
        Son son = new Son();
        System.out.println("22");
    }
}

class Father {
    // 父类构造代码块1
    // 父类静态代码块
    // 父类构造代码块2
    // 父类构造方法
}
````

①父类静态代码块
②子类静态代码块（子类实例化前会先实例化父类）
③子类main方法打印（“11”）
④父类构造代码块1
⑤父类构造代码块2
⑥父类构造方法
⑦子类构造代码块1
⑧子类构造代码块2
⑨子类构造方法
⑩子类main方法打印（“22”）

类加载
-> **静态属性**初始化，且**只初始化一次**
-> **静态代码块**执行，且**只执行一次**
-> **非静态属性**初始化，*只在实例化的时候执行*，且**每次都初始化一次**
-> **构造块**
-> **构造方法**