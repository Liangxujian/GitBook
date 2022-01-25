# Java 线程

## 一、显式创建 Java 线程

> 优势：显而易见的线程创建方式
>
> 缺点：每次都要 new 对象，当有大量请求时，数不清 new 了多少个对象了，如果不及时关闭会导致内存溢出，因此还要考虑线程管理等问题

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

## 二、线程死锁

有 AB 两个线程进行，线程 A 锁定了资源，同时执行操作的过程中需要请求线程 B 的资源，但是资源又被线程 A 占用，两个线程相互锁死

![1562916221444](D:\GitBook\About_Java\Java\assets\1562916221444.png)

## 三、乐观锁和悲观锁

### 乐观锁

乐观锁即是总认为不会发生并发问题，每次取数据的时候总会认为不会有其他线程对数据进行修改，因此不会上锁。只有在更新数据的时候会判断数据有没有被其他线程修改过。（数据库修改数据的操作一般是①取出数据；②修改数据；③更新数据）
这种一般会通过版本号的机制或 CAS 去实现。
版本号机制即在数据库中有一个 version 的字段记录数据被修改的次数，每次更新数据的时候需要对比一下 version 的值，如果不相同，则重新进行取数据操作。
CAS 即对修改前的数据进行记录，在更新数据的时候如果发现跟修改前的数据不一致，则重新进行取数据操作。

### 悲观锁

则是总是假设最坏的情况，认为每次取数据的时候总会有其他线程对数据进行修改，所以都会进行加锁，当其他线程想要访问数据的时候，都会阻塞挂起。在 Java 中，synchronized 的思想也是一个悲观锁。

## 四、Java 自带检测死锁工具（Jconsole）

1. 通过 cmd 窗口 -> 输入命令 jconsole
2. 选择需要检测的进程

## 五、线程池方式创建线程

### Executors

> 统一缺点：不支持自定义拒绝策略

#### 分类

```java
// 创建一个可缓存线程池
Executors.newCachedThreadPool();
// 优点：很灵活，弹性的线程池线程管理，用多少线程给多大的线程池，不用后及时回收，用则新建
// 缺点：一旦线程无限增长，会导致内存溢出

// 创建一个固定大小线程池
Executors.newFixedThreadPool();
// 优点：线程池大小固定，超出的线程会在队列中等待
// 缺点：不支持自定义拒绝策略，大小固定，难以扩展

// 创建一个固定大小线程池
Executors.newScheduledThreadPool();
// 优点：线程池大小固定，可以定时或周期性的执行任务
// 缺点：任务是单线程方式执行，一旦一个任务失败其他任务也受影响

// 创建一个单线程的线程池
Executors.newSingleThreadExecutor();
// 优点：创建一个单线程的线程池，保证线程的顺序执行
// 缺点：不适合并发。。（不懂为什么这种操作要用线程池。。为什么不直接用队列）
```

#### 示例一

```java
HandlerMappingSummaryJobRunnable handlerMappingSummaryJobRunnable = new HandlerMappingSummaryJobRunnable();
// 定义一个线程池
ExecutorService executor = Executors.newCachedThreadPool();
executor.execute(() -> {
	try {
        // 执行方法
		handlerMappingSummaryJobRunnable.run();
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
        // 关闭线程池
		executor.shutdown();
	}
});
```

#### 示例二

````java
// 创建固定长度线程池，长度为限制的线程数
ExecutorService executor = Executors.newFixedThreadPool(50);
for (Map<String,Object> agencyData:agencyDatas) {
	executor.execute(new Runnable() {
		@Override
		public void run() {
			try {
				// 执行方法
				iStdCommonService.doExeSubThreadWFByAgency(keyid,parameters,agencyData,reporttypeDatas,user);
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	});
}
// 执行完毕后关闭线程池
executor.shutdown();
````

### ThreadPoolExecutor

> 优点：集 Executors 创建线程池的优点于一身
>
> 缺点：无（因为 Executors 线程池的底层就是通过 ThreadPoolExecutor 来创建的）

```java
/**
 * ThreadPoolExecutor 构造方法
 * @param corePoolSize    核心线程数
 * @param maximumPoolSize 线程池中的最大线程数
 * @param keepAliveTime   控制 "idle Thread" 的空闲存活时间
 * @param unit            参数 keepAliveTime 的时间单位
 * @param workQueue       阻塞队列
 * @param threadFactory   线程工厂
 * @param handler         拒绝执行策略
 */
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler) {
	if (corePoolSize < 0 || maximumPoolSize <= 0 || maximumPoolSize < corePoolSize || keepAliveTime < 0)
		throw new IllegalArgumentException();
	if (workQueue == null || threadFactory == null || handler == null)
		throw new NullPointerException();
	this.acc = System.getSecurityManager() == null ? null : AccessController.getContext();
	this.corePoolSize = corePoolSize;
	this.maximumPoolSize = maximumPoolSize;
	this.workQueue = workQueue;
	this.keepAliveTime = unit.toNanos(keepAliveTime);
	this.threadFactory = threadFactory;
	this.handler = handler;
}
```

#### 参数说明

##### corePoolSize

核心线程数。在创建线程池之后，默认情况下线程池中并没有任何的线程，而是等待任务到来才创建线程去执行任务，当线程池中的线程数目达到 corePoolSize 后，新来的任务将会被添加到缓存队列中，也就是那个 workQueue，除非调用 `ThreadPoolExecutor#prestartAllCoreThreads()` 方法或者是 `ThreadPoolExecutor # prestartCoreThread()` 方法 (从这两个方法的名字就可以看出是预创建线程的意思，即在没有任务到来之前就创建 corePoolSize 个线程或一个线程)。

PS：很多人不知道这个数该填多少合适，其实也不必特别纠结，根据实际情况填写就好，实在不知道，就按照阿里工程师的写法取下列值就好了：
int NUMBER_OF_CORES = Runtime.getRuntime().availableProcessors();

##### maximumPoolSize

线程池中的最大线程数。表示线程池中最多可以创建多少个线程，很多人以为它的作用是这样的：【当线程池中的任务数超过 corePoolSize 后，线程池会继续创建线程，直到线程池中的线程数小于 maximumPoolSize】。其实这种理解是完全错误的。它真正的作用是：当线程池中的线程数等于 corePoolSize 并且 workQueue 已满，这时就要看当前线程数是否大于 maximumPoolSize，如果小于 maximumPoolSize 定义的值，则会继续创建线程去执行任务， 否则将会调用去相应的任务拒绝策略来拒绝这个任务。另外超过 corePoolSize 的线程被称做 "Idle Thread", 这部分线程会有一个最大空闲存活时间 (keepAliveTime)，如果超过这个空闲存活时间还没有任务被分配，则会将这部分线程进行回收。

##### keepAliveTime

控制 "idle Thread" 的空闲存活时间。这个 idle Thread 就是上面提到的超过 corePoolSize 后新创建的那些线程，默认情况下，只有当线程池中的线程数大于 corePoolSize，且这些 "idle Thread" 并没有被分配任务时，这个参数才会起作用。另外，如果调用了 `ThreadPoolExecutor#allowCoreThreadTimeOut(boolean)` 的方法，在线程池中的线程数不大于 corePoolSize，且这些 core Thread 也没有被分配任务时，keepAliveTime 参数也会起作用。

##### unit

参数 keepAliveTime 的时间单位，共 7 种取值，在 TimeUtil 中定义：

```java
TimeUnit.DAYS;              // 天
TimeUnit.HOURS;             // 小时
TimeUnit.MINUTES;           // 分钟
TimeUnit.SECONDS;           // 秒
TimeUnit.MILLISECONDS;      // 毫秒
TimeUnit.MICROSECONDS;      // 微妙
TimeUnit.NANOSECONDS;       // 纳秒
```

##### workQueue

阻塞队列。如果当前线程池中的线程数目 >=corePoolSize，则每来一个任务，会尝试将其添加到该队列当中，注意只要超过了 corePoolSize 就会把任务添加到该缓存队列，添加可能成功也可能不成功，如果成功的话就会等待空闲线程去执行该任务，若添加失败 (一般是队列已满)，就会根据当前线程池的状态决定如何处理该任务 (若线程数 < maximumPoolSize 则新建线程；若线程数 >= maximumPoolSize, 则会根据拒绝策略做具体处理)。

常用的阻塞队列有：

```java
1）ArrayBlockingQueue 　　　　　　// 基于数组的先进先出队列，此队列创建时必须指定大小；
2）LinkedBlockingQueue　　　　　　// 基于链表的先进先出队列，如果创建时没有指定此队列大小，则默认为Integer.MAX_VALUE；
3）synchronousQueue　　　　　　 　// 这个队列比较特殊，它不会保存提交的任务，而是将直接新建一个线程来执行新来的任务。
```

##### threadFactory

线程工厂。用来为线程池创建线程，当我们不指定线程工厂时，线程池内部会调用 `Executors.defaultThreadFactory()` 创建默认的线程工厂，其后续创建的线程优先级都是 `Thread.NORM_PRIORITY`。如果我们指定线程工厂，我们可以对产生的线程进行一定的操作。

##### handler

拒绝执行策略。当线程池的缓存队列已满并且线程池中的线程数目达到 maximumPoolSize，如果还有任务到来就会采取任务拒绝策略，通常有以下四种策略：

```java
ThreadPoolExecutor.AbortPolicy:　　　　　　　　 // 丢弃任务并抛出RejectedExecutionException异常。
ThreadPoolExecutor.DiscardPolicy：　　　　　　　// 也是丢弃任务，但是不抛出异常。
ThreadPoolExecutor.DiscardOldestPolicy：　　   // 丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
ThreadPoolExecutor.CallerRunsPolicy：　　　　  // 由调用线程处理该任务
```

#### 示例一

```java
import java.util.concurrent.ThreadPoolExecutor;

// 构造一个线程池
ThreadPoolExecutor threadPool = new ThreadPoolExecutor(1, 1, 10, TimeUnit.SECONDS, new ArrayBlockingQueue<Runnable>(1), new ThreadPoolExecutor.DiscardOldestPolicy());
threadPool.execute(() -> {
	try {
		// 执行 方法
		handlerMappingSummaryJobRunnable.run();
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		// 关闭线程池
		threadPool.shutdown();
	}
});
```

#### 示例二

```java
ThreadPoolExecutor threadPool = new ThreadPoolExecutor(50, 50, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<>());
for (Map<String,Object> agencyData:agencyDatas) {
	threadPool.execute(() -> {
		try {
            // 执行方法
			iStdCommonService.doExeSubThreadWFByAgency(keyid,parameters,agencyData,reporttypeDatas,user);
		} catch (Exception e) {
			e.printStackTrace();
		}
	});
}
// 关闭线程池
threadPool.shutdown();
```

