# Python 函数

## 一、Python 函数参数传递

Python 函数的参数传递，分为可更改（mutable）对象与不可更改（immutable）对象。

在 Python 中，strings、tuples 和 numbers 是不可更改的对象，而 list、dict 等则是可以修改的对象。

- **不可变类型：**变量赋值 **a=5** 后再赋值 **a=10**，这里实际是新生成一个 int 值对象 10，再让 a 指向它。
- **可变类型：**变量赋值 **la=[1,2,3,4]** 后再赋值 **la[2]=5** 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。

Python 函数的参数传递：

- **不可变类型：**类似 c++ 的值传递，如：**整数、字符串、元组**。如：fun(a)，传递的只是 a 的值，没有影响 a 对象本身。在 fun(a) 内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。
- **可变类型：**类似 c++ 的引用传递，如：**列表，字典**。如 fun(la)，则是将 la 真正的传过去，修改后 fun 外部的 la 也会受影响

PS：Python 中一切都是对象，严格意义上**不能说值传递还是引用传递，而应该说传不可变对象和传可变对象**。

```python
# 默认参数
def printinfo( name, age = 35 ):
   "打印任何传入的字符串"
   print ("名字: ", name)
   print ("年龄: ", age)
   return
 
#调用 printinfo 函数
printinfo( age=50, name="nowcoder" )
print ("------------------------")
printinfo( name="nowcoder" )

# 不定长参数【带一个星号的参数会以元组（tuple）的形式导入，存放所有未命名的变量参数】
def printinfo( arg1, *vartuple ):
   "打印任何传入的参数"
   print ("输出: ")
   print (arg1)        # 70
   print (vartuple)    # (60, 50)
 
# 调用 printinfo 函数
printinfo( 70, 60, 50 )

# 不定长参数【带两个星号的参数会以字典（dict）的形式导入，存放所有未命名的变量参数】
def printinfo( arg1, **vardict ):
   "打印任何传入的参数"
   print ("输出: ")
   print (arg1)       # 1
   print (vardict)    # {'a':2, 'b':3}
 
# 调用printinfo 函数
printinfo(1, a=2, b=3)

# 不定长参数【如果单独出现一个星号，后面的参数必须用关键字传入，否则报错】
>>> def f(a, b, *, c):return a+b+c
>>> f(1, 2, 3)   # 报错
Traceback (most recent call last):
  File "<pyshell#75>", line 1, in <module>
    f(1,2,3)
TypeError: f() takes 2 positional arguments but 3 were given
>>> f(1, 2, c=3) # 正常
6
```

## 二、Python 匿名函数

Python 使用 lambda 来创建匿名函数。

- lambda 只是一个表达式，函数体比 def 简单很多。
- lambda的主体是一个表达式，而不是一个代码块。**仅仅能在 lambda 表达式中封装有限的逻辑进去**。
- lambda 函数拥有自己的命名空间，且不能访问自己参数列表之外或全局命名空间里的参数。
- 虽然 lambda 函数看起来只能写一行，却不等同于 C 或 C++ 的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

```python
sum = lambda arg1, arg2: arg1 + arg2 : StopIteration
 
# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```

