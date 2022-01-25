# Linux-tar 命令

> Linux tar 命令用于备份文件。
> tar 是用来建立，还原备份文件的工具程序，它可以加入，解开备份文件内的文件。

## 一、语法

````bash
tar [-ABcdgGhiklmMoOpPrRsStuUvwWxzZ][-b <区块数目>][-C <目的目录>][-f <备份文件>][-F <Script文件>][-K <文件>][-L <媒体容量>][-N <日期时间>][-T <范本文件>][-V <卷册名称>][-X <范本文件>][-<设备编号><存储密度>][--after-date=<日期时间>][--atime-preserve][--backuup=<备份方式>][--checkpoint][--concatenate][--confirmation][--delete][--exclude=<范本样式>][--force-local][--group=<群组名称>][--help][--ignore-failed-read][--new-volume-script=<Script文件>][--newer-mtime][--no-recursion][--null][--numeric-owner][--owner=<用户名称>][--posix][--erve][--preserve-order][--preserve-permissions][--record-size=<区块数目>][--recursive-unlink][--remove-files][--rsh-command=<执行指令>][--same-owner][--suffix=<备份字尾字符串>][--totals][--use-compress-program=<执行指令>][--version][--volno-file=<编号文件>][文件或目录...]
````

## 二、部分参数

<kbd>-c</kbd>：建立压缩档案

<kbd>-r</kbd>：向压缩归档文件末尾追加文件

<kbd>-t</kbd>：查看内容

<kbd>-u</kbd>：更新原压缩包中的文件

<kbd>-x</kbd>：解压

以上五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

<kbd>-z</kbd>：有 gzip 属性的

<kbd>-j</kbd>：有 bz2 属性的

<kbd>-Z</kbd>：有 compress 属性的

<kbd>-v</kbd>：显示所有过程

<kbd>-O</kbd>：将文件解开到标准输出

下面的参数 -f 是必须的：

<kbd>-f</kbd>：使用档案名字（切记，这个参数是最后一个参数，后面只能接档案名）

## 三、命令示例

````bash
tar -zxvf ***.tar # 解压文件

tar -cf all.tar *.jpg # 将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名
tar -rf all.tar *.gif # 将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思
tar -tf all.tar # 列出all.tar包中所有文件，-t是列出文件的意思
tar -uf all.tar logo.gif # 更新原来tar包all.tar中logo.gif文件。-u是表示更新文件的意思
tar -xf all.tar # 解出all.tar包中所有文件，-x是解开的意思
````
