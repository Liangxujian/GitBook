# SpringBoot注解记录

### 一、@JsonFormat  与  @DataTimeFormat

>注解 @JsonFormat 主要是后台到前台的时间格式的转换
>注解 @DataFormAT 主要是前后到后台的时间格式的转换

#### 1、@JsonFormat

##### 1.1 maven 导入相关 jar 包

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

##### 1.2 使用案例

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

##### 1.3 注解解析

`@JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")`

* pattern：表示需要转换的时间日期格式
* timezone：表示时间设置为东八区，避免时间在转换中有误差

PS：@JsonFormat注解可以在属性的上方，同样可以在属性对应的get方法上，两种方式没有区别

#### 2、@DataTimeFormat

##### 2.1 maven 导入相关 jar 包

````xml
<!-- joda-time -->
<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.3</version>
</dependency>
````

##### 2.2 使用案例

````java
@DateTimeFormat(pattern = "yyyy-MM-dd")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
private Date symstarttime;
 
@DateTimeFormat(pattern = "yyyy-MM-dd")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss",timezone="GMT+8")
private Date symendtime;
````

