#### Mybatis 动态 sql 

##### 一、if 条件判断

语法示例：

````xml
SELECT * FROM user WHERE 1=1
<if test="username != null and username != ''">
	AND username LIKE CONCAT('%', #{username}, '%')
</if>
````

1. ###### 数字类型判断

   示例（过滤年龄小于20及大于40的条件）：

   ````xml
   SELECT * FROM user WHERE 1=1
   <if test="age gt 20 and age lt 40">
   	AND age = #{age}
   </if>
   ````

   xml 文件中对于`>`、`<`符号都要注意转译

   * `gt`  对应  `>`
   * `gte`  对应  `>=`
   * `lt`  对应  `<`
   * `lte`  对应  `<=`

2. ###### 字符串类型判断

   示例：

   ````xml
   SELECT * FROM user WHERE 1=1
   <if test="username != null and username != ''">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   <!-- 是否以"str"开头 -->
   <if test="username.indexOf('str') == 0">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   
   <!-- 是否包含"str"字符 -->
   <if test="username.indexOf('str') >= 0">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   
   <!-- 是否不包含"str"字符 -->
   <if test="username.indexOf('str') == -1">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   <!-- 或者 -->
   <if test="username.indexOf('str') eq -1">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   <!-- 或者 -->
   <if test="username.indexOf('str') neq 0">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   ````

   * `eq`  对应  `==`
   * `neq`  对应  `!=`

3. ###### list 是否为空

   示例：

   ````xml
   <if test="userList != null and userList.isEmpty()">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   <!-- 或者 -->
   <if test="userList != null and userList.size() == 0">
   	AND username LIKE CONCAT('%', #{username}, '%')
   </if>
   ````

##### 二、other-while 条件判断

示例：

````xml
SELECT * FROM user WHERE 1=1 
<if test="username != null and username != ''">
    <choose>
        <when test="username.length() == 1">
            AND username LIKE CONCAT('%', #{username}, '%')
        </when>
        <otherwise>
            AND username= #{username}
        </otherwise>
    </choose>
</if>
````

##### 三、foreach 集合循环生成动态SQL

