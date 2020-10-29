# ES6 
### let命令

> 用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效,相当于局部变量  

```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
``` 
只在代码块生效
* 不存在变量提升的情况
```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
var命令会发生“变量提升”现象，即变量可以在声明之前使用，值为undefined。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。为了纠正这种现象，let命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。
* 暂时性死区
```
var tmp = 123;

if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
* 不允许重复声明 
```
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}
```
let不允许在相同作用域内，重复声明同一个变量。
*   

### const
> const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。


### 字符串扩展

> ES6为字符串新增了几个新的API
>  - includes() ：返回布尔值，表示是否找到了参数字符串。
>  - startsWith() ：返回布尔值，表示参数字符串是否在原字符串的头部。
>  - endsWith() ：返回布尔值，表示参数字符串是否在原字符串的尾部。

``` js
//试验一下
"hello".includes("llo");
true

"hello".startsWith("h");
true

"hello".endsWith("o");
true
```

> 字符串模板
> ES6中提供了`来作为字符串模板标记。在两个之间的部分都会被作为字符串的值，不管你任意换行，甚至加入js脚本
``` js
let j = `aaa
bbb
ccc
ddd
eee
fff`; 
```

### 解构表达式
> 数组解构
```js
//例如数组,es6之前只能通过下标来访问,es6可以这样
let arr = [1,2,3];
const [x,y,z] = arr;
console.log(x,y,z);
```

> 对象解构
```js
let person = {
  id : 1,
  age : 18,
  name : '小明'
}

let {id,age,name} = person;

console.log(id,age,name);
```

### 函数优化
> 函数参数默认值
```js
//在es6之前我们无法给一个函数参数默认值,只能通过一些方法来判断
function add (a,b) {
  b = b || 1;
  return a + b;
}

//es6之后可以这么写
function add (a, b = 10) {
  return a + b;
}

//传入一个参数
console.log(add(10));

//传入两个参数
console.log(add(10,1));
```
> 箭头函数
```js
//es6函数简写方式
//一个参数时
var print = function (a) {
  console.log(a);
}

//简写
var print = (a) => console.log(a);

//多个参数
var println = function (a, b) {
  console.log(a + b);
}

//简写
var println = (a, b) => console.log(a + b);
```

> 对象的函数属性简写
```js
let person = {
  name : "小明",
  run : function(a) {
    console.log(this.name + "run" + a);
  }, //原先的方式
  eat : (a) => console.log(person.name + "再吃"),  //箭头函数内不能用this!
  //简写版
  eat2(b) {
    console.log(this.name + "eat2" + b);
  }
}

person.run("run");
person.eat("蛋糕");
person.eat2("苹果");
```

> 箭头函数结合解构表达式
```js
const person = {
  name:"jack",
  age:21,
  language: ['java','js','css'] 
}

function hello(person) {
  console.log("hello," + person.name) 
}

//如果用箭头函数替代方法
var hello = ({name}) => console.log("hello" + name);

//调用
hello(person);
```

### map和reduce
> 数组中新增了map方法
```js
//类似于stream中的map
var arr = [1,2,3,4,5];

arr = arr.map(s => s + 1);

console.log(arr);
```
> 数组新增了reduce方法
> reduce() ：接收一个函数（必须）和一个初始值（可选）。
> 第一个参数（函数）接收两个参数：
>   > • 第一个参数是上一次reduce处理的结果 
>   > • 第二个参数是数组中要处理的下一个元素
> 
> reduce() 会从左到右依次把数组中的元素用reduce处理，并把处理的结果作为下次reduce的第
一个参数。如果是第一次，会把前两个元素作为计算参数，或者把用户指定的初始值作为起始参
数
```js
const list = [1,2,3,4,5];
//没有初始值
console.log(list.reduce((a,b) => a + b));

//有初始值
console.log(list.reduce((a,b) => a + b, 100));
```

### 对象扩展
> es6给对象扩展了很多方法如
> > keys(obj)：获取对象的所有key形成的数组
> > values(obj)：获取对象的所有value形成的数组
> > entries(obj)：获取对象的所有key和value形成的二维数组。格式： [[k1,v1],[k2,v2],...]
> > assign(dest, ...src) ：将多个src对象的值 拷贝到 dest中（浅拷贝）。
```js
Object.entries(obj);

Object.keys(obj);

Object.values(obj);

let p2 = {};

Object.assign(p2, obj); //将obj拷贝到p2
```

### 数组扩展
> ES6给数组新增了许多方法:
> > find(callback)：数组实例的find方法，用于找出第一个符合条件的数组成员。它的参
数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为
true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。
> >  findIndex(callback)：数组实例的findIndex方法的用法与find方法非常类似，返回第
一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。
> > includes(数组元素)：与find类似，如果匹配到元素，则返回true，代表找到了。

```js
let arr = [1,2,3,4,5,6,7,8,9];

//找出满足函数的成员 例:下标为5的成员
arr.find((item,index,arr)=> index == 5);

//找出满足函数的成员的位置 例:5的位置
arr.findIndex((item,index,arr)=> item == 5);

//与find类似,如果匹配到元素,代表找到了返回true
arr.includes(5);
```





