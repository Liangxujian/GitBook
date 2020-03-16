# 前端构造 form 表单

### 项目需求

模仿老项目的跳转方式，即通过后台返回的数据，在前端 HTML 通过 JS 脚本构造出一个 form 表单，然后跳转发送到对应的服务中。

### 代码记录

````javascript
// 首先在HTML中获取后台数据，这里采用的方法是用一个hidden的表单接收
window.onload = function() {
    //首先创建一个form表单
    var tempForm = document.createElement("form");
    tempForm.id="tempForm1";
    //制定发送请求的方式为post
    tempForm.method="post";
    //此为window.open的url，通过表单的action来实现
    var url = "${url}";
    tempForm.action=url;

    //利用表单的target属性来绑定window.open的一些参数（如设置窗体属性的参数等）
    tempForm.target="_self";

    //创建input标签，用来设置参数
    var hideInput1 = document.getElementById("ip1");
    var hideInput2 = document.getElementById("ip2");
    var hideInput3 = document.getElementById("ip3");
    var hideInput4 = document.getElementById("ip4");
    var hideInput5 = document.getElementById("ip5");
    var hideInput6 = document.getElementById("ip6");
    var hideInput7 = document.getElementById("ip7");
    var hideInput8 = document.getElementById("ip8");
    //将input表单放到form表单里
    tempForm.appendChild(hideInput1);
    tempForm.appendChild(hideInput2);
    tempForm.appendChild(hideInput3);
    tempForm.appendChild(hideInput4);
    tempForm.appendChild(hideInput5);
    tempForm.appendChild(hideInput6);
    tempForm.appendChild(hideInput7);
    tempForm.appendChild(hideInput8);

    //此为给form表单绑定事件（onsubmit），由于存在浏览器兼容问题所以此处加了一个判断。
    //但实质都是为form表单绑定一个提交事件，以便之后触发实现window.open效果
    if(document.all){
        tempForm.attachEvent("onsubmit",function(){});        //IE
    }else{
        var subObj = tempForm.addEventListener("submit",function(){},false);    //firefox
    }
    //将此form表单添加到页面主体body中
    document.body.appendChild(tempForm);

    //然后触发onsubmit事件时执行openWindow(name)函数。因为有特定浏览器会打开一个空白页面所以这里的function（甚至整个if）可以根据情况省略，经试验这不会影响模拟window.open的效果。
    if(document.all){
        tempForm.fireEvent("onsubmit",function(){ openWindow("_self"); });
    }else{
        //tempForm.dispatchEvent(new Event("submit"));
        $("form").trigger("onsubmit",function(){ openWindow("_self"); });
    }

    /*
    // 被注释的代码，不可兼容IE9
    // 增加提交监听 处理浏览器的兼容性
    if (window.attachEvent) {
        tempForm.attachEvent("onsubmit",function(){ openWindow(name); });
    } else if (window.addEventListener) {
        tempForm.addEventListener("onsubmit",function(){ openWindow(name); });
    }
    document.body.appendChild(tempForm);
    //触发监听 处理浏览器的兼容性
    if(tempForm.fireEvent){
        tempForm.fireEvent("onsubmit");
    } else {
        var evt = document.createEvent('HTMLEvents');
        evt.initEvent('onsubmit', true, true);
        tempForm.dispatchEvent(evt);
    }
    */

    //手动触发，提交表单
    tempForm.submit();
    //从body中移除form表单
    document.body.removeChild(tempForm);
}

function openWindow(name) {
    window.open('about:blank',name,'height=400, width=400, top=0, left=0, toolbar=yes, menubar=yes, scrollbars=yes, resizable=yes,location=yes, status=yes');
}
````

