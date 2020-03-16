# Shiro 实现

这里使用的案例是`Spring`+`SpringBoot`+`Mybatis`框架加上`Shiro`权限控制

这里`Shiro`主要由配置包下的`com.mmc.config.ShiroConfig`和 shiro 包下的`com.mmc.shiro.CustomRealm`进行实现

### 一、ShiroConfig.java

````java
@Configuration
public class ShiroConfig {
    /**
     * 注入 Shiro 拦截器工厂
     * @param securityManager 初始化 ShiroFilterFactoryBean 的时候需要注入 SecurityManager
     * @return
     */
    @Bean
    public ShiroFilterFactoryBean shirFilter(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        // 设置 SecurityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        // 设置默认未登录时、或 session 过期后跳转的默认界面
        // setLoginUrl 如果不设置值，默认会自动寻找 Web 工程根目录下的"/login.jsp"页面 或 "/login" 映射
        shiroFilter.setLoginUrl("/login.html");
        // 设置无权限时跳转的 url;
        shiroFilterFactoryBean.setUnauthorizedUrl("/notRole");

        /*
        anno        -> 开放
        roles[角色] -> 只特定角色可访问
        authc       -> 禁止访问
         */
        // 设置拦截器
        Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
        // 游客，开放权限
        filterChainDefinitionMap.put("/visitor/**", "anon");
        // 用户，需要角色权限 “user”
        filterChainDefinitionMap.put("/user/**", "roles[user]");
        // 管理员，需要角色权限 “admin”
        filterChainDefinitionMap.put("/admin/**", "roles[admin]");
        // 开放登陆接口
        filterChainDefinitionMap.put("/login", "anon");
        // 其余接口一律拦截
        // 主要这行代码必须放在所有权限设置的最后，不然会导致所有 url 都被拦截
        filterChainDefinitionMap.put("/**", "authc");

        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
        System.out.println("Shiro拦截器工厂类注入成功");
        return shiroFilterFactoryBean;
    }

    /**
     * 注入 securityManager
     * @return
     */
    @Bean
    public SecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 设置 realm.
        securityManager.setRealm(customRealm());
        return securityManager;
    }

    /**
     * 自定义身份认证 realm;
     * 必须写这个类，并加上 @Bean 注解，目的是注入 CustomRealm，
     * 否则会影响 CustomRealm类 中其他类的依赖注入
     */
    @Bean
    public CustomRealm customRealm() {
        return new CustomRealm();
    }
}
````

ShiroConfig 配置类主要用于定义拦截器的规则，以及在 springboot 项目启动的时候把设计好规则的拦截器注入到 Shiro 拦截器工厂类中

````java
// 设置拦截器
Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
// 游客，开放权限
filterChainDefinitionMap.put("/visitor/**", "anon");
// 用户，需要角色权限 “user”
filterChainDefinitionMap.put("/user/**", "roles[user]");
// 管理员，需要角色权限 “admin”
filterChainDefinitionMap.put("/admin/**", "roles[admin]");
// 开放登陆接口
filterChainDefinitionMap.put("/login", "anon");
// 其余接口一律拦截
// 主要这行代码必须放在所有权限设置的最后，不然会导致所有 url 都被拦截
filterChainDefinitionMap.put("/**", "authc");

shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
System.out.println("Shiro拦截器工厂类注入成功");
````

![1559808870813](D:\GitBook\About_Java\Shiro\assets\1559808870813.png)

### 二、CustomRealm.java

````java
public class CustomRealm extends AuthorizingRealm {
    @Autowired
    private IUserService userService;

    /**
     * 获取授权信息(验证权限时调用)
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("————权限认证————");
        String username = (String) SecurityUtils.getSubject().getPrincipal();
        // 获得该用户角色
        String role = userService.getRoleByUsername(username);
        Set<String> set = new HashSet<>();
        // 需要将 role 封装到 Set 作为 info.setRoles() 的参数
        set.add(role);
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        // 设置该用户拥有的角色
        info.setRoles(set);
        return info;
    }

    /**
     * 获取身份验证信息(登录时调用)
     * Shiro中，最终是通过 Realm 来获取应用程序中的用户、角色及权限信息的
     *
     * @param authenticationToken 用户身份信息 token
     * @return 返回封装了用户信息的 AuthenticationInfo 实例
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("————身份认证方法————");
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        // 从数据库获取对应用户名密码的用户
        String username = token.getUsername();
        System.out.println(username);
        String password = userService.getPasswordByUsername(username);
        if (null == password) {
            throw new AccountException("用户名不正确");
        } else if (!password.equals(new String((char[]) token.getCredentials()))) {
            throw new AccountException("密码不正确");
        }
        return new SimpleAuthenticationInfo(token.getPrincipal(), password, getName());
    }
}
````

CustomRealm 类继承了 AuthorizingRealm，这里需要区分两个非常类似的英文单词`AuthenticationInfo`（身份验证信息）和`AuthorizationInfo`（授权信息） 。

* `AuthenticationInfo`代表了用户的角色信息集合；
* `AuthorizationInfo`代表了角色的权限信息集合。

根据之前项目可知，Shiro 权限控制的设计思路为：`用户`、`角色`、`权限`均为多对多关系，Shiro 需要根据前端传来的用户名和密码，首先判断登录的用户是否合法，得到对应的角色；然后等到用户继续请求其他资源的时候，再根据角色用户进行授权。而这个过程就是Realm的实现过程。

![1559808870815](D:\GitBook\About_Java\Shiro\assets\1559808870815.png)

### 三、会话

Shiro 可以代替 Tomcat 等容器管理会话。传统的项目中，用户的特殊操作需要程序员在 Servlet 中不停的判断 Session 中是否包含 User 对象，而使用 Shiro 后可以避免这些重复操作，包括用户长时间无操作的超时重新登录的实现（Shiro 默认30分钟超时）。

````java
SecurityUtils.getSubject().getSession().setTimeout(1800000);
````

启用 Shiro 会话管理的另一个用途是可以针对不同的模块采取不同的会话处理。以淘宝为例，用户注册淘宝以后可以选择记住用户名和密码。之后再次访问就无需登陆。但是如果你要访问支付宝或购物车等链接依然需要用户确认身份。当然， Shiro 也可以创建使用容器提供的 Session 作为实现。

