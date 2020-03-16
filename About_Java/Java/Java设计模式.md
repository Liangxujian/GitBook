#### Java设计模式（参考：菜鸟教程）

#### 一、单例模式

###### 使用场景

一个全局使用的类频繁地创建与销毁，需要控制新建实例的数目，以节省系统资源的时候。

###### 注意事项

1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

###### 饿汉模式

````java
public class HungerSingleton {
	private static HungerSingleton singleton = new HungerSingleton();
	
	public static HungerSingleton getInstance(){
		return singleton;
	}
	
	private HungerSingleton() {
		// TODO Auto-generated constructor stub
	}
}
````



###### 懒汉模式

*（这里注意判断两次single是否==null，同时中间使用`synchronized(obj)`锁定资源，最后创建新的单例对象）*

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
	
	private LazySingleton() {
		// TODO Auto-generated constructor stub
	}
}
````

#### 二、代理模式

在代理模式（Proxy Pattern）中，一个类代表另一个类的功能。这种类型的设计模式属于结构型模式。**当我们需要在访问一个类时做一些额外控制，则可考虑使用代理模式。**
**当我们需要做某些操作时，不是直接调用对应方法，而是调用该方法的代理类，由此可在调用该方法的前后添加一些额外操作。**

###### 优点

1. 职责清晰
2. 高扩展性
3. 智能化

###### 缺点

1. 由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。
2. 实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

###### 使用场景

按职责来划分，通常有以下使用场景：

* 远程代理
* 虚拟代理
* Copy-on-Write 代理
* 保护（Protect or Access）代理
* Cache代理
* 防火墙（Firewall）代理
* 同步化（Synchronization）代理
* 智能引用（Smart Reference）代理

###### 注意事项

1. 和适配器模式的区别：适配器模式主要改变所考虑对象的接口，而代理模式不能改变所代理类的接口。
2. 和装饰器模式的区别：装饰器模式为了增强功能，而代理模式是为了加以控制。

###### 一些应用实例

1. Windows 里面的快捷方式
2. 猪八戒去找高翠兰结果是孙悟空变的，可以这样理解：把高翠兰的外貌抽象出来，高翠兰本人和孙悟空都实现了这个接口，猪八戒访问高翠兰的时候看不出来这个是孙悟空，所以说孙悟空是高翠兰代理类
3. 买火车票不一定在火车站买，也可以去代售点
4. 一张支票或银行存单是账户中资金的代理。支票在市场交易中用来代替现金，并提供对签发人账号上资金的控制
5. spring aop

###### 静态代理

* 优点：方便好理解，扩展原功能，不侵入原代码。
* 缺点：当多几个实现方法的时候，代理每个都要重写。

###### 动态代理

* 优点：解决了静态代理多个实现类重复写的问题。

#### 三、工厂模式

工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

###### 使用场景

1. 日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方；
2. 数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时；
3. 设计一个连接服务器的框架，需要三个协议，“POP3”、“IMAP”、“HTTP”，可以把这三个作为产品类，共同实现一个接口。

###### 注意事项

作为一种创建类模式，在任何需要生成复杂对象的地方，都可以使用工厂方法模式。有一点需要注意的地方就是`复杂对象适合使用工厂模式`，而`简单对象，特别是只需要通过 new 就可以完成创建的对象，无需使用工厂模式`。如果使用工厂模式，就需要引入一个工厂类，会增加系统的复杂度。

###### 简单工厂模式

* 缺点：扩展性差（多一种产品就要新建一个产品类，并且修改工厂类方法）；不支持不同产品需要不同参数创建的情况

````java
public class SimpleNoodlesFactory {
    public static final int TYPE_LZ = 1;//兰州拉面
    public static final int TYPE_PM = 2;//泡面
    public static final int TYPE_GK = 3;//干扣面

    public static INoodles createNoodles(int type) {
        switch (type) {
            case TYPE_LZ:
                return new LzNoodles();
            case TYPE_PM:
                return new PaoNoodles();
            case TYPE_GK:
            default:
                return new GankouNoodles();
        }
    }
}
````

###### 一般工厂模式

###### 抽象工厂模式

#### 四、观察者模式

当对象间存在一对多关系时，则使用观察者模式（Observer Pattern）。比如，当一个对象被修改时，则会自动通知它的依赖对象。观察者模式属于行为型模式。

#### 五、享元模式

**享元模式（Flyweight Pattern）主要是根据唯一标识码去判断，减少创建对象的数量，减少内存占用和提高性能。**这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式。
享元模式尝试重用现有的同类对象，如果未找到匹配的对象，则创建新对象。例如有一个实体类“圆”，根据其中特性“颜色”，判断是否需要新建对象。

###### 优点

大大减少对象的创建，降低系统的内存，使效率提高。

###### 缺点

提高了系统的复杂度，需要分离出外部状态和内部状态，而且外部状态具有固有化的性质，不应该随着内部状态的变化而变化，否则会造成系统的混乱。

###### 使用场景

1. 系统有大量相似对象。
2. 需要缓冲池的场景。

###### 注意事项

1. **注意划分外部状态和内部状态，否则可能会引起线程安全问题**
2. **这些类必须有一个工厂对象加以控制**

````java
public class ShapeFactory {
	private static final HashMap<String, Shape> circleMap = new HashMap<>();

	public static Shape getCircle(String color) {
		Circle circle = (Circle) circleMap.get(color);

		if (circle == null) {
			circle = new Circle(color);
			circleMap.put(color, circle);
			System.out.println("Creating circle of color : " + color);
		}
		return circle;
	}
}
````

#### 六、策略模式

在策略模式（Strategy Pattern）中，一个类的行为或其算法可以在运行时更改。这种类型的设计模式属于行为型模式。
在策略模式中，我们创建表示各种策略的对象和一个行为随着策略对象改变而改变的 context 对象。策略对象改变 context 对象的执行算法。

###### 优点

1. 算法可以自由切换
2. 避免使用多重条件判断
3. 扩展性良好

###### 缺点

1. 策略类会增多
2. 所有策略类都需要对外暴露

###### 使用场景

1. 如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。
2. 一个系统需要动态地在几种算法中选择一种。
3. 如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。

###### 注意事项

如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。

###### 步骤

* 创建一个策略接口（Strategy）：

````java
public interface Strategy {
	public int doOperation(int num1, int num2);
}
````

* 创建三个策略实现类：

加法（OperationAdd.java）：

````java
public class OperationAdd implements Strategy {
	@Override
	public int doOperation(int num1, int num2) {
		// TODO Auto-generated method stub
		 return num1 + num2;
	}
}
````

减法（OperationSubstract.java）：

````java
public class OperationSubstract implements Strategy {
	@Override
	public int doOperation(int num1, int num2) {
		// TODO Auto-generated method stub
		return num1 - num2;
	}
}
````

乘法（OperationMultiply.java）：

````java
public class OperationMultiply implements Strategy {
	@Override
	public int doOperation(int num1, int num2) {
		// TODO Auto-generated method stub
		return num1 * num2;
	}
}
````

* 策略调用类（Context.java）：

````java
public class Context {
	private Strategy strategy;

	public Context(Strategy strategy) {
		this.strategy = strategy;
	}

	public int executeStrategy(int num1, int num2) {
		return strategy.doOperation(num1, num2);
	}
}
````

* 策略使用类（StrategyPatternDemo.java）：通过传入不同的策略实现类对象到调用类中，实现对不同策略的使用

````java
public class StrategyPatternDemo {
	public static void main(String[] args) {
		Context context = new Context(new OperationAdd());
		System.out.println("10 + 5 = " + context.executeStrategy(10, 5));

		context = new Context(new OperationSubstract());
		System.out.println("10 - 5 = " + context.executeStrategy(10, 5));

		context = new Context(new OperationMultiply());
		System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
	}
}
````