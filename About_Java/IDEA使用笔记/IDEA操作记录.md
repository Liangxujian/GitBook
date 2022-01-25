# IDEA 操作记录

## 一、左侧结构树出现：0% classes，0% lines coverd

使用 idea 编码的时候，结构树在左侧出现：0% classes 的原因是由于启动的时候点击 coverage 启动

![20190625134908728](.\assets\20190625134908728.png)

![2019062513452165](D:\GitBook\About_Java\IDEA使用笔记\assets\2019062513452165.png)

解决办法：<kbd>ctrl</kbd>+<kbd>alt</kbd>+<kbd>F6</kbd>，去掉勾即可，如下图：

![20190625134958302](.\assets\20190625134958302.png)

## 二、IDEA 开启断言

run -> Edit Configurations...

![141330568](.\assets\141330568.png)

Configuration -> VM options, 在文本框中输入“-ea”， 点击OK即可

![141629640](.\assets\141629640.png)

拓展：Eclipse 中如何开启断言（Assert）

Run -> Run Configurations -> Arguments 页签 -> VM arguments 文本框中加上断言开启的标志：-enableassertions 或者 -ea 就可以了

在myEclipse中,Windows -> Preferences -> Java -> Installed JREs -> 点击正使用的JDK -> Edit -> Default VM Arguments文本框中输入：-ea

## 三、Git管理

### 3.1 更新项目

![02093744088](.\assets\02093744088.png)