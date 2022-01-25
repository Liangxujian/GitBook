# Python 函数之字典

> 字典类似于 map，键必须是唯一的，但值则不必

## 一、Python 字典函数

| 函数           | 描述                                             |
| :------------- | ------------------------------------------------ |
| len(dict)      | 计算字典元素个数，即键的总数                     |
| str(dict)      | 输出字典，以可打印的字符串表示                   |
| type(variable) | 返回输入的变量类型，如果变量是字典就返回字典类型 |

## 二、Python 字典方法

```python
# 字典声明
dict = {}
dict = dict()
dict = {"a": 1, "b": [2, "3"]}
dict["c"] = "4"
print(dict.get("b")) # 输出：[2, '3']

# 删除字典内所有元素
dict.clear()

# 返回一个字典的浅复制
dict.copy()

# 创建一个新字典，以序列 seq 中元素做字典的键，value 为字典所有键对应的初始值
# seq -- 字典键值列表
# value -- 可选参数, 设置键序列（seq）对应的值，默认为 None
dict.fromkeys(seq[, value])

seq = ('name', 'age', 'sex')
dict = dict.fromkeys(seq)
print ("新的字典为 : %s" %  str(dict))  # 新的字典为 : {'age': None, 'name': None, 'sex': None}
dict = dict.fromkeys(seq, 10)
print ("新的字典为 : %s" %  str(dict))  # 新的字典为 : {'age': 10, 'name': 10, 'sex': 10}

# 返回指定键的值，如果值不在字典中返回默认值
# key -- 字典中要查找的键
# default -- 如果指定键的值不存在时，返回该默认值
dict.get(key, default=None)

dict = {'Name': 'Nowcoder', 'Age': 27}
print ("Sex 值为 : %s" %  dict.get('Sex', "NA"))  # Sex 值为 : NA

# 判断键是否存在于字典中，存在返回 true，否则返回 false（与 not in 操作符相反）
key in dict

dict = {'Name': 'Nowcoder', 'Age': 7} 
# 检测键 Age 是否存在
if 'Age' in dict:
    print("键 Age 存在")
else :
    print("键 Age 不存在")

# 以列表返回可遍历的(键, 值) 元组数组
dict.items()

dict = {'Name': 'Nowcoder', 'Age': 7}
print ("Value : %s" %  dict.items())  # Value : dict_items([('Age', 7), ('Name', 'Nowcoder')])

# 返回一个可迭代对象，可以使用 list() 来转换为列表
# 注意：Python2.x 是直接返回列表
dict.keys()

>>> dict = {'Name': 'Nowcoder', 'Age': 7}
>>> dict.keys()
dict_keys(['Name', 'Age'])
>>> list(dict.keys())             # 转换为列表
['Name', 'Age']

# 和 get()方法类似, 如果键不已经存在于字典中，将会添加键并将值设为默认值
# key -- 查找的键值
# default -- 键不存在时，设置的默认键值
dict.setdefault(key, default=None)

dict = {'Name': 'Nowcoder', 'Age': 7}
print ("Sex 键的值为 : %s" %  dict.setdefault('Sex', None))  # Sex 键的值为 : None

# 把字典参数 dict2 的 key/value(键/值) 对更新到字典 dict 里
# dict2 -- 添加到指定字典dict里的字典
dict.update(dict2)

# 返回一个迭代器，可以使用 list() 来转换为列表，列表为字典中的所有值
dict.values()

dict = {'Sex': 'female', 'Age': 7, 'Name': 'Zara'}
print ("字典所有值为 : ",  list(dict.values()))  # 字典所有值为 :  ['female', 7, 'Zara']

# 删除字典给定键 key 所对应的值，返回值为被删除的值（key值必须给出，否则返回default值）
pop(key[,default])

>>> site= {'name': '牛客教程', 'alexa': 10000, 'url': 'www.nowcoder.com'}
>>> pop_obj=site.pop('name')
>>> print(pop_obj)
牛客教程

# 随机返回并删除字典中的最后一对键和值
popitem()

site= {'name': '牛客教程', 'alexa': 10000, 'url': 'www.nowcoder.com'}
pop_obj=site.popitem()
print(pop_obj)  # ('url', 'www.nowcoder.com')
print(site)  # {'name': '牛客教程', 'alexa': 10000}
```

## 