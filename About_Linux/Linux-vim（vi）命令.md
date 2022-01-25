# Linux-vim（vi）命令

## vi/vim 区别

* vim 允许多级撤销，但在 vi 中按 u 只能撤销上次命令
* vim 可以在 Unix、Windows、Mac 等多操作平台上使用，但 vi 只能用于 Unix 中
* vim 可以用不同的颜色加亮编辑的代码
* **vim 对于 vi 完全兼容**

## vi/vim 的三种模式

- 命令行模式
- 插入模式
- 末行模式

刚由 vi/vim 进入的时候是命令行模式；输入 <kbd>a</kbd>、<kbd>i</kbd>、<kbd>o</kbd>、<kbd>O</kbd> 进入插入模式，由 <kbd>ESC</kbd> 键退出插入模式返回命令行模式；输入 <kbd>:</kbd>、<kbd>?</kbd>、<kbd>/</kbd> 从命令行模式进入末行模式

## 命令行模式

### 由行号定位到某一行

<kbd>:n</kbd>：定位到第 n 行

<kbd>ngg / nG</kbd>：定位到第 n 行

<kbd>vim filename + n</kbd>：打开 filename 文件的同时定位到第 n 行

### 由关键字定位到某一行

<kbd>vim +/pattern filename</kbd>：打开 filename 文件的同时定位到关键字 pattern 处

### 显示行号

<kbd>:set nu</kbd>：显示行号

### 光标的移动

<kbd>shift + g</kbd>：将光标直接移动到最后一行；
<kbd>shift + 4</kbd>：移动到当前行末尾；
<kbd>shift + 6</kbd>：移动到当前行头；

<kbd>shift + m</kbd>：当前屏幕上显示的内容移到中间；
<kbd>shift + h</kbd>：当前屏幕上显示的内容移到第一行；
<kbd>shift + l</kbd>：当前屏幕上显示的内容移到最后一行；

<kbd>    ctrl + f</kbd>：向下翻页；
<kbd>ctrl + b</kbd>：向上翻页

### 批处理命令

#### 复制操作

<kbd>yy</kbd>：复制当前整行的内容到 vi 缓存区
<kbd>yw</kbd>：复制当前光标所在位置到单词尾字符的内容到 vi 缓存区，相当于复制一个单词
<kbd>y$</kbd>：复制光标所在位置到行尾的内容到缓存区
<kbd>y^</kbd>：复制光标所在位置到行首的内容到缓存区
<kbd>#yy</kbd>：即 num + yy，例如：5yy 就是复制 5 行
<kbd>#yw</kbd>：即 num + yw，例如：2yw 就是复制两个单词

#### 粘贴操作

<kbd>p</kbd>

#### 删除操作

<kbd>x</kbd>：删除光标处的单个字符
<kbd>dd</kbd>：删除光标所在行
<kbd>dw</kbd>：删除当前字符到单词尾包括空格的所有字符
<kbd>#x</kbd>：例如：3x 就是删除光标处向右的三个字符
<kbd>#dd</kbd>：例如：3dd 就是从当前行开始向下删除三行文本

#### 撤销操作

<kbd>u</kbd>：取消最近一次的操作，可以使用多次来恢复原有的操作
<kbd>U</kbd>：取消所有操作
<kbd>ctrl + R</kbd>：恢复对使用 u 命令的操作

#### 其他操作

<kbd>sort</kbd>：对文档数据进行排序

<kbd>g/^(.\*)\n\1$/d</kbd>：对文档数据进行去重

<img src=".\assets\12195738775.png" alt="image-20200812195738775" style="zoom:50%;" />

## 插入模式

<kbd>i</kbd>：插入命令；<kbd>a</kbd>：附加命令；<kbd>o</kbd>：打开命令；
<kbd>c</kbd>：修改命令；<kbd>r</kbd>：取代命令；<kbd>s</kbd>：替换命令

````bash
vi/vim + 文件名称 # +“i”键进入编辑模式
````

### 光标移动

<kbd>home</kbd>：光标移动到行首；<kbd>end</kbd>：光标移动到行尾；
<kbd>page up</kbd>：上翻页；<kbd>page down</kbd>：下翻页；
<kbd>delete</kbd>：删除光标位置的字符

## 末行模式

<kbd>esc + w</kbd>：保存不退出；<kbd>esc + wq</kbd>：保存并退出；
<kbd>esc + q</kbd>：退出不保存；<kbd>esc + q!</kbd>：强制退出不保存

## 搜索和替换（命令模式）

```shell
/keyword    # 向光标下搜索keyword字符串，keyword可以是正则表达式
?keyword    # 向光标上搜索keyword字符串
n           # 向下搜索前一个搜素动作
N           # 向上搜索前一个搜索动作

*(#)        # 当光标停留在某个单词上时, 输入这条命令表示查找与该单词匹配的下(上)一个单词. 同样, 再输入 n 查找下一个匹配处, 输入 N 反方向查找.

g*(g#)      # 此命令与上条命令相似, 只不过它不完全匹配光标所在处的单词, 而是匹配包含该单词的所有字符串.

:s/old/new          # 用new替换行中首次出现的old
:s/old/new/g        # 用new替换行中所有的old
:n,m s/old/new/g    # 用new替换从n到m行里所有的old
:%s/old/new/g       # 用new替换当前文件里所有的old
```

