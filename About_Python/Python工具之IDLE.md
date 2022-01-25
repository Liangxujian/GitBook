# Python 工具之 IDLE

> 本文摘录自：[Python IDLE使用方法详解](http://c.biancheng.net/view/4221.html)

在安装 Python 后，会自动安装一个 IDLE，它是一个 Python Shell (可以在打开的 IDLE 窗口的标题栏上看到），程序开发人员可以利用 Python Shell 与 Python 交互。

本节将以 Windows7 系统中的 IDLE 为例，详细介绍如何使用 IDLE 开发 Python 程序。

单击系统的开始菜单，然后依次选择“所有程序 -> Python 3.6 -> IDLE (Python 3.6 64-bit)”菜单项，即可打开 IDLE 窗口。

![IDLE主窗口](.\assets\1-1Z6261G445F5.gif)

在实际开发中，通常不能只包含一行代码，当需要编写多行代码时，可以单独创建一个文件保存这些代码，在全部编写完成后一起执行。具体方法如下：

1. 在 IDLE 主窗口的菜单栏上，选择“File -> New File”菜单项，将打开一个新窗口，在该窗口中，可以直接编写 Python 代码。
   在输入一行代码后再按下 <Enter> 键，将自动换到下一行，等待继续输入。

![新创建的 Python 文件窗口](.\assets\1-1Z6261G509506.gif)

2. 在代码编辑区中，编写多行代码。例如，输出由宋词《江城子》改编而成的《程序员之歌》，代码如下

```python
print(" "*5+"程序员之歌")
print(" "*15+"——《江城子》改编\n")
print("十年生死两茫茫，写程序，到天亮，")
print("千行代码，Bug何处藏。")
print("纵使上线又怎样，朝令改，夕断肠。")
print("领导每天新想法，天天改，日日忙。")
print("相顾无言，惟有泪千行")
print("每晚灯火阑珊处，程序员，加班狂。")
```

![编写代码后的 Python 文件窗口](.\assets\1-1Z6261G62aa.jpg)

3. 按下快捷键 <Ctrl+S> 保存文件，这里将文件名称设置为 demo.py。其中，.py 是 Python 文件的扩展名。在菜单栏中选择“Run -> Run Module”菜单项（也可以直接按下快捷键 <F5>），运行程序。

![运行程序](.\assets\1-1Z6261GA5C7.jpg)

4. 运行程序后，将打开 Python Shell 窗口显示运行结果。

![运行结果](.\assets\1-1Z6261GG3924.jpg)

## 常用快捷键

| 快捷键       | 说明                                                         | 适用范围                           |
| ------------ | ------------------------------------------------------------ | ---------------------------------- |
| F1           | 打开 Python 帮助文档                                         | Python文件窗口和Shell 均可用       |
| Alt+p        | 浏览历史命令（上一条）                                       | 仅 Python Shell 窗口可用           |
| Alt+n        | 浏览历史命令（下一条）                                       | 仅 Python Shell 窗口可用           |
| Alt+/        | 自动补全前面曾经出现过的单词，如果之前有多个单词具有相同前缀，<br>可以连续按下该快捷键，在多个单词中间循环选择 | Python 文件窗口和 Shell 窗口均可用 |
| Alt+3        | 注释代码块                                                   | 仅 Python Shell 窗口可用           |
| Alt+4        | 取消代码块注释                                               | 仅 Python Shell 窗口可用           |
| Alt+g        | 转到某一行                                                   | 仅 Python Shell 窗口可用           |
| Ctrl+z       | 撤销一步操作                                                 | Python 文件窗口和 Shell 窗口均可用 |
| Ctrl+Shift+z | 恢复上—次的撤销操作                                          | Python 文件窗口和 Shell 窗口均可用 |
| Ctrl+s       | 保存文件                                                     | Python 文件窗口和 Shell 窗口均可用 |
| Ctrl+]       | 缩进代码块                                                   | 仅 Python Shell 窗口可用           |
| Ctrl+[       | 取消代码块缩进                                               | 仅 Python Shell 窗口可用           |
| Ctrl+F6      | 重新启动 Python Shell                                        | 仅 Python Shell 窗口可用           |

