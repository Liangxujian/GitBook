# Spring常见注解

## 一、config配置类

@Configuration 标注在类上，相当于把该类作为 Spring 的 xml 配置文件中的`<beans>`。作用为：配置 Spring 容器（应用上下文）

````java
/**
 * Shiro的配置文件
 */
/**
 * @Configuration 标注在类上，相当于把该类作为 Spring 的 xml 配置文件中的<beans>。作用为：配置 Spring 容器（应用上下文）
 */
@Configuration
public class ShiroConfig {
    @Bean("sessionManager")
    public SessionManager sessionManager(RedisShiroSessionDAO redisShiroSessionDAO,
                                         @Value("${renren.redis.open}") boolean redisOpen,
                                         @Value("${renren.shiro.redis}") boolean shiroRedis) {
        DefaultWebSessionManager sessionManager = new DefaultWebSessionManager();
        //设置session过期时间为1小时(单位：毫秒)，默认为30分钟
        sessionManager.setGlobalSessionTimeout(60 * 60 * 1000);
        sessionManager.setSessionValidationSchedulerEnabled(true);
        sessionManager.setSessionIdUrlRewritingEnabled(false);

        //如果开启redis缓存且renren.shiro.redis=true，则shiro session存到redis里
        if (redisOpen && shiroRedis) {
            sessionManager.setSessionDAO(redisShiroSessionDAO);
        }
        return sessionManager;
    }
    
    // ......
}
````

## 二、entity实体类

````java
/**
 * 商品管理
 */
@TableName("tb_goods")
public class GoodsEntity implements Serializable {
    private static final long serialVersionUID = 1L;

    /**
     * 商品编号
     */
    @TableId(value = "user_id", type = IdType.AUTO)
    private Long goodsId;
    /**
     * 商品名
     */
    @NotBlank(message = "商品名不能为空", groups = {AddGroup.class, UpdateGroup.class})
    private String name;
    /**
     * 入库时间
     */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+08")
    private Date createTime;
    /**
     * 质检员ID列表
     */
    @TableField(exist = false)
    private List<String> checkUserIdList;
    /**
     * 质检员密码
     */
    @NotBlank(message = "密码不能为空", groups = AddGroup.class)
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    private String password;
    
    // ......
}
````

### 2.1 实体类主键类型（工具包）

@TableId接受两个参数（也可以不写参数）

value = 主键列名；type = 主键类型

````java
package com.baomidou.mybatisplus.enums;

public enum IdType {
    AUTO(0, "数据库ID自增"),
    INPUT(1, "用户输入ID"),
    ID_WORKER(2, "全局唯一ID"),
    UUID(3, "全局唯一ID"),
    NONE(4, "该类型为未设置主键类型"),
    ID_WORKER_STR(5, "字符串全局唯一ID");
    
    // ......
}
````

### 2.2 实体类变量校验规则

变量的非空检查，需要编写添加和修改两个规则的接口，并增加一个校验工具类

````java
/**
 * 新增数据校验组 Group
 */
public interface AddGroup {}
````

````java
/**
 * 更新数据校验组 Group
 */
public interface UpdateGroup {}
````

````java
/**
 * hibernate-validator校验工具类
 * 参考文档：http://docs.jboss.org/hibernate/validator/5.4/reference/en-US/html_single/
 */
public class ValidatorUtils {
    private static Validator validator;

    static {
        validator = Validation.buildDefaultValidatorFactory().getValidator();
    }

    /**
     * 校验对象
     *
     * @param object 待校验对象
     * @param groups 待校验的组
     * @throws RRException 校验不通过，则报RRException异常
     */
    public static void validateEntity(Object object, Class<?>... groups)
            throws RRException {
        Set<ConstraintViolation<Object>> constraintViolations = validator.validate(object, groups);
        if (!constraintViolations.isEmpty()) {
            ConstraintViolation<Object> constraint = (ConstraintViolation<Object>) constraintViolations.iterator().next();
            throw new RRException(constraint.getMessage());
        }
    }
}
````

### 2.3 时间变量格式控制

@JsonFormat  与  @DataTimeFormat

> 注解 @JsonFormat 主要是后台到前台的时间格式的转换
> 注解 @DataFormAT 主要是前后到后台的时间格式的转换

#### 2.3.1 @JsonFormat

##### 2.3.1.1 maven 导入相关 jar 包

````xml
<!--JsonFormat-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.8.8</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.8.8</version>
</dependency>

<dependency>
    <groupId>org.codehaus.jackson</groupId>
    <artifactId>jackson-mapper-asl</artifactId>
    <version>1.9.13</version>
</dependency>
````

##### 2.3.1.2 使用案例

````java
import java.util.Date;
  
import com.fasterxml.jackson.annotation.JsonFormat;
  
public class TestClass {
    //设置时区为上海时区，时间格式自己据需求定。
    @JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")
    private Date testTime;
     
    public Date gettestTime() {
        return testTime;
    }
  
    public void settestTime(Date testTimee) {
        this.testTime= testTime;
    }
}
````

##### 2.3.1.3 注解解析

`@JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")`

* pattern：表示需要转换的时间日期格式
* timezone：表示时间设置为东八区，避免时间在转换中有误差

PS：@JsonFormat注解可以在属性的上方，同样可以在属性对应的get方法上，两种方式没有区别

#### 2.3.2 @DataTimeFormat

##### 2.3.2.1 maven 导入相关 jar 包

````xml
<!-- joda-time -->
<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.3</version>
</dependency>
````

##### 2.3.2.2 使用案例

````java
@DateTimeFormat(pattern = "yyyy-MM-dd")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
private Date symstarttime;
 
@DateTimeFormat(pattern = "yyyy-MM-dd")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
private Date symendtime;
````

### 2.4 实体类变量读写控制（工具包）

````java
package com.fasterxml.jackson.annotation;

@Target({ElementType.ANNOTATION_TYPE, ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@JacksonAnnotation
public @interface JsonProperty {
    // ......

    public static enum Access {
        AUTO,
        READ_ONLY,
        WRITE_ONLY,
        READ_WRITE;

        private Access() {}
    }
}
````

## 三、service业务逻辑类

````java
@Service("goodsService")
public class GoodsServiceImpl extends ServiceImpl<GoodsDao, GoodsEntity> implements GoodsService {
    @Override
    public PageUtils queryPage(Map<String, Object> params) {
        // ......
    }
    
    @Override
    @DataSource(name = DataSourceNames.SECOND)
    public SysUserEntity queryUser2(Long userId) {
        // ......
    }
}
````

## 四、controller视图界面类

````java
/**
 * 商品管理
 */
@RestController
@RequestMapping("sys/goods")
public class GoodsController extends AbstractController {
    @Autowired
    private GoodsService goodsService;

    /**
     * 列表
     * data: {objType: 3}
     */
    @RequestMapping("/list")
    @RequiresPermissions("sys:goods:list")
    public R list(@RequestParam Map<String, Object> params) {
        PageUtils page = goodsService.queryPage(params);
        return R.ok().put("page", page);
    }
    
    /**
     * 信息
     * $.get(baseURL + "sys/goods/info/" + goodsId, function (r) {vm.goods = r.goods;});
     */
    @RequestMapping("/info/{goodsId}")
    @RequiresPermissions("sys:goods:info")
    public R info(@PathVariable("goodsId") Long goodsId) {
        GoodsEntity goods = goodsService.selectById(goodsId);
        return R.ok().put("goods", goods);
    }

    /**
     * 保存
     * ajax参数：data: JSON.stringify(vm.goods)
     */
    @RequestMapping("/save")
    @RequiresPermissions("sys:goods:save")
    public R save(@RequestBody GoodsEntity goods) {
        ValidatorUtils.validateEntity(goods, UpdateGroup.class);
        goodsService.insert(goods);
        return R.ok();
    }
    
    /**
     * 登录
     * var data = "username=" + vm.username + "&password=" + vm.password + "&captcha=" + vm.captcha;
     */
    @ResponseBody
    @RequestMapping(value = "/sys/login", method = RequestMethod.POST)
    public R login(String username, String password, String captcha) {
        // ......
        return R.ok();
    }
    
    // ......
}
````

## 五、切面类

````java
/**
 * 系统日志，切面处理类
 */
@Aspect
@Component
public class SysLogAspect {
    @Autowired
    private SysLogService sysLogService;

    @Pointcut("@annotation(com.chinauip.common.annotation.SysLog)")
    public void logPointCut() {}

    @Around("logPointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        long beginTime = System.currentTimeMillis();
        //执行方法
        Object result = point.proceed();
        //执行时长(毫秒)
        long time = System.currentTimeMillis() - beginTime;
        //保存日志
        saveSysLog(point, time);
        return result;
    }

    private void saveSysLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();
        SysLogEntity sysLog = new SysLogEntity();
        SysLog syslog = method.getAnnotation(SysLog.class);
        if (syslog != null) {
            //注解上的描述
            sysLog.setOperation(syslog.value());
        }
        //请求的方法名
        String className = joinPoint.getTarget().getClass().getName();
        String methodName = signature.getName();
        sysLog.setMethod(className + "." + methodName + "()");
        //请求的参数
        Object[] args = joinPoint.getArgs();
        try {
            String params = new Gson().toJson(args[0]);
            sysLog.setParams(params);
        } catch (Exception e) {}
        //获取request
        HttpServletRequest request = HttpContextUtils.getHttpServletRequest();
        //设置IP地址
        sysLog.setIp(IPUtils.getIpAddr(request));
        //用户名
        String username = ((SysUserEntity) SecurityUtils.getSubject().getPrincipal()).getUsername();
        sysLog.setUsername(username);
        sysLog.setTime(time);
        sysLog.setCreateDate(new Date());
        //保存系统日志
        sysLogService.insert(sysLog);
    }
}
````

切面注解类：

````java
/**
 * 系统日志注解
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface SysLog {
    String value() default "";
}
````

## 六、数据库连接类

````java
/**
 * 多数据源注解
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface DataSource {
    String name() default "";
}
````

````java
/**
 * 多数据源，切面处理类
 */
@Aspect
@Component
public class DataSourceAspect implements Ordered {
    protected Logger logger = LoggerFactory.getLogger(getClass());

    @Pointcut("@annotation(com.chinauip.datasources.annotation.DataSource)")
    public void dataSourcePointCut() {}

    @Around("dataSourcePointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        MethodSignature signature = (MethodSignature) point.getSignature();
        Method method = signature.getMethod();

        DataSource ds = method.getAnnotation(DataSource.class);
        if (ds == null) {
            DynamicDataSource.setDataSource(DataSourceNames.FIRST);
            logger.debug("set datasource is " + DataSourceNames.FIRST);
        } else {
            DynamicDataSource.setDataSource(ds.name());
            logger.debug("set datasource is " + ds.name());
        }

        try {
            return point.proceed();
        } finally {
            DynamicDataSource.clearDataSource();
            logger.debug("clean datasource");
        }
    }

    @Override
    public int getOrder() {
        return 1;
    }
}
````

````java
/**
 * 增加多数据源，在此配置
 */
public interface DataSourceNames {
    String FIRST = "first";
    String SECOND = "second";
}
````

````java
/**
 * 动态数据源
 */
public class DynamicDataSource extends AbstractRoutingDataSource {
    private static final ThreadLocal<String> contextHolder = new ThreadLocal<>();

    public DynamicDataSource(DataSource defaultTargetDataSource, Map<Object, Object> targetDataSources) {
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        super.setTargetDataSources(targetDataSources);
        super.afterPropertiesSet();
    }

    @Override
    protected Object determineCurrentLookupKey() {
        return getDataSource();
    }

    public static void setDataSource(String dataSource) {
        contextHolder.set(dataSource);
    }

    public static String getDataSource() {
        return contextHolder.get();
    }

    public static void clearDataSource() {
        contextHolder.remove();
    }
}
````

````java
/**
 * 配置多数据源
 */
@Configuration
public class DynamicDataSourceConfig {
    @Bean
    @ConfigurationProperties("spring.datasource.druid.FIRST")
    public DataSource firstDataSource() {
        return DruidDataSourceBuilder.create().build();
    }

    @Bean
    @ConfigurationProperties("spring.datasource.druid.SECOND")
    public DataSource secondDataSource() {
        return DruidDataSourceBuilder.create().build();
    }

    @Bean
    @Primary
    public DynamicDataSource dataSource(DataSource firstDataSource, DataSource secondDataSource) {
        Map<Object, Object> targetDataSources = new HashMap<>();
        targetDataSources.put(DataSourceNames.FIRST, firstDataSource);
        targetDataSources.put(DataSourceNames.SECOND, secondDataSource);
        return new DynamicDataSource(firstDataSource, targetDataSources);
    }
}
````

## 七、application启动类

````java
@SpringBootApplication
@MapperScan(basePackages = {"com.chinauip.modules.*.dao"})
public class AdminApplication extends SpringBootServletInitializer {

	public static void main(String[] args) {
		SpringApplication.run(AdminApplication.class, args);
	}

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(AdminApplication.class);
	}
}
````

