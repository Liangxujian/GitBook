# JavaScript

## 一、获取 json 数据的属性名及属性值

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

#### 1.1 JS设置属性

```html
data-options
```

```javascript
$('#xxxx').组件名称({})
```

#### 1.2 JS设置事件

```html
onClick:'fun1()'
```

```javascript
$('#xxxx').组件名称({
    onClick:funtion(){}
})
```

#### 1.3 JS设置属性

```javascript
$('#xxxx').组件名称('方法',参数)
```

## 二、修改 DOM 属性

```javascript
// 刷新当前页面
window.history.go(0);
// 后退到上一个页面
window.history.go(-1);
// 修改某一个DOM为隐藏
document.getElementById('id').style.hidden='none';
// 修改某一个DOM的颜色
document.getElementById('id').style.color='red';
```

## 三、判断空字符串

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

## 四、闭包

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

## 五、回车键监听事件

```javascript
// 回车事件
document.onkeydown = function(event) {
    var e = event || window.event;
    if (e && e.keyCode == 13) { // 回车键的键值为13
        vm.login();
    }
};
```

## 六、一种函数声明及调用方式

````javascript
// 声明函数
let arr = [];
let fun = function (data) {
    data.forEach((item) => {
        if (item.children && item.children.length > 0) {
            fun(item.children);
        }
        arr.push(item.code);
    });
}
// 调用函数
fun(this.$refs["dept-tree"].data);
````

## 七、var 与 let、const

```javascript
// 1、var 声明的变量会挂载在 window 上，而 let 和 const 声明的变量不会
var a = 100;
console.log(a, window.a);    // 100 100

let b = 10;
console.log(b, window.b);    // 10 undefined

const c = 1;
console.log(c, window.c);    // 1 undefined

// 2、var 声明变量存在变量提升，let 和 const 不存在变量提升
console.log(a); // undefined  ===>  a 已声明还没赋值，默认得到 undefined 值
var a = 100;

console.log(b); // 报错：b is not defined  ===> 找不到 b 这个变量
let b = 10;

console.log(c); // 报错：c is not defined  ===> 找不到 c 这个变量
const c = 10;

// 3、let 和 const 声明形成块作用域
if(1) {
    var a = 100;
    let b = 10;
    const c = 1;
}
console.log(a); // 100
console.log(b); // 报错：b is not defined  ===> 找不到 b 这个变量
console.log(c); // 报错：c is not defined  ===> 找不到 c 这个变量

// 4、同一作用域下 let 和 const 不能声明同名变量，而 var 可以
var a = 100;
var a = 10;
// 以上情况对于 var 是允许的

let a = 100;
let a = 10;
// 控制台报错：Identifier 'a' has already been declared ===> 标识符 a 已经被声明了

// 5、暂存死区
var a = 100;
if(1) {
    a = 10;
    let a = 1;
    // 控制台报错：Cannot access 'a' before initialization ===> 初始化前无法访问“a”
}

let b = 100;
if(1) {
    b = 10;
    var b = 1;
    // 控制台报错：Identifier 'b' has already been declared ===> 标识符“b”已声明
}

// 6、const
// 6.1 一旦声明必须赋值，不能使用 null 占位
const a;             // 控制台报错：Missing initializer in const declaration ===> 常量声明中缺少初始值设定项

// 6.2 声明后不能再修改
const a = 100;
a = 10;              // 控制台报错：Assignment to constant variable ===> 常量变量赋值

// 6.3 如果声明的是复合类型数据，可以修改其属性
const list = [];
list[0] = 10;
console.log(list);　　// [10]

const obj = {a:100};
obj.name = 'apple';
obj.a = 10000;
console.log(obj);　　// {a:10000,name:'apple'}
```

