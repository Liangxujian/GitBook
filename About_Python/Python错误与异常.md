# Python 错误与异常

python中定义的主要异常

```python
- BaseException                      # 所有异常的父类
    - SystemExit                     # 由sys.exit()抛出的异常
    - KeyBoardInterrupt              # 通常由ctrl+c或者Delete抛出的异常
    - GeneratorExit                  # 当生成器被关闭时抛出的异常
    - Exception                      # 
        - StopIteration              # 迭代结束异常
        - StopAsyncIteration         # 由异步迭代的`__anext__()`抛出的异常
        - ArithmeticError            # 各种算数错误引起的异常
            - FloatingPointError     # 浮点数操作错误
            - OverflowError          # 结果超出范围
            - ZeroDivisionError      # ０为除数异常
    - AssertionError                 # assert错误异常
    - AttributeError                 # 属性引用异常
    - BufferError                    # 缓存错误
    - EOFError                       # 读不到数据
    - ImportError                    # import错误
        - ModuleNotFoundError        # 找不多模块
    - LookupError                    # 由索引和key值引起的异常
        - IndexError                 # 索引错误
        - KeyError                   # 字典key值错误
    - MemortError                    # 内存溢出异常
    - NameError                      # 本地和全局找不到变量名
        - UnboundLocalError          # 局部变量没有赋值
    - OSError                        # system错误
        - BlockingIOError            # 调用阻塞异常错误
        - ChildProcessError          # 子进程
        - ConnectionError            # 连接
            - BrokenPipeError        # 管道读写异常
            - ConnectionAbortedError # 连接失败
            - ConnectionRefusedError # 连接拒绝
            - ConnectionResetError   # 连接重置
        - FileExistsError            # 创建文件和文件夹错误
        - FileNotFoundError          # 文件未找到
        - InterruptedError           # 中断错误
        - IsADirectoryError          # 文件操作用在文件夹上
        - NotADirectoryError         # 不是文件夹
        - PermissionError            # 权限
        - ProcessLookupError         # 进程不存在
        - TimeoutError               # 超时
    - ReferenceError                 # 引用异常
    - RuntimeError                   # 
        - NotImplementedError        # 运行抽象方法
        - RecursionError             # 超出最大递归深度
    - SyntaxError                    # 语法错误
        - IndentationError           # 缩进错误
            - TabError               # tab错误
    - SystemError                    # 解释器中断
    - TypeError                      # 类型错误
    - ValueError                     # 赋值错误
        - UnicodeError               # 
            - UnicodeEncodeError     # unicode编码错误
            - UnicodeDecodeError     # unicode解码错误
            - UnicodeTranslateError  # unicode转换错误
    - Warning                        # 
        - DeprecationWarning         # 操作不赞成警告
        - PendingDeprecationWarning  # 表明此操作将来会被弃用
        - UserWarning                # 用于用户生成警告
        - SyntaxWarning              # 语法可疑警告
        - RuntimeWarning             # 运行警告
        - FutureWarning              # 将会改变警告
        - ImportWarning              # 导入警告
        - UnicodeWarning             # unicode相关警告
        - BytesWarning               # 字节相关警告
        - ResourceWarning            # 资源使用情况警告
```

Python常见异常类型

```python
# AssertionError：当 assert 关键字后的条件为假时，程序运行会停止并抛出 AssertionError 异常
>>> demo_list = ['Python案例测试']
>>> assert len(demo_list) > 0
>>> demo_list.pop()
'Python案例测试'
>>> assert len(demo_list) > 0
Traceback (most recent call last):
  File "<pyshell#6>", line 1, in <module>
    assert len(demo_list) > 0
AssertionError

# AttributeError：当试图访问的对象属性不存在时抛出的异常
>>> demo_list = ['Python案例测试']
>>> demo_list.len
Traceback (most recent call last):
  File "<pyshell#10>", line 1, in <module>
    demo_list.len
AttributeError: 'list' object has no attribute 'len'

# IndexError：索引超出序列范围会引发此异常
>>> demo_list = ['Python案例测试']
>>> demo_list[3]
Traceback (most recent call last):
  File "<pyshell#8>", line 1, in <module>
    demo_list[3]
IndexError: list index out of range

# KeyError：字典中查找一个不存在的关键字时引发此异常
>>> demo_dict={'Python案例测试':"c.biancheng.net"}
>>> demo_dict["Python案例"]
Traceback (most recent call last):
  File "<pyshell#12>", line 1, in <module>
    demo_dict["Python案例"]
KeyError: 'Python案例'

# NameError：尝试访问一个未声明的变量时，引发此异常
>>> Python案例测试
Traceback (most recent call last):
  File "<pyshell#15>", line 1, in <module>
    Python案例测试
NameError: name 'Python案例测试' is not defined

# TypeError：不同类型数据之间的无效操作
>>> 1+'Python案例测试'
Traceback (most recent call last):
  File "<pyshell#17>", line 1, in <module>
    1+'Python案例测试'
TypeError: unsupported operand type(s) for +: 'int' and 'str'

# ZeroDivisionError：除法运算中除数为 0 引发此异常
>>> a = 1/0
Traceback (most recent call last):
  File "<pyshell#2>", line 1, in <module>
    a = 1/0
ZeroDivisionError: division by zero
```

