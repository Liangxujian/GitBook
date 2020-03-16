#### JavaScript

#### 一、JavaScript 获取 json 数据的属性名及属性值

```javascript
var jsons = {
    name: "1111",
    age: 18
};
for (var key in jsons) {
    alert(key);
    alert(jsons[key]);
}
```

##### JS设置属性

```html
data-options
```

```javascript
$('#xxxx').组件名称({})
```

##### JS设置事件

```html
onClick:'fun1()'
```

```javascript
$('#xxxx').组件名称({
    onClick:funtion(){}
})
```

##### JS设置属性

```javascript
$('#xxxx').组件名称('方法',参数)
```



#### 二、JavaScript 修改 DOM 属性

##### 刷新当前页面：

```javascript
window.history.go(0);
```

##### 后退到上一个页面：

```javascript
window.history.go(-1);
```

##### 修改某一个DOM为隐藏：

```javascript
document.getElementById('id').style.hidden='none';
```

##### 修改某一个DOM的颜色：

```javascript
document.getElementById('id').style.color='red';
```



#### 三、JavaScript 判断空字符串

很多情况下，都是直接用 length 来直接判断字符串是否为空：

```javascript
var strings = '';
if (string.length == 0) {
    alert('不能为空');
}
```

但如果用户输入的是空格，制表符，换页符呢？这样的话，也是不为空的，但是这样的数据就不是我们想要的吧。

这时候可以用正则表达式来把这些“空”的符号去掉来判断：

```javascript
var strings = ' ';
if (strings.replace(/(^\s*)|(\s*$)/g, "").length == 0) {
    alert('不能为空');
}
```

`\s`小写的 s 是，匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。



#### 四、JavaScript 闭包

闭包特点：

1. 在 function 里嵌套 function
2. 内部的 function 访问外部 function 里的变量
3. 外部 function return 内部 function
4. 外部变量获取外部 function，同时可以进行传值操作
5. 调用变量，同时也可以进行传值操作

```javascript
function a(x) {
    var tmp = 3;
    function b(y) {
        alert(x + y + (++tmp));
    }
    return b;
}
var c = a(2); // var 现在是一个闭包
c(10);
```



#### 五、JavaScript 回车键监听事件

```javascript
// 回车事件
document.onkeydown = function(event) {
    var e = event || window.event;
    if (e && e.keyCode == 13) { // 回车键的键值为13
        vm.login();
    }
};
```

