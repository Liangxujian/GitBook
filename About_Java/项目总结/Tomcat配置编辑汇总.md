#### Tomcat配置编辑汇总

##### 一、Tomcat 修改窗口名称

1. 打开该 Tomcat 对应的 bin\catalina.bat 文件

2. 修改配置：

   ![1558516556471](D:\GitBook\About_Java\项目总结\assets\1558516556471.png)

##### 二、Tomcat 使用指定 JDK

1. 打开该 Tomcat 对应的 bin\catalina.bat 和 bin\setclasspath.bat 文件

2. 在文件开头的空白处加上：

   ````
   set JAVA_HOME=C:\Program Files\Java\jdk1.7.0_79
   set JRE_HOME=C:\Program Files\Java\jdk1.7.0_79\jre
   ````

   ![1009124401692](D:\GitBook\About_Java\项目总结\assets\1009124401692.png)

##### 三、Tomcat 修改字符集标准

这里参考 [文件名下载乱码.md](文件名下载乱码.md) 

##### 四、Tomcat 修改使用的 JVM 大小

1. 打开该 Tomcat 对应的 bin\catalina.bat 文件

2. 在文件开头的空白处加上：

   ````
   set JAVA_OPTS=-Xms2048m -Xmx2048m -Xss1024K -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=512m
   ````

   ![1009133827213](D:\GitBook\About_Java\项目总结\assets\1009133827213.png)

