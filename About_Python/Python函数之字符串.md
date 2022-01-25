# Python 函数之字符串

## 一、Python 字符串转义字符

| 转义字符 | 描述 |
| -------- | ---- |
| \n       | 换行 |

## 二、Python 字符串运算符

| 操作符 | 描述                                     | 示例                                   |
| :----: | ---------------------------------------- | -------------------------------------- |
|  r/R   | 所有的字符串都是直接按照字面的意思来使用 | print( r'\n' ) / print( R'\n' ) >>> \n |
|   %    | 格式化字符                               |                                        |

## 三、Python 字符串格式化

```python
>>> print ("我叫 %s 今年 %d 岁!" % ('小明', 10))
我叫 小明 今年 10 岁!

>>> print ("我叫 %s 今年 %d 岁!" % ('小明', '一百'))
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    print ("我叫 %s 今年 %d 岁!" % ('小明', '一百'))
TypeError: %d format: a number is required, not str
```

Python 字符串格式化符号

| 符 号 | 描述                                 |
| :---: | :----------------------------------- |
|  %c   | 格式化字符及其ASCII码                |
|  %s   | 格式化字符串                         |
|  %d   | 格式化整数                           |
|  %u   | 格式化无符号整型                     |
|  %o   | 格式化无符号八进制数                 |
|  %x   | 格式化无符号十六进制数               |
|  %X   | 格式化无符号十六进制数（大写）       |
|  %f   | 格式化浮点数字，可指定小数点后的精度 |
|  %e   | 用科学计数法格式化浮点数             |
|  %E   | 作用同%e，用科学计数法格式化浮点数   |
|  %g   | %f和%e的简写                         |
|  %G   | %f 和 %E 的简写                      |
|  %p   | 用十六进制数格式化变量的地址         |

## 四、Python 字符串函数

```python
# 字符串声明
str = "Hello World"
inx = 123
str = str + str(inx)

# find()：检测字符串是否包含，返回该字符串位置，如果不包含返回-1
str.find("Hello") # 返回值：0
str.find("W")     # 返回值：6, 这里需要注意下：空格也是一个字符。W前面有个空格，所以W位置是6
str.find("R")     # 返回值：-1，并不包含在Hello World中，如果不包含返回-1

# index()：检测字符串是否包含指定的字符，并返回开始的索引值，如果不包含会报错，建议慎用
str.index("Hello") # 返回值：0
str.index("o")     # 返回值：4
str.index("W")     # 返回值：6
str.index("R")     # 返回值：报错信息，因为R并不包含其中

# len()：返回字符串长度，以0开始计算
len(str) # 返回值：10

# count()：收集指定字符在字符串中出现的次数
str.count("o") 返回值：2, o字符在Hello World中存在两个。
# 也可以指定count()函数从某个位置开始查找。语法为：count(" ",start,end)
str.count('o',5,10)       # 返回值：1，原因：指定位置后会从索引5开始检索，以索引10结束。5到10之间只存在一个'o'
str.count('o',4,len(str)) # 返回值：2，索引从4开始，到字符串结束。len(str)字符串长度

# replace()：替换字符串
str.replace('hello','HELLO')  # 把小写的hello替换为大写的HELLO
str.replace('W','B')          # 把W替换为B

# split()：字符串切割，返回一个列表
str.split('o') # 返回值：["hell","w","rld"]

# upper()：将所有的字符转换为大写
str.upper() # 返回值：HELLO WORLD

# title()：首字母转换为大写
str.title() # 返回值：Hello World

# center()：返回一个原字符串居中，并以空格填充至长度宽度的新字符串
str.center(80) # 返回值: ( Hello World ) （其字符串两头被空格填充）

# join()：在字符串后面插入一个指定字符，构造一个新的字符串
_str = "_"
list = ["I","Love","You"]
_str.join(list) # 返回值：I_Love_You 每个列表元素后面都插入一个下划线

# isspace()：检测字符串中是否只包含空格，如果有返回Trun反之返回False，注意只校验空格
str_one = " "
str_two = "早上好！"
str_three = ""
str_one.isspace()   # 返回trun
str_two.isspace()   # 返回false
str_three.isspace() # 返回false

# isalnum()：检测是否只包含数字或字母。用处：可以用于判断密码，一般情况下密码不能输入汉字或空格
str_one = "a123"
str_two = "a 456"
str_one.isalnum() # 返回trun
str_two.isalnum() # 返回false，因为包含空格

# isdigit()：检测字符是否只包含数字，返回Trun和False
str_one = '123'
str_two = 'a123'
str_one.isdigit() # 返回trun 
str_two.isdigit() # 返回false

# isalpha()：检测字符串是否只包含字母
str_one = "abcd"
str_two="123abacd"
str_one.isalpha() # 返回trun
str_two.isalpha() # 返回false
```

## 五、Python 字符串转换

```python
# Python字符串转json
>>> import json
>>> str = '{"name" : "john", "gender" : "male", "age": 28}'
>>> json = json.loads(str)
# 使用json.loads()方法要注意，数组或对象之中的字符串必须使用双引号，不能使用单引号
>>> str = "{'name' : 'john', 'gender' : 'male', 'age': 28}"
>>> json = json.loads(str)
Traceback (most recent call last):
  File "<pyshell#8>", line 1, in <module>
    json = json.loads(str)
  File "C:\Users\13692\AppData\Local\Programs\Python\Python38\lib\json\__init__.py", line 357, in loads
    return _default_decoder.decode(s)
  File "C:\Users\13692\AppData\Local\Programs\Python\Python38\lib\json\decoder.py", line 337, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "C:\Users\13692\AppData\Local\Programs\Python\Python38\lib\json\decoder.py", line 353, in raw_decode
    obj, end = self.scan_once(s, idx)
json.decoder.JSONDecodeError: Expecting property name enclosed in double quotes: line 1 column 2 (char 1)

# Python json转字符串
>>> str = json.dumps(json, indent=2) # indent=2：按照缩进格式

# Python字符串转列表list
>>> str = "hello world"
>>> print(list(str))
['h', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd']
>>> print(str.split())
['hello', 'world']
>>> print(str.split(""))
['hello', 'world']

# Python列表list转字符串
>>> str = "".join(list)
>>> print(str)
hello world

# Python字符串转字典dict
# 方法一：使用json.load()方法
# 方法二：使用eval()函数
>>> str = "{'name' : 'john', 'gender' : 'male', 'age': 28}"
>>> dict = eval(str)
>>> print(dict)
{'name': 'john', 'gender': 'male', 'age': 28}
# 使用ast.literal_eval()方法
>>> import ast
>>> dict = ast.literal_eval(str)
>>> print(dict)
{'name': 'john', 'gender': 'male', 'age': 28}

# Python字典dict转字符串
# 方法一：使用json.dumps()方法
# 方法二：使用str()函数
```

