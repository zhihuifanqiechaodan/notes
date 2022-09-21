先来看一段代码

```
var foo = function () {
    console.log('foo1');
}
foo();  // foo1

var foo = function () {
    console.log('foo2');
}
foo(); // foo2
```
然后来看这段代码

```
function foo() {
    console.log('foo1');
}
foo();  // foo2

function foo() {
    console.log('foo2');
}
foo(); // foo2
```
打印的结果却是两个 foo2。

**以上两段代码,第一个列子中的变量提升和第二个列子中的函数提升,当两者代码混入的时候,你还能够正确的分辨出执行顺序么?**

## 让我们来看一个混入的小例子再来详细了解

```
console.log(a) // undefined
var a = 'hello JS' 

/* 这里我们要考虑一个问题? 在我们声明a之前为什么输出a不会报错呢？*/

num = 6;
num++;
var num;
console.log(num) // 7 这里考虑一个问题? 为什么给一个还没有声明的变量赋值会不报错呢

function hoistFunction() {
    foo();
    function foo() {        
        console.log('running...')    
    }
}
hoistFunction(); // running...  这里考虑一个问题? 为什么foo执行的时候没有报错呢
```
### 分析原因
S引擎会在正式执行代码之前进行一次”预编译“，预编译简单理解就是在内存中开辟一些空间，存放一些变量和函数。具体步骤如下（browser）：
* 页面创建GO全局对象（Global Object）对象（window对象）。
* 加载第一个脚本文件
* 脚本加载完毕后，进行语法分析。
* **开始预编译**
  
```
* 查找函数声明，作为GO属性，值赋予函数体（函数声明优先）
* 查找变量声明，作为GO属性，值赋予undefined
GO/window = {
    //页面加载创建GO同时，创建了document、navigator、screen等等属性，此处省略
    a: undefined,
    num: undefined，
    hoistFunction: function(y){
        foo();
        function foo() {        
        console.log('running...')    
        }
    }
}
```
* 解释执行代码（直到执行函数hoistFunction，该部分也被叫做词法分析）
```
* 创建AO活动对象（Active Object）
* 查找形参和变量声明，值赋予undefined
* 实参值赋给形参
* 查找函数声明，值赋给函数体
* 解释执行函数中的代码
GO/window = {
    //变量随着执行流得到初始化
    a: hello JS,
    num: 6, // num++ num:7
    hoistFunction: function(y){
        foo();
        function foo() {        
        console.log('running...')    
        }
    }
}
```
* 第一个脚本文件执行完毕，加载第二个脚本文件
* 第二个文件加载完毕后，进行语法分析
* 开始预编译
* 重复预编译步骤 ....

**预解析机制使得变量提升（Hoisting)，从字面上理解就是变量和函数的声明会移动到函数或者全局代码的开头位置(他所在的作用域)。我们再来分析一下上面的小例子加深一下理解。**

```
console.log(a) // 执行之前，变量提升作为window的属性， 值被设置为undefined
var a = 'hello JS' 

/* JavaScript 仅提升声明，而不提升初始化 */

num = 6;
num++;
var num;
console.log(num) // 变量提升 值为undefined的num赋值为6，再自增 => 7

function hoistFunction() {
    foo();
    function foo() {        
        console.log('running...')    
    }
}
hoistFunction(); // 函数声明提升，可以在函数体之前执行
```
**注： JS并不存在真正的预编译，var与function的提升实际是在语法分析阶段就处理好的。而且JS的预编译是以一个脚本文件为块的。一个脚本文件进行一次预编译，而不是全文编译完成再进行”预编译”的**

## 什么是提升（Hosting）？
引擎会在解释JavaScript代码之前首先对其进行编译，编译过程中的一部分工作就是找到所有的声明，并用合适的作用域将他们关联起来，这也正是词法作用域的核心内容。

### 变量提升
变量声明的提升是以变量所处的第一层词法作用域为“单位”的，即全局作用域中声明的变量会提升至全局最顶层，函数内声明的变量只会提升至该函数作用域最顶层。那么开始的一段代码经过预编译则变为

```

var a;
console.log(a); // undefined
a = "a";
var foo = () => {
    var a; // 全局变量会被局部作用域中的同名变量覆盖
    console.log(a); // undefined
    a = "a1";
}
foo();
```
ES6新增了let和const关键字，使得js也有了“块”级作用域，而且使用let和const 声明的变量和函数是不存在提升现象的，比较有利于我们养成良好的编程习惯。
### 函数提升
有了上面变量提升的说明，函数提升理解起来就比较容易了，但较之变量提升，函数的提升还是有区别的。

```

console.log(foo1); // [Function: foo1]
foo1(); // foo1
console.log(foo2); // undefined
foo2(); // TypeError: foo2 is not a function
function foo1 () {
	console.log("foo1");
};
var foo2 = function () {
	console.log("foo2");
}
// 这里可能会有人有疑问? 为foo2会报错,不同样也是声明?
// foo2在这里是一个函数表达式且不会被提升
```
函数提升只会提升函数声明，而不会提升函数表达式。

**最后以一个小列子结尾,看看你到底有没有了解函数提升以及变量提升**

```
var a = 1;
function foo() {
    a = 10;
    console.log(a);
    return;
    function a() {};
}
foo();
console.log(a);
```
上面的小例子你答对了没有?

**上面的代码块经过预编译后可以看做如下形式（只分析foo方法内部情况）：**

```

var a = 1; // 定义一个全局变量 a
function foo() {
    // 首先提升函数声明function a () {}到函数作用域顶端
    // 然后function a () {}等同于 var a =  function() {};最终形式如下
    var a = function () {}; // 定义局部变量 a 并赋值。
    a = 10; // 修改局部变量 a 的值，并不会影响全局变量 a
    console.log(a); // 打印局部变量 a 的值：10
    return;
}
foo();
console.log(a); // 打印全局变量 a 的值：1
```