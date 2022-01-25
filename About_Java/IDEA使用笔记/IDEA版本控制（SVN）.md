# IDEA 版本控制（SVN）

> 本篇笔记参考自博客：[Idea 部署SVN详细步骤以及上传、检出、提交更新步骤](https://blog.csdn.net/zhuyi2576947717/article/details/82713910)

### 一、提交项目到 SVN

#### 1.1 让 SVN 接管项目版本管理

![1565677560583](D:\GitBook\About_Java\IDEA使用笔记\assets\1565677560583.png)

![1565677643222](D:\GitBook\About_Java\IDEA使用笔记\assets\1565677643222.png)

#### 1.2 查看 SVN 仓库

调出svn视图：

![1565677810770](D:\GitBook\About_Java\IDEA使用笔记\assets\1565677810770.png)

连接svn服务器：

![1565677847216](D:\GitBook\About_Java\IDEA使用笔记\assets\1565677847216.png)

连接后效果如下：

![1565677893553](D:\GitBook\About_Java\IDEA使用笔记\assets\1565677893553.png)

#### 1.3 添加要忽略上传的文件/文件夹

由于不同的用户使用的环境不同，因此有些文件不用上传到 svn 服务器，比如有的用户使用的是 idea，而用的用户使用的是 eclipse，由于使用的工具不同，因此上传的时候需要忽略其环境文件，只要上传 pom.xml 和 src 目录下的文件即可，那么无论使用的是哪个工具都可以通过这两个文件生成工程并进行操作。

![1565679108489](D:\GitBook\About_Java\IDEA使用笔记\assets\1565679108489.png)

![1565679266806](D:\GitBook\About_Java\IDEA使用笔记\assets\1565679266806.png)

这里按规则忽略写法：

1. .idea
2. target
3. *.iml

#### 1.4 上传工程到 svn

![1565680043603](D:\GitBook\About_Java\IDEA使用笔记\assets\1565680043603.png)

选择要上传的路径：

![1565679839514](D:\GitBook\About_Java\IDEA使用笔记\assets\1565679839514.png)

提交代码（绿色表示已经加入版本控制但是未提交）

![1565680561215](D:\GitBook\About_Java\IDEA使用笔记\assets\1565680561215.png)

### 二、取消 SVN 的版本控制

#### 2.1 安装插件

![1565681089460](D:\GitBook\About_Java\IDEA使用笔记\assets\1565681089460.png)

#### 2.2 插件使用

![1565681228336](D:\GitBook\About_Java\IDEA使用笔记\assets\1565681228336.png)

之后即可重新上传项目