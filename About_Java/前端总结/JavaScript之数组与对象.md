# JavaScript 之数组与对象

## 一、数组

```javascript
let arr = ["1","2"];
// 1. join()：将数组中所有元素都转化为字符串并连接在一起
arr.join(); // "1,2"

// 2. reverse()：将数组中的元素颠倒顺序
arr.reverse(); // ["2", "1"]

// 3. concat()：数组合并的功能，返回新数组，原数组不受影响
// 该方法可用于数组拷贝，即将原数组数据拷贝到一个空白的新数组中，但只是浅拷贝
let brr = ["3","4","5","6","7","8","9"];
let crr = arr.concat(brr); // ["1", "2", "3", "4", "5", "6", "7", "8", "9"]
arr; // ['1', '2']
brr; // ["3", "4", "5", "6", "7", "8", "9"]
crr; // ["1", "2", "3", "4", "5", "6", "7", "8", "9"]

// 4. slice()：截取数组生成新数组，原数组不受影响
// 该方法可用于数组拷贝，即将原数组整个截取到一个空白的新数组中，但只是浅拷贝
// 返回的数组包含第一个参数指定的位置和所有到但不含第二个参数指定位置之间的所有元素（a ≦ 新数组 ﹤ b）
// 如果为负数，表示相对于数组中最后一个元素的位置。如果只有一个参数，表示到数组末尾
crr.slice(2);    // ["3", "4", "5", "6", "7", "8", "9"]
crr.slice(-2);   // ["8", "9"]（从末尾倒数第二个开始往后取数）
crr.slice(2,18); // ["3", "4", "5", "6", "7", "8", "9"]（超过最大长度的话，则只显示到最后结果）
crr.slice(2,8);  // ["3", "4", "5", "6", "7", "8"]
crr.slice(2,-1); // ["3", "4", "5", "6", "7", "8"]（从最末尾倒数第一个的前面开始截取）
crr.slice(2,-2); // ["3", "4", "5", "6", "7"]（从最末尾倒数第二个的前面开始截取）

// 5. splice()：从数组中删除元素、插入元素到数组中或者同时完成这两种操作
// 第一个参数为指定插入或删除的起始位置（如果只有一个参数，默认删除参数后边的所有元素）；
// 第二个参数为要删除的个数；
// 之后的参数表示需要插入到数组中的元素
// 操作会返回一个由被删除元素组成的数组，相当于新建了一个数组，并修改了原数组
crr = ["1","2","3","4","5","6","7","8","9"];
crr.splice(4); // ["5", "6", "7", "8", "9"]（从下标index = 4起删除后面的元素，返回被删除的元素数组，原数组已被修改）
crr; // ["1", "2", "3", "4"]
crr.splice(2,2); // ["3", "4"]（从下标index = 2起删除2个元素，返回被删除的元素数组，原数组已被修改）
crr; // ["1", "2"]
crr.splice(0,0,"7","8","9"); // []（从下标index = 0起删除0个元素，从数组头部开始添加元素"7","8","9"）
crr; // ["7", "8", "9", "1", "2"]

// 6. push()：在数组末尾添加一个或多个元素，并返回新数组长度
crr.push("5","6"); // 7
crr; // ["7", "8", "9", "1", "2", "5", "6"]

// 7. pop()：从数组末尾删除1个元素（删且只删除1个）, 并返回被删除的元素
crr.pop(); // "6"
crr; // ["7", "8", "9", "1", "2", "5"]

// 8. shift()：从数组头部删除1个元素（删且只删除1个）, 并返回被删除的元素
crr.shift(); // "7"
crr; // ["8", "9", "1", "2", "5"]

// 9. unshift()：在数组头部添加一个或多个元素，并返回新数组长度
crr.unshift("3","4"); // 7
crr; // ["3", "4", "8", "9", "1", "2", "5"]

// 10. toString()和toLocaleString()：将数组的每个元素转化为字符串，并且用逗号分隔进行拼接，返回拼接后的字符串。功能类似join()
crr.toString(); // "3,4,8,9,1,2,5"
crr.toLocaleString(); // "3,4,8,9,1,2,5"

// 11. indexOf()和lastIndexOf()：查找元素在数组中的下标，如果数组中不存在该元素，则返回-1
crr = ["3","4","8","9","1","2","5","9"];
crr.indexOf("9"); // 3（默认从数组头部开始遍历→，返回元素在数组中第一次匹配到的下标）
crr.indexOf("9",4); // 7（从下标4开始遍历数组→，返回元素在数组中第一次匹配到的下标）
crr.lastIndexOf("9"); // 7（默认从数组末尾开始往头部遍历←，返回元素在数组中第一次匹配到的下标）
crr.lastIndexOf("9",4); // 3（从下标4开始往数组头部遍历←，返回元素在数组中第一次匹配到的下标）

// 12. sort()：默认情况下sort()方法没有传比较函数的话，默认按字母升序进行排序。
crr.sort(); // ["1", "2", "3", "4", "5", "8", "9", "9"]
crr; // ["1", "2", "3", "4", "5", "8", "9", "9"]
// 注意：如果元素不是字符串的话，会先调用toString()方法将元素转化为字符串的Unicode(万国码)位点，然后再比较字符。所以用默认方法排序数据是有问题的
crr = [20,10,2,1,3];
crr.sort(); // [1, 10, 2, 20, 3]
crr.sort(function(a,b){return a-b;}); // [1, 2, 3, 10, 20]（升序排序）
crr.sort(function(a,b){return b-a;}); // [20, 10, 3, 2, 1]（降序排序）

// 13. forEach()：从头至尾遍历数组
crr.forEach((item, index)=>{item+=3;console.log(index+" - "+item);});

// 14. map()：调用的数组的每一个元素传递给指定的函数，并返回一个新数组，不修改原数组
crr = [1, 2, 3, 10, 20];
let drr = crr.map(function(x){return x*x});
drr; // [1, 4, 9, 100, 400]

// 15. filter()：过滤功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组，不修改原数组（可以巧妙的用来去重）
crr = ["3","4","8","9","1","2","5","9"];
crr.filter(function(item,index,self){return self.indexOf(item)==index;}); // ["3", "4", "8", "9", "1", "2", "5"]

// 16. every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true
//      some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true
crr.every(item=>item>0); // true
crr.every(item=>item>5); // false
crr.some(item=>item>5);  // true

// 17. reduce()：数组从第一项开始，逐个遍历到最后
//     reduceRight()：数组从最后一项开始，逐个遍历到头部
crr = ["1","2","5","9"];
crr.reduce(function(a,b){return a+b;}); // "1259"
crr = [1,2,5,9];
crr.reduce(function(a,b){return a+b;});      // 17
crr.reduceRight(function(a,b){return a+b;}); // 17

// 18. 数组深拷贝（以下方法能够满足99%的深拷贝，但是对与RegExp类型和Function类型则无法完全满足，而且不支持有循环引用的对象）
// 普通数组使用浅拷贝即可，对象数组需要使用深拷贝
err = JSON.parse(JSON.stringify(crr));
```

## 二、对象

```javascript
let obj = {"name":"lxj","hobby":"sleep","address":"software park"};
obj; // {name: "lxj", hobby: "sleep", address: "software park"}
obj.name; // "lxj"
obj["name"]; // "lxj"

// 遍历
for(let item in obj){if(obj.hasOwnProperty(item)) {console.log(item);console.log(obj[item]);}}

// 删除delete
delete obj["name"]; // true
obj; // {hobby: "sleep", address: "software park"}

// 合并对象（拷贝源对象（source|obj）的所有可枚举属性给目标对象（target|obj1），并返回obj1，obj1中相同的key（name）会被obj覆盖掉）
// 拷贝的属性是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（enumerable: false）
let obj1 = {"name":"Tom","tel":"10086","from":"china"}
Object.assign(obj1, obj); // {name: "lxj", tel: "10086", from: "china", hobby: "sleep", address: "software park"}
obj1; // {name: "lxj", tel: "10086", from: "china", hobby: "sleep", address: "software park"}

// 清空
Object.clearAll(obj1);
obj1; // {}

// 查看对象所有的key
Object.keys(obj); // ["name", "hobby", "address"]
// 该方法可以用来查看一个对象内元素的数量，同理也可判断一个对象是否为空
if (Object.keys(obj).length === 0) {
    return false; // 如果为空,返回false
}
// 另外，还可以通过JSON自带的stringify()方法来判断对象是否为空
if (JSON.stringify(data) === '{}') {
    return false; // 如果为空,返回false
}

// 查看对象的所有value
Object.values(obj); // ["lxj", "sleep", "software park"]
```

