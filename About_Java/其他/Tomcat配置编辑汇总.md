# .Tomcat配置编辑汇总

## 一、Tomcat 修改窗口名称

1. 打开该 Tomcat 对应的 bin\catalina.bat 文件

2. 修改配置：

   ![1558516556471](.\assets\1558516556471.png)

## 二、Tomcat 使用指定 JDK

1. 打开该 Tomcat 对应的 bin\catalina.bat 和 bin\setclasspath.bat 文件

2. 在文件开头的空白处加上：

   ````
   set JAVA_HOME=C:\Program Files\Java\jdk1.7.0_79
   set JRE_HOME=C:\Program Files\Java\jdk1.7.0_79\jre
   ````

   ![1009124401692](.\assets\1009124401692.png)

## 三、Tomcat 修改字符集标准

这里参考 [文件名下载乱码.md](../项目总结/文件名下载乱码.md) 

## 四、Tomcat 修改使用的 JVM 大小

1. 打开该 Tomcat 对应的 bin\catalina.bat 文件

2. 在文件开头的空白处加上：

   ````
   set JAVA_OPTS=-Xms2048m -Xmx2048m -Xss1024K -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=512m
   ````

   ![1009133827213](.\assets\1009133827213.png)

## 五、Tomcat控制台输出乱码问题

![113033222](.\assets\113033222.png)

问题原因：编码不一致，Tomcat 启动后默认编码 UTF-8，而 windows 的默认编码是 GBK。所以只需配置启动 Tomcat 后为 GBK 编码即可。

处理方法：找到该 Tomcat conf 目录下的 logging.properties 文件，添加（或修改）语句为：java.util.logging.ConsoleHandler.encoding = GBK，然后重启 Tomcat 使配置生效即可。（注意：这里修改为什么编码，取决于 Tomcat 所在的系统）

![135056253](.\assets\135056253.png)