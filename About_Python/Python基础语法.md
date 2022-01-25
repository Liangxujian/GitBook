# Python 基础语法

> Google Python 命名规范
>
> module_name，模块
>
> package_name，包
>
> ClassName，类
>
> method_name，方法
>
> ExceptionName，异常
>
> function_name，函数
>
> GLOBAL_VAR_NAME，常量
>
> instance_var_name，实例
>
> function_parameter_name，参数
>
> local_var_name，本变量

## 一、Python 编码规范

> 本小结摘录自：[Python编码规范（PEP 8）](http://c.biancheng.net/view/4184.html)

```python
# 1、每个 import 语句只导入一个模块，尽量避免一次导入多个模块
#推荐
import os
import sys
#不推荐
import os,sys

# 2、不要在行尾添加分号，也不要用分号将两条命令放在同一行
#不推荐
height=float(input("输入身高：")) ; weight=fioat(input("输入体重：")) ;

# 3、建议每行不超过 80 个字符，如果超过，建议使用小括号将多行内容隐式的连接起来，而不推荐使用反斜杠 \ 进行连接
# 例如，如果一个字符串文本无法实现一行完全显示，则可以使用小括号将其分开显示
#推荐
s=("C语言中文网是中国领先的C语言程序设计专业网站，"
"提供C语言入门经典教程、C语言编译器、C语言函数手册等。")
#不推荐
s="C语言中文网是中国领先的C语言程序设计专业网站，\
提供C语言入门经典教程、C语言编译器、C语言函数手册等。"
# 注：此编程规范适用于绝对大多数情况，但以下 2 种情况除外：
# a.导入模块的语句过长
# b.注释里的 URL

# 4、使用必要的空行可以增加代码的可读性，通常在顶级定义（如函数或类的定义）之间空两行，而方法定义之间空一行，另外在用于分隔某些功能的位置也可以空一行
# 比如说，在下图右侧这段代码中，if 判断语句同之前的代码多实现的功能不同，因此这里可以使用空行进行分隔

# 5、通常情况下，在运算符两侧、函数参数之间以及逗号两侧，都建议使用空格进行分隔


```

![两段功能相同的 Python 代码](.\assets\1-1Z62414494a07.jpg)

## 二、Python 标识符命名规范

```python
# 1、标识符是由字符（A~Z 和 a~z）、下划线和数字组成，但第一个字符不能是数字

# 2、标识符不能和 Python 中的保留字相同

# 3、Python中的标识符中，不能包含空格、@、% 以及 $ 等特殊字符
# 合法
UserID
name
mode12
user_age
# 非法
4word    # 不能以数字开头
try      # try是保留字，不能作为标识符
$money   # 不能包含特殊字符

# 4、在 Python 中，标识符中的字母严格区分大小写

# 5、Python 语言中，以下划线开头的标识符有特殊含义
# a.以单下划线开头的标识符（如 _width），表示不能直接访问的类属性，其无法通过 from...import* 的方式导入
# b.以双下划线开头的标识符（如__add），表示类的私有成员
# c.以双下划线作为开头和结尾的标识符，表示 Python 里特殊方法专用的标识，如 __init__ 代表类的构造函数
# PS：除非特定场景需要，应避免使用以 下划线 开头的标识符
# PS：Python 允许使用汉字作为标识符，但应尽量避免
```

### 2.1 标识符正确名称规范

- 当标识符用作模块名时，应尽量短小，并且全部使用小写字母，可以使用下划线分割多个字母，例如 game_mian、game_register 等。
- 当标识符用作包的名称时，应尽量短小，也全部使用小写字母，不推荐使用下划线，例如 com.mr、com.mr.book 等。
- 当标识符用作类名时，应采用单词首字母大写的形式。例如，定义一个图书类，可以命名为 Book。
- 模块内部的类名，可以采用 "下划线+首字母大写" 的形式，如 _Book。
- 函数名、类中的属性名和方法名，应全部使用小写字母，多个单词之间可以用下划线分割。
- 常量命名应全部使用大写字母，单词之间可以用下划线分割。

## 三、Python 关键字（保留字）

```python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

<table>
	<tr>
		<th colspan="10" style="text-align: center">Python关键字一览表</th>
	</tr>
	<tr>
		<td>and</td>
		<td>as</td>
		<td>assert</td>
		<td>async</td>
		<td>await</td>
		<td>break</td>
		<td>class</td>
		<td>continue</td>
		<td>def</td>
		<td>del</td>
	</tr>
	<tr>
		<td>elif</td>
		<td>else</td>
		<td>except</td>
		<td>FALSE</td>
		<td>finally</td>
		<td>for</td>
		<td>from</td>
		<td>global</td>
		<td>if</td>
		<td>import</td>
	</tr>
	<tr>
		<td>in</td>
		<td>is</td>
		<td>lambda</td>
		<td>None</td>
		<td>nonlocal</td>
		<td>not</td>
		<td>or</td>
		<td>pass</td>
		<td>raise</td>
		<td>return</td>
	</tr>
	<tr>
		<td>try</td>
		<td>while</td>
		<td>with</td>
		<td>yield</td>
		<td>TRUE</td>
	</tr>
</table>

PS：由于 Python 是严格区分大小写的，保留字也不例外。所以，我们可以说 if 是保留字，但 IF 就不是保留字

在实际开发中，如果使用 Python 中的保留字作为标识符，则解释器会提示“invalid syntax” 的错误信息

![保留字作标识符报错信息示意图](.\assets\1-1Z6241FQ3419.jpg)

## 四、Python 行与缩进

Python 最具特色的就是用缩进来写模块。缩进的空白数量是可变的，但是所有代码块语句必须包含相同的缩进空白数量，这个必须严格执行。

如果缩进方式不一致会报错：IndentationError: unindent does not match any outer indentation level。

## 五、Python 多行语句

Python 语句中一般以新行作为语句的结束符。

但是我们可以使用斜杠（ \）将一行的语句分为多行显示，如下所示：

```python
total = item_one + \
        item_two + \
        item_three
```

语句中包含 [], {} 或 () 括号就不需要使用多行连接符。如下实例：

```python
days = ['Monday', 'Tuesday', 'Wednesday',
        'Thursday', 'Friday']
```

## 六、Python 引号

Python 可以使用引号( **'** )、双引号( **"** )、三引号( **'''** 或 **"""** ) 来表示字符串，引号的开始与结束必须是相同类型的。

其中三引号可以由多行组成，编写多行文本的快捷语法，常用于文档字符串，在文件的特定地点，被当做注释。

```python
word = 'word'
sentence = "这是一个句子。"
paragraph = """这是一个段落。
包含了多个语句"""

'''
这是多行注释，使用单引号。
这是多行注释，使用单引号。
这是多行注释，使用单引号。
'''

"""
这是多行注释，使用双引号。
这是多行注释，使用双引号。
这是多行注释，使用双引号。
"""
```

## 七、Python 函数

Python 函数分为：内置函数和标准库函数

Python 解释器也是一个程序，它给用户提供了一些常用功能，并给它们起了独一无二的名字，这些常用功能就是内置函数。Python 解释器启动以后，内置函数也生效了，可以直接拿来使用。

Python 标准库相当于解释器的外部扩展，它并不会随着解释器的启动而启动，要想使用这些外部扩展，必须提前导入。Python 标准库非常庞大，包含了很多模块，要想使用某个函数，必须提前导入对应的模块，否则函数是无效的。

内置函数是解释器的一部分，它随着解释器的启动而生效；标准库函数是解释器的外部扩展，导入模块以后才能生效。一般来说，内置函数的执行效率要高于标准库函数。

Python 解释器一旦启动，所有的内置函数都生效了；而导入标准库的某个模块，只是该模块下的函数生效，并不是所有的标准库函数都生效。

内置函数的数量必须被严格控制，否则 Python 解释器会变得庞大和臃肿。一般来说，只有那些使用频繁或者和语言本身绑定比较紧密的函数，才会被提升为内置函数。

例如，在屏幕上输出文本就是使用最频繁的功能之一，所以 print() 是 Python 的内置函数。

> 在 Python 2.x 中，print 是一个关键字；到了 Python 3.x 中，print 变成了内置函数。

<table>
	<tr>
		<th colspan="10" style="text-align: center">Python 3.x内置函数一览表</th>
	</tr>
	<tr>
		<td>abs()</td>
		<td>delattr()</td>
		<td>hash()</td>
		<td>memoryview()</td>
		<td>set()</td>
		<td>all()</td>
		<td>dict()</td>
		<td>help()</td>
		<td>min()</td>
		<td>setattr()</td>
	</tr>
	<tr>
		<td>any()</td>
		<td>dir()</td>
		<td>hex()</td>
		<td>next()</td>
		<td>slicea()</td>
		<td>ascii()</td>
		<td>divmod()</td>
		<td>id()</td>
		<td>object()</td>
		<td>sorted()</td>
	</tr>
	<tr>
		<td>bin()</td>
		<td>enumerate()</td>
		<td>input()</td>
		<td>oct()</td>
		<td>staticmethod()</td>
		<td>bool()</td>
		<td>eval()</td>
		<td>int()</td>
		<td>open()</td>
		<td>str()</td>
	</tr>
	<tr>
		<td>breakpoint()</td>
		<td>exec()</td>
		<td>isinstance()</td>
		<td>ord()</td>
		<td>sum()</td>
		<td>bytearray()</td>
		<td>filter()</td>
		<td>issubclass()</td>
		<td>pow()</td>
		<td>super()</td>
	</tr>
	<tr>
		<td>bytes()</td>
		<td>float()</td>
		<td>iter()</td>
		<td>print()</td>
		<td>tuple()</td>
		<td>callable()</td>
		<td>format()</td>
		<td>len()</td>
		<td>property()</td>
		<td>type()</td>
	</tr>
	<tr>
		<td>chr()</td>
		<td>frozenset()</td>
		<td>list()</td>
		<td>range()</td>
		<td>vars()</td>
		<td>classmethod()</td>
		<td>getattr()</td>
		<td>locals()</td>
		<td>repr()</td>
		<td>zip()</td>
	</tr>
	<tr>
		<td>compile()</td>
		<td>globals()</td>
		<td>map()</td>
		<td>reversed()</td>
		<td>__import__()</td>
		<td>complex()</td>
		<td>hasattr()</td>
		<td>max()</td>
		<td>round()</td>
	</tr>
</table>

注：不要使用内置函数的名字作为标识符使用（例如变量名、函数名、类名、模板名、对象名等），虽然这样做 Python 解释器不会报错，但这会导致同名的内置函数被覆盖，从而无法使用。

```python
>>> print = "http://c.biancheng.net/python/"  #将print作为变量名
>>> print("Hello World!")  #print函数被覆盖，失效
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    print("Hello World!")
TypeError: 'str' object is not callable
```

