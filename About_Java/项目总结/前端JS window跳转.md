# 前端JS window跳转

### 一、问题概述

子窗口的一些操作需要一并修改父窗口的一些信息显示，但是调用总是报错："XXX is not a function"

### 二、处理方案

由于使用的是 Ext.js 框架，而 Ext.js 代码都是包含在 Ext.onReady 之中，该方法即表示：在页面完毕（onload 方法被调用之前以及图片被装载之前）调用初始化函数，导致因为作用域的问题，一直都找不到变量。后来把变量放到最外层即可解决。

#### 2.1 子窗口：

````javascript
success:function(form,action){
	world();
}
var world = function(){
	window.top.Test();
}
````

#### 2.2 父窗口：

````javascript
function Test(){
	// 方法体
}
````

### 三、顺带一些JS原生 window 跳转汇总

在应用有 frameset 或者 iframe 的页面时：
parent 是父窗口；
top 是最顶级父窗口（有的窗口中套了好几层 frameset 或者 iframe）；
self 是当前窗口；
opener 是用 open 方法打开当前窗口的哪个窗口


（可以将框架视为窗口中的不同区域，框架是浏览器窗口中特定的部分。一个浏览器窗口可以根据你的需要分成任意多的框架，一个单个的框架也可以分成其它多个框架，即所谓的嵌套框架）