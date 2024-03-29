# Mybatis 常见查询

> 本篇笔记参考自博客：[MyBatis学习-映射文件标签篇(select、resultMap)](https://www.cnblogs.com/libra0920/p/6208587.html)

### 一、 映射文件的顶级元素

* **select：**映射查询语句
* **insert：**映射插入语句
* **update：**映射更新语句
* **delete：**映射删除语句
* **sql：**可以重用的sql代码块
* **resultMap：**最复杂，最有力量的元素，用来描述如何从数据库结果集中加载你的对象
* cache：配置给定命名空间的缓存
* cache-ref：从其他命名空间引用缓存配置

### 二、select 标签的属性信息

````xml
<select id="selectUser" 
        parapeterType="int" 
        resultType="hashmap" 
        resultMap="USER_RESULT_MAP" 
        flushCache="false" 
        useCache="true" 
        timeout="10000" 
        fetchSize="256" 
        statementType="PREPARED" 
        resultSetType="FORWORD_ONLY"
></select>
<!-- 
	1. id（必须配置）
　　　　id是命名空间中的唯一标识符，可被用来代表这条语句
　　　　一个命名空间（namespace）对应一个dao接口
　　　　这个id也应该对应dao里面的某个方法（sql相当于方法的实现），因此id应该与方法名一致

	2. parapeterType（可选配置，默认由mybatis自动选择处理）
　　　　将要传入语句的参数的完全限定名或别名，如果不配置，mybatis会通过ParamterHandler根据参数类型默认选择合适的typeHandler进行处理
　　　　paramterType 主要指定参数类型，可以是int, short, long, string等类型，也可以是复杂类型（如对象）

	3. resultType（resultType 与 resultMap 二选一配置）
　　　　用来指定返回类型，指定的类型可以是基本类型，也可以是java容器，也可以是javabean

	4. resultMap（resultType 与 resultMap 二选一配置）
　　　　用于引用我们通过 resultMap 标签定义的映射类型，这也是mybatis组件高级复杂映射的关键

	5. flushCache（可选配置）
　　　　将其设置为true，任何时候语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false

	6. useCache（可选配置）
　　　　将其设置为true，会导致本条语句的结果被二级缓存，默认值：对select元素为true

	7. timeout（可选配置）
　　　　这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数，默认值为：unset（依赖驱动）

	8. fetchSize（可选配置）
　　　　这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为：unset（依赖驱动）

	9. statementType（可选配置）
　　　　STATEMENT, PREPARED或CALLABLE的一种，这会让MyBatis使用选择Statement, PrearedStatement或CallableStatement，默认值：PREPARED

	10. resultSetType（可选配置）
　　　　FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为：unset（依赖驱动）
-->
````

### 三、resultMap 标签的属性信息

````xml
<!-- 
	1. type 对应的返回类型，可以是javabean, 也可以是其它，例如实体类
	2. id 必须唯一， 用于标示这个resultMap的唯一性，在使用resultMap的时候，就是通过id引用
	3. extends 继承其他resultMap标签
-->
<resultMap type="" id="" extends="">
	<!-- 
		1. id 唯一性，注意啦，这个id用于标示这个javabean对象的唯一性， 不一定会是数据库的主键（不要把它理解为数据库对应表的主键）
		2. property 属性对应javabean的属性名
		3. column 对应数据库表的列名
		（这样，当javabean的属性与数据库对应表的列名不一致的时候，就能通过指定这个保持正常映射了）
	-->
	<id property="" column=""/>
	<!-- result 与id相比，对应普通属性 -->    
	<result property="" column=""/>
        
	<!-- constructor 对应javabean中的构造方法 -->
	<constructor>
		<!-- idArg 对应构造方法中的id参数 -->
        <idArg column=""/>
        <!-- arg 对应构造方法中的普通参数 -->
        <arg column=""/>
    </constructor>
   
	<!-- 
		collection 为关联关系，是实现一对多的关键 
		1. property 为javabean中容器对应字段名
		2. ofType 指定集合中元素的对象类型
		3. select 使用另一个查询封装的结果
		4. column 为数据库中的列名，与select配合使用
    -->
	<collection property="" column="" ofType="" select="">
		<!-- 当使用select属性时，无需下面的配置 -->
		<id property="" column=""/>
		<result property="" column=""/>
	</collection>
        
	<!-- 
		association 为关联关系，是实现一对一的关键
		1. property 为javabean中容器对应字段名
		2. javaType 指定关联的类型，当使用select属性时，无需指定关联的类型
		3. select 使用另一个select查询封装的结果
		4. column 为数据库中的列名，与select配合使用
	-->
	<association property="" column="" javaType="" select="">
		<!-- 使用select属性时，无需下面的配置 -->
		<id property="" column=""/>
		<result property="" column=""/>
	</association>
</resultMap>

<resultMap type="com.mmc.project.entity.UserEntity" id="userList">
</resultMap>
````

### 四、insert 标签的属性信息

````xml
<insert id="insertProject" 
        paramterType="projectInfo"
        useGeneratedKeys="true"
        keyProperty="projectId"
></insert>
<!-- 
	1. id（必须配置）
		同 select 标签
	2. paramterType
		同 select 标签
	3. useGeneratedKeys（可选配置，与 keyProperty 相配合）
		设置为true，并将 keyProperty 属性设为数据库主键对应的实体对象的属性名称
	4. keyProperty（可选配置，与 useGeneratedKeys 相配合）
		用于获取数据库自动生成的主键
-->
````

### 五、重用 sql 标签

````xml
<sql id="userColumns">id,username,password</sql>
````

这个 SQL 片段可以被包含在其他语句中，eg：

````xml
<select id="selectProjectList" paramertType="int" resultType="hashmap">
　　SELECT 
    　　<include refid="userColumns"/>
　　FROM 
    　　t_project_002_project_info
</select>
````

### 六、完全限定名使用别名替代

在 mybatis 配置文件中，使用如下配置

````xml
<typeAliases>
    <typeAlias type="com.enh.bean.ProjectInfo" alias="projectInfo"/>
</typeAliases>
````

那么在 Mapper 中，可以直接使用 projectInfo，即表示 com.enh.bean.ProjectInfo

### 七、命名空间

每个sql映射文件的要元素中，都需要指定一个名称空间，用以确保每个映射语句的id属性不会重复。如

````xml
<mapper namespace="com.enh.mapper.PersonMapper">
````

在Java代码中引用某个 sql 映射时，使用的亦是含有名称空间的全路径。如

````java
session.update("com.enh.mapper.PersonMapper.udpateUser", user);
````

