# GitBook 使用说明

## 一、新建书籍

* 创建一个目录 —— 书的项目文件夹
* 执行命令：`gitbook init`。会创建2个文件夹，一个是目录(SUMMARY.md)，一个是章节内容(README.md) 默认这是书的首页。
* 启动一个serve：`gitbook serve` (注：这里是serve不是server)
* 本地查看 `localhost:4000`
* 自己设置一下自己喜欢的样式 mstyle.css

## 二、常用命令

````bash
//查看gitbook的版本
gitbook -V

//初始化一个项目
gitbook init

//列出所有的gitbook的命令
gitbook help

//显示gitbook-cli的帮助信息
gitbook --help

// 生成静态页面html
gitbook build

//生成静态网页并且启动本地的一个server
gitbook serve

//列出本地所有gitbook的版本
//列出远端可用的gitbook的最新版本
gitbook ls
gitbook ls-remote

//更新到gitbook最新的版本
gitbook update

//卸载对应的gitbook版本
gitbook uninstall 2.0.1

//指定log的级别
gitbook build --log=debug

//输出错误信息
gitbook build --debug

//gitbook安装插件
gitbook install ./
````

