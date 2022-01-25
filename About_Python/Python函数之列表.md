# Python 函数之列表

>类似 Object 类型的数组
>
>Python 列表在尾部添加和弹出元素速度快，在列表中间插入或取头部元素速度慢（因为其他元素需要一个一个地移动）
>
>因此把 Python 列表当成一个队列【先进先出】来使用，效率不高
>
>可以把 Python 列表当成一个堆栈【先进后出】来使用，append() 在列表末尾添加元素，配合 pop() 把一个元素从堆栈顶释放出来

## 一、Python 列表函数

| 函数      | 描述                     | 示例                                                         |
| --------- | ------------------------ | ------------------------------------------------------------ |
| len(list) | 返回列表元素个数         |                                                              |
| max(list) | 返回列表元素中的最大值   |                                                              |
| min(list) | 返回列表元素中的最小值   |                                                              |
| list(seq) | 将元组或字符串转换为列表 | print(list("Hello World"))<br>>>> [``'H'``, ``'e'``, ``'l'``, ``'l'``, ``'o'``, ``' '``, ``'W'``, ``'o'``, ``'r'``, ``'l'``, ``'d'``] |

## 二、Python 列表方法

```python
# 列表声明
list = []
list = ["name","age","gender"]
print(list[1]) # 输出：age

# 在列表末尾添加新的对象
list.append(obj)

# 统计某个元素在列表中出现的次数
list.count(obj)

# 在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
list.extend(seq)

# 从列表中找出某个值第一个匹配项的索引位置
# start - 可选，查找的起始位置
# end - 可选，查找的结束位置
list.index(x[, start[, end]])

# 将指定对象插入列表的指定位置
list.insert(index, obj)

# 移除列表中的一个元素（默认最后一个元素），并返回该元素的值（按下标删除）
list.pop([index=-1])
# 按下标删除（删除下标为1的元素）
del list[1]
# 按下标删除（删除2<=下标<4的元素）
del list[2:4]

# 移除列表中某个值的第一个匹配项（按值删除）
list.remove(obj)

# 反向列表中元素
list.reverse()

# 对原列表进行排序，如果指定参数，则使用比较函数指定的比较函数
# key - 用来进行比较的元素，可在迭代对象中指定一个元素来进行排序
# reverse - 排序规则，True为降序，False为升序（默认）
list.sort( key=None, reverse=False)

def takeSecond(elem):
    return elem[1]
random = [(2, 2), (3, 4), (4, 1), (1, 3)]
random.sort(key=takeSecond)
print (``'排序列表：'``, random)
# 排序列表：[(4, 1), (2, 2), (1, 3), (3, 4)]

# 清空列表，类似于 del a[:]
list.clear()

# 复制列表，类似于 a[:]
list.copy()

list1 = ['Google', 'Nowcoder', 'Taobao', 'Baidu']
list2 = list1.copy()
print ("list2 列表: ", list2)
# list2 列表:  ['Google', 'Nowcoder', 'Taobao', 'Baidu']
```

## 