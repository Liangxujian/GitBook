# WebService

### 一、关于WebService

WebService 是基于 HTTP 协议，和使用 xml 来描述的一种程序。用于解决系统之间相互调用的问题。

### 二、WebService 使用步骤：

#### 2.1 提供服务器端：

1. 编写服务类，需要通过使用 @WebService 把类注解声明为 WebService；

2. 发布服务类，通过 EndPoint 来发布（不支持发布静态方法和 final 方法）；

   ![1558512627517](D:\GitBook\About_Java\其他\assets\1558512627517.png)

3. 使用浏览器访问描述文件：address?wsdl

#### 2.2 调用服务 —— 生成客户端（wsimport）：

1. 先新建客户端项目

   ![1558512847712](D:\GitBook\About_Java\其他\assets\1558512847712.png)

2. 然后生成客户端代码（通过 wsdl 生成） —— wsimport（jdk 自带指令）

   ````
   wsimport -s D:\JavaProject\EclipseNeonProject\WebServiceClient\src -p com.mmc.ws.client http://127.0.0.1:3306/student?wsdl
   ````

   ![1558512929379](D:\GitBook\About_Java\其他\assets\1558512929379.png)

3. 编写测试类，测试调用代码

   ![1558512960417](D:\GitBook\About_Java\其他\assets\1558512960417.png)

