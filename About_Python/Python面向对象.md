# Python 面向对象

## 一、Python 类对象

> 在类中，两个下划线开头表示私有
>
> __private_method：表示该方法为私有方法，只能在类中使用，不能在类外部调用
>
> __private_attribute：表示该属性为私有属性，只能在类中使用，不能在类外部调用

```python
class Complex:
    # 构造方法，构造方法必须有一个额外的第一个参数名称，惯例是self
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart
    i = 12345
    def f(self):
        return 'hello world'

# 调用构造方法实例化
x = Complex(3.0, -4.5)
print(x.r, x.i)   # 输出结果：3.0 -4.5
# 访问类的属性和方法
print("MyClass 类的属性 i 为：", x.i)
print("MyClass 类的方法 f 输出为：", x.f())
```

### 1.1 Python 类对象变量定义

```python
xx     # 公有变量
_xx    # 单前置下划线，私有化属性或方法，类对象和子类可以访问，from somemodule import * 禁止导入
__xx   # 双前置下划线，私有化属性或方法，无法在外部直接访问（名字重整所以访问不到）
__xx__ # 双前后下划线，系统定义名字（不要自己发明这样的名字）
xx_    # 单后置下划线，用于避免与Python关键词的冲突
```

### 1.2 Python 类常用方法名称规范

- **init** ：构造函数，在生成对象时调用
- **del** ：析构函数，释放对象时使用
- **repr** ：打印，转换
- **setitem** ：按照索引赋值
- **getitem**：按照索引获取值
- **len**：获得长度
- **cmp**：比较运算
- **call**：函数调用
- **add**：加运算
- **sub**：减运算
- **mul**：乘运算
- **truediv**：除运算
- **mod**：求余运算
- **pow**：乘方

## 二、Python 类继承

> Python 支持多继承

```python
# 类定义
class people:
    # 定义基本属性
    name = ''
    age = 0
    # 定义私有属性，私有属性在类外部无法直接进行访问
    __weight = 0
    # 定义构造方法
    def __init__(self, n, a, w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name, self.age))

# 单继承示例
class student(people):
    grade = ''
    def __init__(self, n, a, w, g):
        # 调用父类的构函
        people.__init__(self, n, a, w)
        self.grade = g
    # 重写父类方法
    def speak(self):
        print("%s 说: 我 %d 岁了，我在读 %d 年级"%(self.name, self.age, self.grade))

s = student('ken', 10, 60, 3)
s.speak()

# 另一个类
class speaker():
    topic = ''
    name = ''
    def __init__(self,n,t):
        self.name = n
        self.topic = t
    def speak(self):
        print("我叫 %s，我是一个演说家，我演讲的主题是 %s"%(self.name,self.topic))

# 多重继承
class sample(speaker, student):
    a =''
    def __init__(self, n, a, w, g, t):
        student.__init__(self, n, a, w, g)
        speaker.__init__(self, n, t)
 
test = sample("Tim", 25, 80, 4, "Python")
test.speak()   # 方法名同，默认调用的是在括号中靠前的父类的方法
```

