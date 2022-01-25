# Python 函数之集合

> 集合是一个无序的不重复元素序列，类似于 set，可利用其来去重
>
> 注意：创建一个空集合必须用 **set()** 而不是 **{ }**，因为 **{ }** 是用来创建一个空字典

## 一、Python 集合方法

```python
>>> thisset = set()
>>> thisset = set(("Google", "Nowcoder", "Taobao"))
>>> print(thisset)
{'Taobao', 'Google', 'Nowcoder'}

# 拷贝集合
>>> thatset = thisset.copy()
>>> print(thatset)
{'Taobao', 'Google', 'Nowcoder'}

# 增加
>>> thisset = {'Taobao', 'Nowcoder', 'Google'}
>>> thisset.add("Facebook")
>>> print(thisset)
{'Taobao', 'Nowcoder', 'Google', 'Facebook'}

# 返回多个集合间的交集
>>> thinset = {"Google", "Nowcoder", "Jingdong"}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> print(thisset.intersection(thatset, thinset))
{'Google', 'Nowcoder'}

# 返回两个集合间的差集
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thatset.difference(thisset)
set()
>>> thisset.difference(thatset)
{'Facebook'}

# 返回两个集合的并集
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thinset = {"Google", "Nowcoder", "Jingdong"}
>>> print(thisset.union(thatset, thinset))
{'Facebook', 'Jingdong', 'Nowcoder', 'Taobao', 'Google'}

# 返回两个集合间不重复的元素（对称差集/左右差集）
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> print(thisset.symmetric_difference(thatset))
{'Facebook'}

# 移除当前集合中在另外一个指定集合相同的元素，并将另外一个指定集合中不同的元素插入到当前集合中
>>> thisset = {'Twitter', 'Facebook', 'Google', 'Jingdong'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thatset.symmetric_difference_update(thisset)
>>> print(thatset)
{'Nowcoder', 'Twitter', 'Facebook', 'Jingdong', 'Taobao'}

# 判断两个集合是否包含相同的元素，如果没有返回 True，否则返回 False
>>> thisset = {'Twitter', 'Facebook', 'Google', 'Jingdong'}
>>> thinset = {"Google", "Nowcoder", "Jingdong"}
>>> thisset.isdisjoint(thinset)
False

# 判断一个集合是否是另一个集合的子集，如果是返回 True，否则返回 False（子判断父）
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thatset.issubset(thisset)
True
>>> thinset.issubset(thisset)
False

# 判断一个集合是否完全包含另一个集合，如果是返回 True，否则返回 False（父判断子）
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thisset.issuperset(thatset)
True
>>> thisset.issuperset(thinset)
False

# 移除两个集合中都存在的元素
>>> thisset = {'Taobao', 'Nowcoder', 'Google', 'Facebook'}
>>> thatset = {'Taobao', 'Google', 'Nowcoder'}
>>> thisset.difference_update(thatset)
>>> print(thisset)
{'Facebook'}
>>> print(thatset)
{'Taobao', 'Google', 'Nowcoder'}

# 删除指定元素（删除不存在的元素会报错）
>>> thisset = {'Facebook'}
>>> thisset.remove("Facebook")
>>> print(thisset)
set()
>>> thisset.remove("baidu")
Traceback (most recent call last):
  File "<pyshell#5>", line 1, in <module>
    thisset.remove("baidu")
KeyError: 'baidu'

# 删除指定元素（这里与 remove() 方法不同的是，在移除一个不存在的元素时不会报错）
>>> thisset.discard("baidu")

# 随机删除一个元素并返回
>>> thinset = {"Google", "Nowcoder", "Jingdong"}
>>> thinset.pop()
'Google'
>>> print(thinset)
{'Jingdong', 'Nowcoder'}
>>> thinset.pop()
'Jingdong'
>>> print(thinset)
{'Nowcoder'}

# 添加元素/另一个集合到当前集合中，如果元素/集合中的元素已存在，当前集合没有变化
>>> thisset = {'Taobao', 'Nowcoder', 'Google'}
>>> thisset.update({1,3})
>>> print(thisset)
{1, 3, 'Taobao', 'Nowcoder', 'Google'}
>>> thisset.update([1,4],[5,6])
>>> print(thisset)
{1, 3, 4, 5, 6, 'Taobao', 'Nowcoder', 'Google'}

# 清空
>>> thisset.clear()

# 查看长度
>>> len(thisset)
0

# 判断元素是否存在于集合内
>>> thisset = set()
>>> "Taobao" in thisset
False
```

## 二、Python 集合遍历

### 2.1 Python 集合迭代器遍历

迭代器是一个可以记住遍历的位置的对象。（字符串，列表或元组对象都可用于创建迭代器）

迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。

迭代器有两个基本的方法：**iter()** 和 **next()**。

```python
#!/usr/bin/python3
 
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
for x in it:
    print (x, end=" ")
```

```python
import sys         # 引入 sys 模块
 
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
 
while True:
    try:
        print (next(it))
    except StopIteration:
        sys.exit()
```

### 2.2 Python 集合生成器遍历

```python
#!/usr/bin/python3
 
import sys
 
def fibonacci(n): # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n):
            return
        yield a          # 遇到 yield 时函数会暂停并保存当前所有的运行信息，这里将a的值缓存到迭代对象中
        a, b = b, a + b  # a = b;b = a + b
        counter += 1     # 遍历次数累计
f = fibonacci(10) # f 是一个迭代器，由生成器返回生成
 
while True:
    try:
        print (next(f), end=" ")
    except StopIteration:
        sys.exit()
```

## 