# JavaScript vs JQuery

### JavaScript 获取元素方法

````javascript
function dom() {
  var a = document.getElementById("dom");
  var b = a.childNodes;     // 获取a的全部子节点
  var c = a.fristChild;     // 获取a的第一个子节点
  var d = a.lastChild;      // 获取a的最后一个子节点

  var e = a.parentNode;     // 获取a的父节点

  var f = a.previousSbiling;// 获取a的上一个兄弟节点
  var g = a.nextSbiling;    // 获取a的下一个兄弟节点
}
````

### JQuery 获取元素方法

* JQuery.parent(expr);
  找父亲节点，可以传入 expr 进行过滤，例如：

  ````javascript
  $("span").parent()或者$("span").parent(".class")
  ````

* JQuery.parents(expr);
  类似于 JQuery.parent(expr)，但这是查找所有祖先元素，不限于父元素
* JQuery.children(expr);
  返回所有子节点，这个方法只会返回直接的孩子节点，不会返回所有的子孙节点
* JQuery.contents();
  返回下面的所有内容，包括节点和文本。这个方法和children()的区别在于——包括空白文本，也会被作为一个 JQuery 对象返回，children()只会返回节点
* JQuery.prev();
  返回上一个兄弟节点（不是所有的兄弟节点）
* JQuery.prevAll();
  返回所有之前的兄弟节点
* JQuery.next();
  返回下一个兄弟节点（不是所有的兄弟节点）
* JQuery.nextAll();
  返回所有之后的兄弟节点
* JQuery.siblings();
  返回兄弟姐妹节点，不分前后
* JQuery.find(expr);
  跟 JQuery.filter(expr) 完全不一样。JQuery.filter() 是从初始的 JQuery 对象集合中筛选出一部分，而 JQuery.find() 的返回结果，不会有初始集合中的内容，比如`$("p").find("span")`，是从 p 元素开始找,等同于`$("p span")`