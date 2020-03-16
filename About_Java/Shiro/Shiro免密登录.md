# Shiro 免密登录

### 一、原始的登录认证方式

#### SysLoginController.java

````java
@Controller
public class SysLoginController extends AbstractController{
    ......
    
    @ResponseBody
	@RequestMapping(value = "/sys/login", method = RequestMethod.POST)
	public R login(String code, String pwd, String captcha) {
		try {
			Subject subject = ShiroUtils.getSubject();
			if(code.equals("") || code==null) {
				return R.error("账号或密码不正确");
			}
			if(pwd.equals("") || pwd==null) {
				return R.error("账号或密码不正确");
			}
             // 使用账号和密码，构造 token 对象
			UsernamePasswordToken token = new UsernamePasswordToken(code, pwd);
             // 执行这个方法的时候，shiro 会直接到我们自己创建的 MyRealm 类中寻找 doGetAuthenticationInfo 方法，校验账号密码的正确性
             // 账号密码验证正确，则跳转首页，在后台根据用户对应的角色、权限，加载对应菜单
			subject.login(token);
		} catch (UnknownAccountException e) {
			return R.error(e.getMessage());
		} catch (IncorrectCredentialsException e) {
			return R.error("账号或密码不正确");
		} catch (LockedAccountException e) {
			return R.error("账号已被锁定,请联系管理员");
		} catch (AuthenticationException e) {
			return R.error("账户验证失败");
		}

		SysLoginLogEntity sysLoginLog = new SysLoginLogEntity();
		//获取request
		HttpServletRequest request = HttpContextUtils.getHttpServletRequest();
		sysLoginLog.setUsername(getUser().getName());
		sysLoginLog.setOwnerId(getUserId());
		sysLoginLog.setOwnerDept(getUser().getDeptId());
		sysLoginLog.setLogintime(new Date());
		//设置IP地址
		sysLoginLog.setClientIp(IPUtils.getIpAddr(request));
		sysLoginService.insert(sysLoginLog);
		return R.ok();
	}
    
    ......
}
````

#### UserRealm.java

````java
@Component
public class UserRealm extends AuthorizingRealm {
    ......

    /**
	 * 认证(登录时调用)
	 */
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken authcToken) throws AuthenticationException {
		UsernamePasswordToken token = (UsernamePasswordToken)authcToken;

		//查询用户信息
		SysUserEntity user = new SysUserEntity();
		user.setCode(token.getUsername());
		user = sysUserDao.selectOne(user);

		//账号不存在
		if(user == null) {
			throw new UnknownAccountException("账号或密码不正确");
		}

		//账号锁定
		if(user.getStatus() == 0){
			throw new LockedAccountException("账号已被锁定,请联系管理员");
		}

		SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(user, user.getPwd(), ByteSource.Util.bytes(user.getSalt()), getName());
		return info;
	}
    
    ......
}
````

### 二、识别其他系统标识，免密登录改造

#### 2.1 创建一个枚举类（enum ）

LoginType.java

````java
public enum LoginType {
    // 密码登录
    PASSWORD("password"),
    // 免密登录
    NOPASSWD("nopassword");

    // 状态值
    private String code;

    private LoginType(String code) {
        this.code = code;
    }

    public String getCode() {
        return code;
    }
}
````

#### 2.2 自定义 token 继承 UsernamePasswordToken

通过构造方法来区分：账号密码登陆（password） 和 免密登陆（nopassword）

EasyTypeToken.java

````java
public class EasyTypeToken extends UsernamePasswordToken {
    private static final long serialVersionUID = -2564928913725078138L;

    private LoginType type;

    public EasyTypeToken() {
        super();
    }

    public EasyTypeToken(String username, String password, LoginType type, boolean rememberMe, String host) {
        super(username, password, rememberMe, host);
        this.type = type;
    }

    /**
     * 免密登录
     */
    public EasyTypeToken(String username) {
        super(username, "", false, null);
        this.type = LoginType.NOPASSWD;
    }

    /**
     * 账号密码登录
     */
    public EasyTypeToken(String username, String password) {
        super(username, password, false, null);
        this.type = LoginType.PASSWORD;
    }

    public LoginType getType() {
        return type;
    }

    public void setType(LoginType type) {
        this.type = type;
    }
}
````

#### 2.3 继承并重写 token 校验方法

MyCredentialsMatcher.java

````java
/**
 * 自定义密码认证方法，继承 HashedCredentialsMatcher，重写其中的 doCredentialsMatch，识别免密 token
 */
public class MyCredentialsMatcher extends HashedCredentialsMatcher {
    @Override
    public boolean doCredentialsMatch(AuthenticationToken authcToken, AuthenticationInfo info) {
        EasyTypeToken token = (EasyTypeToken) authcToken;
        // 这里如果类型是免密 Token 的话，不用进行常规校验，直接返回 true
        if (token.getType().equals(LoginType.NOPASSWD)) {
            return true;
        }
        boolean matches = super.doCredentialsMatch(authcToken, info);
        return matches;
    }
}
````

#### 2.4 修改原有登录 token

SysLoginController.java

````java
// 使用账号和密码，构造 token 对象
// UsernamePasswordToken token = new UsernamePasswordToken(code, pwd);
// 修改为构造自定义的 Token 对象
EasyTypeToken token = new EasyTypeToken(code, pwd);
````

#### 2.5 编写免登录接口

````java
/**
 * 识别其他系统带过来的标识，校验成功则登录，不用传统的账号密码方式
 */
@Controller
public class SysPageController {
    @RequestMapping("loginControl")
	public String loginControl(String sid) {
		if (StringUtils.isNotBlank(sid)) {
			// 查询数据库看suserid是否存在
			String aicUserId = sysLoginService.querySUserIdBySid(sid);
			if (StringUtils.isNotBlank(aicUserId)) {
				// 查询自身用户表看userid是否存在
				SysUserEntity sysUserEntity = sysLoginService.queryByAicUserId(aicUserId);
				if (sysUserEntity != null) {
					String code = sysUserEntity.getCode();
					try {
						Subject subject = ShiroUtils.getSubject();
						EasyTypeToken token = new EasyTypeToken(code);
						subject.login(token);
						return "redirect:index.html";
					} catch (AuthenticationException e) {
						e.printStackTrace();
						return "redirect:error.html";
					}
				}
			}
		}
		return "redirect:error.html";
	}
}
````

### 三、识别用户名，免密登录改造

> 步骤和识别其他系统标识大致一样，但是 2.5 步骤不同

#### 2.1 创建一个枚举类（enum ）

#### 2.2 自定义 token 继承 UsernamePasswordToken

#### 2.3 继承并重写 token 校验方法

#### 2.4 修改原有登录 token

#### 2.5 编写免登录接口

````java
public R login(String username) {
    Subject subject = ShiroUtils.getSubject();
    // 使用自定义 token 对象，只传入对象名表示免登录用户
    EasyTypeToken token = new EasyTypeToken(username);
    subject.login(token);
    // 省略日志等代码
    ......
    return R.ok();
}
````

