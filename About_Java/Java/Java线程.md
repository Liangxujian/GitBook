#### Java 线程

##### 一、Java 线程

线程的两种声明方式（实现接口的方式来创建线程会加灵活）

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

实现接口：

````java
public class Test02 implements Runnable {
    @Override
    public void run() {
        // TODO Auto-generated method stub		
    }
}
````

线程的五种状态/生命周期：新建 -> 就绪 -> 运行 -> 阻塞 -> 死亡

![1562916177659](D:\GitBook\About_Java\Java\assets\1562916177659.png)

##### 二、线程死锁

有 AB 两个线程进行，线程 A 锁定了资源，同时执行操作的过程中需要请求线程 B 的资源，但是资源又被线程 A 占用，两个线程相互锁死

![1562916221444](D:\GitBook\About_Java\Java\assets\1562916221444.png)

##### 三、乐观锁和悲观锁

###### 乐观锁

乐观锁即是总认为不会发生并发问题，每次取数据的时候总会认为不会有其他线程对数据进行修改，因此不会上锁。只有在更新数据的时候会判断数据有没有被其他线程修改过。（数据库修改数据的操作一般是①取出数据；②修改数据；③更新数据）
这种一般会通过版本号的机制或 CAS 去实现。
版本号机制即在数据库中有一个 version 的字段记录数据被修改的次数，每次更新数据的时候需要对比一下 version 的值，如果不相同，则重新进行取数据操作。
CAS 即对修改前的数据进行记录，在更新数据的时候如果发现跟修改前的数据不一致，则重新进行取数据操作。

###### 悲观锁

则是总是假设最坏的情况，认为每次取数据的时候总会有其他线程对数据进行修改，所以都会进行加锁，当其他线程想要访问数据的时候，都会阻塞挂起。在 Java 中，synchronized 的思想也是一个悲观锁。

##### 四、Java 自带检测死锁工具（Jconsole）

1. 通过 cmd 窗口 -> 输入命令 jconsole
2. 选择需要检测的进程