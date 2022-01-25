# Python 进阶语法

## 一、Python 变量类型

Python 中的变量赋值不需要类型声明。

每个变量在内存中创建，都包括变量的标识，名称和数据这些信息。

每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。

````python
# 100，1000.0 和 "John" 三个对象分别赋值给 counter，miles，name 变量
counter = 100  # 赋值整型变量
miles = 1000.0 # 浮点型
name = "John"  # 字符串
 
print counter
print miles
print name

# 创建一个整型对象，值为1，a、b、c三个变量被分配到相同的内存空间上
a = b = c = 1

# 两个整型对象 1 和 2 分别分配给变量 a 和 b，字符串对象 "john" 分配给变量 c
a, b, c = 1, 2, "john"

# 使用 del 语句可以删除一些对象的引用
var = 1
print(var)
del var
print(var)
# 前一句打印正常，后一句打印报错：name 'var' is not defined
````

## 二、Python 标准数据类型

Python 有五个标准的数据类型：

* Numbers（数字）
* String（字符串）
* List（列表）
* Tuple（元组）
* Dictionary（字典）

### 2.1 Python 数字

Python 支持四种不同的数字类型：

* int（有符号整型）
* long（长整型[也可以代表八进制和十六进制]）
* float（浮点型）
* complex（复数）

一些数值类型的实例：

| int    | long                  | float      | complex    |
| ------ | --------------------- | ---------- | ---------- |
| 10     | 51924361L             | 0.0        | 3.14j      |
| 100    | -0x19323L             | 15.20      | 45.j       |
| -786   | 0122L                 | -21.9      | 9.322e-36j |
| 080    | 0xDEFABCECBDAECBFBAEl | 32.3e+18   | .876j      |
| -0490  | 535633629843L         | -90.       | -.6545+0J  |
| -0x260 | -052318172735L        | -32.54e100 | 3e+26J     |
| 0x69   | -4721885298529L       | 70.2E-12   | 4.53e-7j   |

* 长整型也可以使用小写 l，但是还是建议使用大写 L，避免与数字 1 混淆。Python 使用 L 来显示长整型
* Python 还支持复数，复数由实数部分和虚数部分构成，可以用 a + bj，或者 complex(a,b) 表示， 复数的实部 a 和虚部 b 都是浮点型

**注：long 类型只存在于 Python2.X 版本中，在 2.2 以后的版本中，int 类型数据溢出后会自动转为long类型。在 Python3.X 版本中 long 类型被移除，使用 int 替代。**

## 三、Python 字符串

Python 的字串列表有 2 种取值顺序：

* 从左到右索引默认 0 开始的，最大范围是字符串长度少 1
* 从右到左索引默认 -1 开始的，最大范围是字符串开头

![img](.\assets\python-string-slice.png)

```python
# 截取字符串示例：
>>> str = 'zip.zip'
>>> str[:-4]            # 如果头下标为空，默认为0；如果尾下标为空，默认字符串长度-1
zip
# 这样的分割字符串，Python 返回的是一个新的字符串对象

>>> print str           # 输出完整字符串
>>> print str[0]        # 输出字符串中的第一个字符
>>> print str[2:5]      # 输出字符串中第三个至第六个之间的字符串
>>> print str[2:]       # 输出从第三个字符开始的字符串

>>> print str * 2       # 输出字符串两次
>>> print str + "TEST"  # 输出连接的字符串

>>> print str[1:4:2]    # 最后一个参数表示步长为2，输出：i.
```

## 四、Python 列表

Python 列表可以完成大多数集合类的数据结构实现。它支持字符，数字，字符串甚至可以包含列表（即嵌套）。

Python 列表用 **[ ]** 标识，是 Python 最通用的复合数据类型。

<img src=".\assets\list_slicing1_new1.png" alt="img" style="zoom:50%;" />

```python
list = [ 'runoob', 786 , 2.23, 'john', 70.2 ]
tinylist = [123, 'john']

print list               # 输出完整列表
print list[0]            # 输出列表的第一个元素
print list[1:3]          # 输出第二个至第三个元素 
print list[2:]           # 输出从第三个开始至列表末尾的所有元素
print tinylist * 2       # 输出列表两次
print list + tinylist    # 打印组合的列表
```

## 五、Python 元祖

Python 元组是另一个数据类型，类似于 List（列表）。

Python 元组用 **()** 标识。内部元素用逗号隔开。但是元组不能二次赋值，相当于只读列表。

```python
tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
tinytuple = (123, 'john')

print tuple               # 输出完整元组
print tuple[0]            # 输出元组的第一个元素
print tuple[1:3]          # 输出第二个至第四个（不包含）的元素 
print tuple[2:]           # 输出从第三个开始至列表末尾的所有元素
print tinytuple * 2       # 输出元组两次
print tuple + tinytuple   # 打印组合的元组

# 以下是元组无效的，因为元组是不允许更新，而列表允许
tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
list = [ 'runoob', 786 , 2.23, 'john', 70.2 ]
tuple[2] = 1000    # 元组中是非法应用
list[2] = 1000     # 列表中是合法应用
```

## 六、Python 字典

字典（dictionary）是除列表以外 Python 之中最灵活的内置数据结构类型。列表是有序的对象集合，字典是无序的对象集合。

两者之间的区别在于：字典当中的元素是通过键来存取的，而不是通过偏移存取。

字典用"{ }"标识。字典由索引（key）和它对应的值 value 组成。

```python
dict = {}
dict['one'] = "This is one"
dict[2] = "This is two"

tinydict = {'name': 'runoob','code':6734, 'dept': 'sales'}

print dict['one']          # 输出键为'one' 的值
print dict[2]              # 输出键为 2 的值
print tinydict             # 输出完整的字典
print tinydict.keys()      # 输出所有键
print tinydict.values()    # 输出所有值
```

## 七、Python 数据类型转换

```python
# int(x, base=10) - 将 x 转换为一个整数
# - x    -- 字符串或数字
# - base -- 进制数，默认十进制
>>> int()                # 不传入参数时，得到结果0
0
>>> int(3)
3
>>> int(3.6)
3
>>> int('12',16)        # 如果是带参数base的话，12要以字符串的形式进行输入，12 为 16进制
18
>>> int('0xa',16)  
10  
>>> int('10',8)  
8

# long(x, base=10) - 将 x 转换为一个长整数
# 注：Python3.x 版本已删除 long() 函数。
>>> long()
0L
>>> long(1)
1L
>>> long('123')
123L

# float(x)/float([x]) - 将一个整数或字符串 x 转换到一个浮点数
>>> float(1)
1.0
>>> float(112)
112.0
>>> float(-123.6)
-123.6
>>> float('123')     # 字符串
123.0

# complex(real [,imag]) - 创建一个复数
# - real -- int, long, float或字符串
# - imag -- int, long, float
>>> complex(1, 2)
(1 + 2j)
>>> complex(1)    # 数字
(1 + 0j)
>>> complex("1")  # 当做字符串处理
(1 + 0j)
# 注意：这个地方在"+"号两边不能有空格，也就是不能写成"1 + 2j"，应该是"1+2j"，否则会报错
>>> complex("1+2j")
(1 + 2j)

# str(x) - 将对象 x 转换为字符串
>>> s = 'RUNOOB'
>>> str(s)
'RUNOOB'
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'};
>>> str(dict)
"{'google': 'google.com', 'runoob': 'runoob.com'}"

# repr(object) - 将对象 object 转换为表达式字符串
>>> s = 'RUNOOB'
>>> repr(s)
"'RUNOOB'"
>>> dict = {'runoob': 'runoob.com', 'google': 'google.com'};
>>> repr(dict)
"{'google': 'google.com', 'runoob': 'runoob.com'}"

# eval(expression[, globals[, locals]]) - 用来计算在字符串中的有效 Python 表达式，并返回一个对象
# - expression -- 表达式
# - globals    -- 变量作用域，全局命名空间，如果被提供，则必须是一个字典对象
# - locals     -- 变量作用域，局部命名空间，如果被提供，可以是任何映射对象
>>> x = 7
>>> eval( '3 * x' )
21
>>> eval('pow(2,2)')
4
>>> eval('2 + 2')
4
>>> n=81
>>> eval("n + 4")
85

# tuple( iterable ) - Python 元组 tuple() 函数将列表转换为元组
>>> tuple([1,2,3,4])
(1, 2, 3, 4)
>>> tuple({1:2,3:4})    # 针对字典 会返回字典的key组成的tuple
(1, 3)
>>> tuple((1,2,3,4))    # 元组会返回元组自身
(1, 2, 3, 4)

aList = [123, 'xyz', 'zara', 'abc']
aTuple = tuple(aList)
print "Tuple elements : ", aTuple

# list( tup ) - 将序列 tup 转换为一个列表
aTuple = (123, 'runoob', 'google', 'abc')
print(list(aTuple))

[123, 'runoob', 'google', 'abc']

# set([iterable]) - 转换为可变集合（创建一个无序不重复元素集），可进行关系测试，删除重复数据，还可以计算交集、差集、并集等
>>> x = set('runoob')
>>> y = set('google')
>>> x, y
(set(['b', 'r', 'u', 'o', 'n']), set(['e', 'o', 'g', 'l']))   # 重复的被删除
>>> x & y         # 交集
set(['o'])
>>> x | y         # 并集
set(['b', 'e', 'g', 'l', 'o', 'n', 'r', 'u'])
>>> x - y         # 差集
set(['r', 'b', 'u', 'n'])

# dict(d) - 创建一个字典。参数 d 必须是一个序列 (key,value)元组
# dict(**kwarg)           - **kwargs -- 关键字
# dict(mapping, **kwarg)  - mapping  -- 元素的容器
# dict(iterable, **kwarg) - iterable -- 可迭代对象
>>> dict()                        # 创建空字典
{}
>>> dict(a='a', b='b', t='t')     # 传入关键字
{'a': 'a', 'b': 'b', 't': 't'}
>>> dict(zip(['one', 'two', 'three'], [1, 2, 3]))   # 映射函数方式来构造字典
{'three': 3, 'two': 2, 'one': 1} 
>>> dict([('one', 1), ('two', 2), ('three', 3)])    # 可迭代对象方式来构造字典
{'three': 3, 'two': 2, 'one': 1}

# frozenset([iterable]) - 返回一个冻结的集合，冻结后集合不能再添加或删除任何元素
# - iterable -- 可迭代的对象，比如列表、字典、元组等等
>>> a = frozenset(range(10))     # 生成一个新的不可变集合
>>> a
frozenset([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> b = frozenset('runoob') 
>>> b
frozenset(['b', 'r', 'u', 'o', 'n'])   # 创建不可变集合

# chr(i) - 将一个整数转换为一个字符（用一个范围在 range（256）内的（就是0～255）整数作参数，返回一个对应的字符）
# - i -- 可以是10进制也可以是16进制的形式的数字
>>> print chr(0x30), chr(0x31), chr(0x61)   # 十六进制
0 1 a
>>> print chr(48), chr(49), chr(97)         # 十进制
0 1 a

# unichr(i) - 将一个整数转换为Unicode字符（unichr() 函数 和 chr() 函数功能基本一样， 只不过是返回 unicode 的字符）
# 注： Python3 不支持 unichr()，改用 chr() 函数
>>> unichr(97)
u'a'
>>> unichr(98)
u'b'
>>> unichr(99)
u'c'

# ord(c) - 将一个字符转换为它的整数值
# ord() 函数是 chr() 函数（对于8位的ASCII字符串）或 unichr() 函数（对于Unicode对象）的配对函数，它以一个字符（长度为1的字符串）作为参数，返回对应的 ASCII 数值，或者 Unicode 数值，如果所给的 Unicode 字符超出了你的 Python 定义范围，则会引发一个 TypeError 的异常
>>> ord('a')
97
>>> ord('b')
98
>>> ord('c')
99

# hex(x) - 将一个整数转换为一个十六进制字符串
>>> hex(255)
'0xff'
>>> hex(-42)
'-0x2a'
>>> hex(1L)
'0x1L'
>>> hex(12)
'0xc'
>>> type(hex(12))
<class 'str'>      # 字符串

# oct(x) - 将一个整数转换为一个八进制字符串
# Python2.0+ 案例
>>> oct(10)
'012'
>>> oct(20)
'024'
>>> oct(15)
'017'
# Python3.0+ 案例
>>> oct(10)
'0o12'
>>> oct(20)
'0o24'
>>> oct(15)
'0o17'
```

