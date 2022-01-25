# IDEA 常用快捷键

### 一、IDEA 中文件名不同颜色含义

绿色：已经加入版本控制未提交；
红色：未加入版本控制；
蓝色：已经加入版本控制，已经提交，但有改动；
白色：已经加入版本控制，已经提交，没有改动；
灰色：版本控制已忽略文件

### 二、IDEA 和 Eclipse 常用快捷键对应表

| 快捷键描述           | Eclipse                | IntelliJ IDEA        |
| :------------------- | ---------------------- | -------------------- |
| 关闭标签页           | Ctrl + w               | Ctrl + F4            |
| 本类搜索             | Ctrl + f               | Ctrl + f             |
| 全局搜索             | Ctrl + h               | Ctrl + Shift + f     |
| 本类替换             | Ctrl + f               | Ctrl + r             |
| 全局替换             | Ctrl + h               | Ctrl + Shift + r     |
|                      |                        |                      |
| 操作后退             | Ctrl + z               | Ctrl + z             |
| 操作前进             | Ctrl + y               | Ctrl + Shift + z     |
| 代码格式化           | Ctrl + Shift + f       | Ctrl + Alt + l       |
| 批量修改变量名称     | Ctrl + 1               | Shift + F6           |
| 删除整行             | Ctrl + d               | Ctrl + y             |
| 代码上下移动         | Alt + ↑/↓              | Ctrl + Shift + ↑/↓   |
| 快速生成setget方法   | 菜单Source             | Alt + Insert         |
| 快速生成try-catch    | 菜单Source             | Ctrl + Alt + t       |
| 快速导入所有需要的包 | Ctrl + Shift + o       | Ctrl + Alt + o       |
|                      |                        |                      |
| 当前方法展开/折叠    | Ctrl + Shift + 9       | Ctrl + "+/-"         |
| 所有方法展开/折叠    | Ctrl + Alt + Shift + 9 | Ctrl + Shift + "+/-" |
| 将代码抽取成方法     |                        | Ctrl + Alt + m       |

根据类名查找：(可以":12"表示第12行)

* <kbd>Ctrl</kbd> + <kbd>n</kbd>;
* <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>n</kbd>;
* <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>n</kbd>;

打开本类方法大纲：<kbd>Alt</kbd> + <kbd>7</kbd>; 或者 <kbd>Ctrl</kbd> + <kbd>F12</kbd>;
查看类继承结构：<kbd>Ctrl</kbd> + <kbd>h</kbd>;
查看类调用了什么函数或被什么调用了：<kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>h</kbd>;
返回上/下一个浏览的位置：<kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>←/→</kbd>;
定位到错误位置：<kbd>F2</kbd>;
显示粘贴板，包含几个就近的复制项：<kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>v</kbd>;

### 三、IDEA Debug 快捷键

| 快捷键描述                            | Eclipse | IntelliJ IDEA |
| ------------------------------------- | ------- | ------------- |
| 进入内嵌的方法中                      | F5      | F7            |
| 进入下一步                            | F6      | F8            |
| 一直下一步，直至下一个断点或 response | F8      | F9            |