# SpringBoot 事务使用

本篇笔记参考自博客：[SpringBoot 使用 @Transactional 注解配置事务](https://blog.csdn.net/abysscarry/article/details/80189232)

### 一、使用

#### 1. 启动类 AdminApplication.java 添加注解：`@EnableAutoConfiguration`

````java
@EnableAutoConfiguration
@SpringBootApplication
@MapperScan(basePackages = {"com.chinauip.modules.*.dao"})
public class AdminApplication extends SpringBootServletInitializer {
	private static final Logger log = LoggerFactory.getLogger(AdminApplication.class);
    ......
}
````

#### 2. service 层方法添加注解：`@Transactional`

````java
@Override
@Transactional(rollbackFor = {RuntimeException.class, Error.class})
public void updateAuditStatus(Map<String, Object> params) {
    baseMapper.updateAuditStatus(params);
    subjectService.check(params);
    // throw new RuntimeException("发生异常");
}
````

**这里注意把两个执行 SQL 的方法都包含进去**

测试的时候可以手动模拟抛出异常，看事务会不会自动回滚，数据有没有插入到数据库。

#### 3. 在 controller 业务层调用 service 方法时用 try-catch 捕抓，返回异常信息到浏览器

### 二、说明

虽然 @Transactional 注解可以作用于接口、接口方法、类以及类方法上，但是 Spring 建议不要在接口或者接口方法上使用该注解，因为这只有在使用基于接口的代理时它才会生效。另外，@Transactional 注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。如果你在 protected、private 或者默认可见性的方法上使用 @Transactional 注解，这将被忽略，也不会抛出任何异常。

默认情况下，只有来自外部的方法调用才会被 AOP 代理捕获，也就是，类内部方法调用本类内部的其他方法并不会引起事务行为，即使被调用方法使用 @Transactional 注解进行修饰。

Spring 的事务管理默认是针对 `unchecked exception` 回滚，也就是默认对 Error 异常和 RuntimeException 异常以及其子类进行事务回滚，且必须抛出异常，若使用 try-catch 对其异常捕获则不会进行回滚！（Error 异常和 RuntimeException 异常抛出时不需要方法调用 throws 或 try-catch 语句）；而 `checked exception` 则必须用 try 语句块进行处理或者把异常交给上级方法处理总之就是必须写代码处理它，所以必须在 service 捕获异常，然后再次抛出，这样事务方才起效。