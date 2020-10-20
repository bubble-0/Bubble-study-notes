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
> "hello".includes("llo");
true

> "hello".startsWith("h");
true

> "hello".endsWith("o");
true
```



