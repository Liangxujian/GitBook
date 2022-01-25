# JQuery

### 一、JQuery ajax 请求示例：

````javascript
$.ajax({
    url: "/users/login" ,//url
    type: "POST",// 方法类型
    dataType: "json",// 预期服务器返回的数据类型
    data: $('#form1').serialize(),
    success: function (result) {
        console.log(result);// 打印服务端返回的数据(调试用)
        if (result.resultCode == 200) {
            alert("SUCCESS");
        } ;
    },
    error : function() {
        alert("异常！");
	}
});
````

1.	url：
要求为 String 类型的参数，（默认为当前页地址）发送请求的地址

2.	type：
要求为 String 类型的参数，请求方式（POST 或 GET）默认为 GET。注意其他 http 请求方法，例如 put 和 delete 也可以使用，但仅部分浏览器支持。

3. dataType：
   要求为 String 类型的参数，预期服务器返回的数据类型。如果不指定，JQuery 将自动根据 http 包 mime 信息返回responseXML 或 responseText，并作为回调函数参数传递。可用的类型如下：

   * xml：返回 XML 文档，可用 JQuery 处理。
   * html：返回纯文本 HTML 信息；包含的script标签会在插入 DOM 时执行。
   * script：返回纯文本 JavaScript 代码。不会自动缓存结果。除非设置了 cache 参数。注意在远程请求时（不在同一个域下），所有 POST 请求都将转为 GET 请求。
   * json：返回 JSON 数据。
   * jsonp：JSONP 格式。使用 SONP 形式调用函数时，例如：`myurl?callback=?`，jQuery 将自动替换后一个`“?”`为正确的函数名，以执行回调函数。
   * text：返回纯文本字符串。

4.	date：
要求为 Object 或 String 类型的参数，发送到服务器的数据。如果已经不是字符串，将自动转换为字符串格式。GET 请求中将附加在 url 后。
防止这种自动转换，可以查看“processData”选项。
对象必须为 key/value 格式，例如：`{foo1:"bar1",foo2:"bar2"}`转换为`&foo1=bar1&foo2=bar2`。
如果是数组，JQuery 将自动为不同值对应同一个名称。例如：`{foo:["bar1","bar2"]}`转换为`&foo=bar1&foo=bar2`。

5.	timeout：
要求为 Number 类型的参数，设置请求超时时间（毫秒）。此设置将覆盖 $.ajaxSetup() 方法的全局设置。

6.	async：
要求为 Boolean 类型的参数，默认设置为 true，所有请求均为异步请求。如果设置为 false，则发送同步请求。注意，同步请求将锁住浏览器，用户其它操作必须等待请求完成才可以执行。

7.	cache：
要求为 Boolean 类型的参数，默认为 true（当 dataType 为 script 时，默认为 false），设置为 false 将不会从浏览器缓存中加载请求信息。

8. beforeSend：
   要求为 Function 类型的参数，发送请求前可以修改 XMLHttpRequest 对象的函数，例如添加自定义 HTTP 头。在 beforeSend 中如果返回 false 可以取消本次 ajax 请求。XMLHttpRequest 对象是惟一的参数。

   ````javascript
   function(XMLHttpRequest) {
   	this;   //调用本次ajax请求时传递的options参数
   }
   ````

9. complete：
   要求为 Function 类型的参数，请求完成后调用的回调函数（请求成功或失败时均调用）。参数：XMLHttpRequest 对象和一个描述成功请求类型的字符串。

   ````javascript
   function(XMLHttpRequest, textStatus) {
   	this;    //调用本次ajax请求时传递的options参数
   }
   ````

10. success：
    1) 由服务器返回，并根据 dataType 参数进行处理后的数据。
    2) 描述状态的字符串。

    ````javascript
    function(data, textStatus) {
        //data可能是xmlDoc、jsonObj、html、text等等
        this;  //调用本次ajax请求时传递的options参数
    }
    ````

11. error：
    要求为 Function 类型的参数，请求失败时被调用的函数。该函数有 3 个参数，即 XMLHttpRequest 对象、错误信息、捕获的错误对象(可选)。ajax 事件函数如下：

    ````javascript
    function(XMLHttpRequest, textStatus, errorThrown) {
        //通常情况下textStatus和errorThrown只有其中一个包含信息
        this;   //调用本次ajax请求时传递的options参数
    }
    ````

12.	contentType：
要求为 String 类型的参数，当发送信息至服务器时，内容编码类型默认为`"application/x-www-form-urlencoded"`。该默认值适合大多数应用场合。

13. dataFilter：
    要求为 Function 类型的参数，给 Ajax 返回的原始数据进行预处理的函数。提供 data 和 type 两个参数。data 是 Ajax 返回的原始数据，type 是调用 jQuery.ajax 时提供的 dataType 参数。函数返回的值将由 JQuery 进一步处理。

    ````javascript
    function(data, type){
        //返回处理后的数据
        return data;
    }
    ````

14.	global：
要求为 Boolean 类型的参数，默认为 true。表示是否触发全局 ajax 事件。设置为 false 将不会触发全局 ajax 事件，ajaxStart 或 ajaxStop 可用于控制各种 ajax 事件。

15.	ifModified：
要求为 Boolean 类型的参数，默认为 false。仅在服务器数据改变时获取新数据。服务器数据改变判断的依据是 Last-Modified 头信息。默认值是 false，即忽略头信息。

16.	jsonp：
要求为 String 类型的参数，在一个 jsonp 请求中重写回调函数的名字。该值用来替代在`"callback=?"`这种 GET 或 POST 请求中 URL 参数里的`"callback"`部分，例如：`{jsonp:'onJsonPLoad'}`会导致将`"onJsonPLoad=?"`传给服务器。

17.	username：
要求为 String 类型的参数，用于响应 HTTP 访问认证请求的用户名。

18.	passoword：
要求为 String 类型的参数，用于响应 HTTP 访问认证请求的密码。

19.	processData：
要求为 Boolean 类型的参数，默认为 true。默认情况下，发送的数据将被转换为对象（从技术角度来讲并非字符串）以配合默认内容类型`"application/x-www-form-urlencoded"`。如果要发送 DOM 树信息或者其他不希望转换的信息，请设置为 false。

20.	scriptCharset：
要求为 String 类型的参数，只有当请求时 dataType 为`"jsonp"`或者`"script"`，并且 type 是 GET 时才会用于强制修改字符集（charset）。通常在本地和远程的内容编码不同时使用。