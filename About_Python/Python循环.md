# Python 循环

```python
for item in list:
    print(item)

for idx, item in enumerate(list):
    print(str(idx) + " - " + item)

for idx range(len(list)):
    print(list[idx])
```

* break：跳出最小封闭的for或while循环
* continue：跳出本次循环，执行下一次
* exit(0)：中断程序退出，告诉解释器或操作系统这是正常退出
* exit(1)：中断程序退出，告诉解释器或操作系统这是发生了错误退出
* pass：不做任何事情，只起到占位的作用