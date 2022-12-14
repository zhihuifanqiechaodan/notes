ECMAScript 6.0（简称ES6），作为下一代JavaScript的语言标准正式发布于2015 年 6 月，至今已经发布4年多了，但是因为蕴含的语法之广，完全消化需要一定的时间，这里我总结了自己在学习ES6过程中的一些知识点，以及ES6以后新语法的知识点，使用场景，希望对各位有所帮助.

**俗话说得好,好记性不如烂笔头,自己多敲一遍代码记得快,印象也深刻**
## 第一章 严格模式
严格模式是ES5引入, 严格模式主要有以下限制;
* 变量必须声明后在使用
* 函数的参数不能有同名属性, 否则报错
* 不能使用with语句 (说实话我基本没用过)
* 不能对只读属性赋值, 否则报错
* 不能使用前缀0表示八进制数,否则报错 (说实话我基本没用过)
* 不能删除不可删除的数据, 否则报错
* 不能删除变量delete prop, 会报错, 只能删除属性delete global[prop]
* eval不会在它的外层作用域引入变量
* eval和arguments不能被重新赋值
* arguments不会自动反映函数参数的变化
* 不能使用arguments.caller (说实话我基本没用过)
* 不能使用arguments.callee (说实话我基本没用过)
* 禁止this指向全局对象
* 不能使用fn.caller和fn.arguments获取函数调用的堆栈 (说实话我基本没用过)
* 增加了保留字（比如protected、static和interface） 
## 第二章 let 和 const命令
基本用法跟 **es5** 的 **var** 一样,但是 **let** 声明不存在变量提升现象

**var** 声明存在变量提升现象, **let**和**const**则不会有这种情况
#### 暂时性死区  简称 TDZ 
只要块级作用域内存在let命令, 它所声明的变量就"绑定"(binding)这个区域,不再受外部的影响

```
var num = 123
if (true) {
    num = 'abc' // Cannot access 'num' before initialization
    let num
}
```
ES6明确规定, 如果区块中存在let和const命令, 这个区块对这些命令声明的变量,从一开始就形成了封闭作用域,凡是在声明之前就使用这些变量,就会报错

暂时性死去的本质就是, 只要一进入当前作用域,所有使用的变量就已经存在了, 但是不可获取, 只有等声明变量的那一行代码出现,才可以获取和使用该变量
#### 不允许重复声明
let 不允许在相同的作用域内, 重复声明同一个变量

```
// 报错
function fn() {
    let a = 1
    var a = 2
}

// 报错
function fn() {
    let a = 1
    let a = 2
}
```
因此，不能在函数内部重新声明参数。

```
function fn(arg) {
  let arg; // 报错
}

function fn(arg) {
  {
    let arg; // 不报错
  }
}
```
#### 块级作用域
为什么需要快作用域

ES5只有全局作用域和函数作用域, 没有块作用域, 这种情况带来了很多不合理的场景

第一种场景, 内层变量可能会覆盖外层变量

```
var num = 123
function fn() {
    console.log(num)
    if (false) { // 内部声明变量覆盖了全局, 由于是var声明出现变量提升上面的num值为undefined
        var num = 456  
    }
}
fn() // undefined
```
第二种场景, 用来计数的循环变量泄露成为全局变量

```
var s = 'hello';
for (var i = 0; i < s.length; i++) {  // 这里var声明的变量自动挂载到了全局
  console.log(s[i]); 
}
console.log(i); // 5
```
**ES6 的块级作用域**

```
function fn() {
    let n = 5
    if (true) {
        let n = 10
    }
    console.log(n)
}
fn() // 5
```
ES6 允许块级作用域的任意嵌套。

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE / 立即调用的函数表达式）不再必要了。

```
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```
**块级作用域不返回值，除非t是全局变量。**
### const命令
const声明一个只读的常量。

**const除了以下两点与let不同外，其他特性均与let相同:**

```
1. const一旦声明变量，就必须立即初始化，不能留到以后赋值。
2. 一旦声明，常量的值就不能改变。
```
#### 本质
const限定的是赋值行为。

```
const a = 1;
a = 2; // 报错

const arr = [];
arr.push(1) // [1] 
//在声明引用型数据为常量时，const保存的是变量的指针，只要保证指针不变就不会保存。下面的行为就会报错

arr = []; // 报错 因为是赋值行为。变量arr保存的指针改变了。
```
### 顶层对象属性与全局变量
顶层对象, 在浏览器环境指的是window对象, 在Node环境指的是global对象, ES5之中,顶层对象的属性与全局变量是等价的

```
window.a = 1
a // 1
a = 2;
window.a // 2
```
顶层对象的属性与全局变量挂钩, 被认为是JavaScript语言最大的设计败笔之一

**为了解决这个问题, ES6引入的let cosnt class声明的全局变量不再属于顶层对象的属性**

而同时为了向下兼容, var和function声明的变量依然属于全局对象的属性

```
var a = 1;
window.a // 1

let b = 1;
window.b // undefined
```
## 第三章 变量的解构赋值
#### 基本用法
ES6允许按照一定模式, 从数组和对象中提取值, 对变量进行赋值, 这被称为解构(Destructuring)

ES5一次声明多个变量

```
var a = 1,
    b = 2,
    c = 3;
```
ES6一次声明多个变量

```
let [a, b, c] = [1, 2, 3]
// a = 1
// b = 2
// c = 3
```
**本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。**

```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
如果解构不成功，变量的值就等于undefined。

```
let [foo] = [];
let [bar, foo] = [1];
// foo 都是undefined
```
另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。

```
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4

//上面两个例子，都属于不完全解构，但是可以成功。
```
如果等号的右边不是数组，那么将会报错。

```
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
#### 默认值
解构赋值允许指定默认值。

```
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```
注意，ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，如果一个数组成员不严格等于undefined，默认值是不会生效的。

```
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```
如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

```
function f() {
  console.log('aaa');
}

let [x = f()] = [1]; // [1]
//等价于
let x;
if ([1][0] === undefined) {
  x = f();
} else {
  x = [1][0];
}
```
默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

```
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError
//上面最后一个表达式之所以会报错，是因为x用到默认值y时，y还没有声明
```
#### 对象的解构赋值
解构不仅可以用于数组, 还可以用于对象

```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
**对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。**

```
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```
如果变量名与属性名不一致，必须写成下面这样。

```
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
实际上说明，对象的解构赋值是下面形式的简写

```
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。

```
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```
与数组一样，解构也可以用于嵌套结构的对象。

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```
对象的解构也可以指定默认值。

```
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x: y = 3} = {};
y // 3

var {x: y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"
```
**默认值生效的条件是，对象的属性值严格等于undefined。**

```
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```
如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。

```
// 报错
let {foo: {bar}} = {baz: 'baz'};
//等号左边对象的foo属性，对应一个子对象。该子对象的bar属性，解构时会报错。原因很简单，因为foo这时等于undefined，再取子属性就会报错，
```
由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。

```
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```
#### 字符串的解构赋值

```
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。

```
let {length : len} = 'hello';
len // 5
```
#### 函数参数的解构赋值

```
function add([a,b]){
  return a + b;
}
add([2,3])//5
```
函数参数的解构也可以使用默认值。

```
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```
**注意，下面的写法会得到不一样的结果。**

```
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```
上面代码是为函数move的参数指定默认值，而不是为变量x和y指定默认值，所以会得到与前一种写法不同的结果。
#### 数值和布尔值的解构赋值
解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
#### 圆括号
解构赋值虽然很方便，但是解析起来并不容易。对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。

由此带来的问题是，如果模式中出现圆括号怎么处理。ES6 的规则是，只要有可能导致解构的歧义，就不得使用圆括号。

但是，这条规则实际上不那么容易辨别，处理起来相当麻烦。因此，建议只要有可能，就不要在模式中放置圆括号。

**不能使用圆括号的情况**以下三种解构赋值不得使用圆括号。

```
1) 变量声明语句
// 全部报错
let [(a)] = [1];

let {x: (c)} = {};
let ({x: c}) = {};
let {(x: c)} = {};
let {(x): c} = {};

let { o: ({ p: p }) } = { o: { p: 2 } };

2)函数参数---函数参数也属于变量声明，因此不能带有圆括号。
// 报错
function f([(z)]) { return z; }
// 报错
function f([z,(x)]) { return x; }

3) 赋值语句的模式
// 全部报错
({ p: a }) = { p: 42 };
([a]) = [5];
//上面代码将整个模式放在圆括号之中，导致报错。
// 报错
[({ p: a }), { x: c }] = [{}, {}];
```
**可以使用圆括号的情况 **
可以使用圆括号的情况只有一种：赋值语句的非模式部分，可以使用圆括号。

```
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```
上面三行语句都可以正确执行，因为首先它们都是赋值语句，而不是声明语句；其次它们的圆括号都不属于模式的一部分。第一行语句中，模式是取数组的第一个成员，跟圆括号无关；第二行语句中，模式是p，而不是d；第三行语句与第一行语句的性质一致。

**用途**

```
1. 除了可以一次定义多个变量
2. 还可以让函数返回多个值
3. 可以方便地让函数的参数跟值对应起来
4. 提取json数据
5. 函数参数的默认值
```
## 第四章 字符串扩展
**includes()、startsWith()、endsWith()**

传统上，JavaScript 只有indexOf方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6 又提供了三种新方法。
- includes()：返回布尔值，表示是否找到了参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```
这三个方法都支持第二个参数，表示开始搜索的位置。

```
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```
上面代码表示，使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。
#### repeat
repeat方法返回一个新字符串，表示将原字符串重复n次。

```
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```
- 参数如果是小数，会被向下取整。
- 如果repeat的参数是负数或者Infinity，会报错。
- 0 到-1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到-1 之间的小数，取整以后等于-0，repeat视同为 0。
- 参数NaN等同于 0。
- 如果repeat的参数是字符串，则会先转换成数字。
#### padStart()、padEnd()
ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。padStart()用于头部补全，padEnd()用于尾部补全。

```
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```
- 如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
- 如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。
- 如果省略第二个参数，默认使用空格补全长度。
#### 模板字符串 (这个我项目中经常用到)
es5的字符串模板输出通常是使用+拼接。

这样的缺点显然易见：字符串拼接内容多的时候，过于混乱，易出错。

而ES6 引入了模板字符串解决这个问题。

```
var name = "番茄",trait = "帅气";
//es5dDdD
var str = "他叫"+name+",人非常"+trait+",说话又好听";

//es6
var str2 = `他叫 ${name} ,人非常 ${trait} ,说话又好听`;
```
模板字符串是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

- 如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。
- 如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。
- 模板字符串中嵌入变量，需要将变量名写在${}之中。
- 大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。
- 模板字符串之中还能调用函数。
- 如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的toString方法。
- 如果模板字符串中的变量没有声明，将报错。
#### 标签模板
模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为“标签模板”功能。

```
alert`123`
// 等同于
alert(123)
```
标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。

**如果模板字符里面有变量，就不是简单的调用了，而是会将模板字符串先处理成多个参数，再调用函数。**

```
let a = 5;
let b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```
## 第五章 数值的扩展
**Number.isFinite()、Number.isNaN()**

ES6 在Number对象上，新提供了Number.isFinite()和Number.isNaN()两个方法。

Number.isFinite()用来检查一个数值是否为有限的（finite）。

```
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```
Number.isNaN()用来检查一个值是否为NaN。

和全局函数 isNaN() 相比，该方法不会强制将参数转换成数字，只有在参数是真正的数字类型，且值为 NaN 的时候才会返回 true。

```
Number.isNaN(NaN);        // true
Number.isNaN(Number.NaN); // true
Number.isNaN(0 / 0)       // true

// 下面这几个如果使用全局的 isNaN() 时，会返回 true。
Number.isNaN("NaN");      // false，字符串 "NaN" 不会被隐式转换成数字 NaN。
Number.isNaN(undefined);  // false
Number.isNaN({});         // false
Number.isNaN("blabla");   // false

// 下面的都返回 false
Number.isNaN(true);
Number.isNaN(null);
Number.isNaN(37);
Number.isNaN("37");
Number.isNaN("37.37");
Number.isNaN("");
Number.isNaN(" ");
```
#### Number.parseInt()、Number.parseFloat()
ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。

```
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```
这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。

```
Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```
#### Number.isInteger()
Number.isInteger()用来判断一个值是否为整数。需要注意的是，在 JavaScript 内部，整数和浮点数是同样的储存方法，所以 3 和 3.0 被视为同一个值。

```
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```
#### Math 对象的扩展 
ES6 在 Math 对象上新增了 17 个与数学相关的方法。所有这些方法都是静态方法，只能在 Math 对象上调用。

**Math.trunc()**  
Math.trunc方法用于去除一个数的小数部分，返回整数部分。

```
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```
- 对于非数值，Math.trunc内部使用Number方法将其先转为数值。
- 对于空值和无法截取整数的值，返回NaN。

**Math.sign()**
Math.sign方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

它会返回五种值。

- 参数为正数，返回+1；
- 参数为负数，返回-1；
- 参数为 0，返回0；
- 参数为-0，返回-0;
- 其他值，返回NaN。

```
Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN

Math.sign('')  // 0
Math.sign(true)  // +1
Math.sign(false)  // 0
Math.sign(null)  // 0
Math.sign('9')  // +1
Math.sign('foo')  // NaN
Math.sign()  // NaN
Math.sign(undefined)  // NaN
```
**Math.cbrt()**
Math.cbrt方法用于计算一个数的立方根。

对于非数值，Math.cbrt方法内部也是先使用Number方法将其转为数值。

```
Math.cbrt(-1) // -1
Math.cbrt(0)  // 0
Math.cbrt(1)  // 1
Math.cbrt(2)  // 1.2599210498948734
```
**Math.hypot()**
Math.hypot方法返回所有参数的平方和的平方根。

```
Math.hypot(3, 4);        // 5
Math.hypot(3, 4, 5);     // 7.0710678118654755
Math.hypot();            // 0
Math.hypot(NaN);         // NaN
Math.hypot(3, 4, 'foo'); // NaN
Math.hypot(3, 4, '5');   // 7.0710678118654755
Math.hypot(-3);          // 3
```
**指数运算符**
ES2016 新增了一个指数运算符（**）。

```
2 ** 2 // 4
2 ** 3 // 8
```
指数运算符可以与等号结合，形成一个新的赋值运算符（**=）。

```
let a = 1.5;
a **= 2;
// 等同于 a = a * a;

let b = 4;
b **= 3;
// 等同于 b = b * b * b;
```
## 第六章 函数的扩展
#### 基本用法
ES6 之前，不能直接为函数的参数指定默认值，只能采用变通的方法。

```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```
#### 与解构赋值默认值结合使用

```
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({x: 1}) // 1 5
foo({x: 1, y: 2}) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
```
#### 参数默认值的位置 
通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了哪些参数。如果非尾部的参数设置默认值，实际上这个参数是没法省略的。

```
// 例一
function f(x = 1, y) {
  return [x, y];
}

f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1]

// 例二
function f(x, y = 5, z) {
  return [x, y, z];
}

f() // [undefined, 5, undefined]
f(1) // [1, 5, undefined]
f(1, ,2) // 报错
f(1, undefined, 2) // [1, 5, 2]
```
#### 函数的 length 属性
指定了默认值以后，函数的length属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，length属性将失真。

```
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```
- fn.length 返回形参个数
- arguments.length 返回实参个数
#### 作用域
一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。

```
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```
上面代码中，参数y的默认值等于变量x。调用函数f时，参数形成一个单独的作用域。在这个作用域里面，默认值变量x指向第一个参数x，而不是全局变量x，所以输出是2。

```
let x = 1;

function f(y = x) {
  let x = 2;
  console.log(y);
}

f() // 1
```
上面代码中，函数f调用时，参数y = x形成一个单独的作用域。这个作用域里面，变量x本身没有定义，所以指向外层的全局变量x。函数调用时，函数体内部的局部变量x影响不到默认值变量x。

```
var x = 1;

function foo(x = x) {
  // ...
}

foo() // ReferenceError: x is not defined
```
上面代码中，参数x = x形成一个单独作用域。实际执行的是let x = x，由于暂时性死区的原因，这行代码会报错”x 未定义“。

```
var x = 1;
function foo(x, y = function() { x = 2; }) {
  var x = 3;
  y();
  console.log(x);
}

foo()//3
x//1
```
如果将var x = 3的var去除，函数foo的内部变量x就指向第一个参数x，与匿名函数内部的x是一致的，所以最后输出的就是2，而外层的全局变量x依然不受影响。

```
var x = 1;
function foo(x, y = function() { x = 2; }) {
  x = 3;
  y();
  console.log(x);
}

foo() // 2
x // 1
```
#### rest 参数 
ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```
arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组。rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。下面是一个利用 rest 参数改写数组push方法的例子。

```
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)
```
**注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。**

```
// 报错
function f(a, ...b, c) {
  // ...
}
```
函数的length属性，不包括 rest 参数。

```
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```
#### 严格模式
从 ES5 开始，函数内部可以设定为严格模式。

ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错
#### name 属性 
返回函数名。

```
function foo() {}
foo.name // "foo"

var f = function () {}; // "f"
```
#### 箭头函数
* 基本用法

```
ES6 允许使用“箭头”（=>）定义函数。
var f = v => v;
//上面的箭头函数等同于
var f = function(v) {
  return v;
};

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用return语句返回。
var sum = (num1, num2) => { return num1 + num2; }

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```
* 使用注意点
箭头函数有几个使用注意点。

1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
4. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。



this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。

箭头函数转成 ES5 的代码如下。

```
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
//转换后的 ES5 版本清楚地说明了，箭头函数里面根本没有自己的this，而是引用外层的this。
```
由于箭头函数没有自己的this，所以当然也就不能用call()、apply()、bind()这些方法去改变this的指向。
#### 函数参数的尾逗号
ES2017 允许函数的最后一个参数有尾逗号（trailing comma）。

这样的规定也使得，函数参数与数组和对象的尾逗号规则，保持一致了。

```
function clownsEverywhere(
  param1,
  param2,
) { /* ... */ }

clownsEverywhere(
  'foo',
  'bar',
);
```
## 第七章 数组的扩展
#### 扩展运算符
扩展运算符（spread）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```
该运算符将一个数组，变为参数序列。

扩展运算符后面还可以放置表达式。

```
var x = 1
const arr = [...(x > 0 ? ['a'] : [], 'b')]
console.log(arr) // ['a', 'b']
```
**如果扩展运算符后面是一个空数组，则不产生任何效果。**

```
var arr = [...[], 1]
console.log(arr) // [1]
```
* 替代数组的 apply 方法 (将数组当作参数传入函数中)
由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。

```
// ES5 的写法
function fn(x, y, z) {
    // ...
}
var arr = [1, 2, 3]
fn.apply(null, arr)

// ES6 的写法
function fn(x, y, z) {
    // ...
}
var arr = [1, 2, 3]
fn(...arr)
```
es5的时候大家的利用Math.max拿数组最大值

```
//es5
Math.max.apply(null,[1,5,2,8]) // 8
//es6
Math.max(...[1,5,2,8]) // 8
//上面两种方法等同于
Math.max(1,5,2,8)
```
* 扩展运算符的应用

```
// 复制数组
// ES5 的方法
var arr = [1, 2, 3]
var arr1 = arr.concat()
arr1[arr1.length] = 5
console.log(arr) // [1, 2, 3]
console.log(arr1) // [1, 2, 3, 5]

// 扩展运算符提供了复制数组的简便写法。
// 方法一
var arr = [1, 2, 3]
var arr1 = [...arr]
console.log(arr1) // [1, 2, 3]

// 方法二
var arr = [1, 2, 3]
var [...arr1] = arr
console.log(arr1) // [1, 2, 3]
```
* 合并数组
扩展运算符提供了数组合并的新写法。

```
// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]

var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1 = arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
* 与解构赋值结合

扩展运算符可以与解构赋值结合起来，用于生成数组。
```
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []

// 错误用法
const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```
* 字符串
扩展运算符还可以将字符串转为真正的数组

```
[...'hello']
// ["h", "e", "l", "l", "o"]
```
* 将类数组转为数组

```
// 平时我们获取dom节点的数组是一个类数组, 无法使用数组的方法
let nodeList = document.querySelectorAll('div');
// 通过扩展云算法转换为数组
let array = [...nodeList];
```
* Array.from() 

Array.from方法用于将两类对象转为真正的数组

```
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).forEach(function (p) {
  console.log(p);
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```
**参数：**

- 第一个参数：一个类数组对象，用于转为真正的数组
- 第二个参数：类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组。
- 第三个参数：如果map函数里面用到了this关键字，还可以传入Array.from的第三个参数，用来绑定this。
****
* Array.of()

Array.of方法用于将一组值，转换为数组。

这个方法的主要目的，是弥补数组构造函数Array()的不足。因为参数个数的不同，会导致Array()的行为有差异。

```
//Array
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]

Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```
* 数组实例的 copyWithin()

```
数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

它接受三个参数。

- target（必需）：从该位置开始替换数据。
- start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示倒数。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

这三个参数都应该是数值，如果不是，会自动转为数值。

[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]    // 改变第一个数字, 值从第三个值赋值
```
* 数组实例的 find() 和 findIndex()

```
// find()
数组实例的find方法，用于找出第一个符合条件的数组成员。
它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，
然后返回该成员。如果没有符合条件的成员，则返回undefined。

[1, 4, -5, 10].find((n) => n < 0)
// -5
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
//find方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。

// findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，
如果所有成员都不符合条件，则返回-1。
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```
我自己一般使用find方法比较多

**这两个方法都可以接受第二个参数，用来绑定回调函数的this对象。**
* 数组实例的 fill()

```
fill方法使用给定值，填充一个数组。
fill方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
fill方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。
var arr = [1, 2, 3]
arr.fill(6, 1, 2) // [1, 6, 3]
arr.fill(6, 1) // [1, 6, 6]
```
* for...of

```
es6引入的作为遍历所有数据结构的统一的方法。

一个数据结构只要部署了Symbol.iterator属性，就被视为具有 iterator 接口，
就可以用for...of循环遍历它的成员。也就是说，for...of循环内部调用的是数据结构的Symbol.iterator方。
```
* 数组实例的 entries()，keys() 和 values()

entries()，keys()和values()——用于遍历数组。它们都返回一个遍历器对象，可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。
```
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```
* 数组实例的 includes() 

方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。ES2016 引入了该方法。
```
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true
```
该方法的第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。

没有该方法之前，我们通常使用数组的indexOf方法，检查是否包含某个值。

indexOf方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相等运算符（===）进行判断，这会导致对NaN的误判。
* 数组的空位
数组的空位指，数组的某一个位置没有任何值。

注意，空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值，in运算符可以说明这一点。

```
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```
ES5 对空位的处理，已经很不一致了，大多数情况下会忽略空位。

- forEach(), filter(), reduce(), every() 和some()都会跳过空位。
- map()会跳过空位，但会保留这个值
- join()和toString()会将空位视为undefined，而undefined和null会被处理成空字符串。



ES6 则是明确将空位转为undefined。

Array.from方法会将数组的空位，转为undefined，也就是说，这个方法不会忽略空位。

Array.from方法会将数组的空位，转为undefined，也就是说，这个方法不会忽略空位。

```
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]
```
扩展运算符（...）也会将空位转为undefined。

```
[...['a',,'b']]
// [ "a", undefined, "b" ]
```
copyWithin()会连空位一起拷贝。

```
[,'a','b',,].copyWithin(2,0) // [,"a",,"a"]
```
fill()会将空位视为正常的数组位置。

```
new Array(3).fill('a') // ["a","a","a"]
```
for...of循环也会遍历空位。
```
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```
上面代码中，数组arr有两个空位，for...of并没有忽略它们。如果改成map方法遍历，空位是会跳过的。

entries()、keys()、values()、find()和findIndex()会将空位处理成undefined。

```
// entries()
[...[,'a'].entries()] // [[0,undefined], [1,"a"]]

// keys()
[...[,'a'].keys()] // [0,1]

// values()
[...[,'a'].values()] // [undefined,"a"]

// find()
[,'a'].find(x => true) // undefined

// findIndex()
[,'a'].findIndex(x => true) // 0
```
**由于空位的处理规则非常不统一，所以建议避免出现空位。**
## 第八章 对象的扩展
* 属性的简洁表示法

ES6 允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。
```
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}

// 等同于
const baz = {foo: foo};
```
方法也可以简写。

```
const o = {
  method() {
    return "Hello!";
  }
};

// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};
```
* 属性名表达式

JavaScript 定义对象的属性，有两种方法。

```
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;
```
但是，如果使用字面量方式定义对象（使用大括号），在 ES5 中只能使用方法一（标识符）定义属性。

```
var obj = {
  foo: true,
  abc: 123
};
```
ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};
```
表达式还可以用于定义方法名。

```
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi
```
**注意，属性名表达式与简洁表示法，不能同时使用，会报错。**

```
// 报错
const foo = 'bar';
const bar = 'abc';
const baz = { [foo] };

// 正确
const foo = 'bar';
const baz = { [foo]: 'abc'};
```
* Object.is()

ES5 比较两个值是否相等，只有两个运算符：相等运算符（==）和严格相等运算符（===）。它们都有缺点，前者会自动转换数据类型，后者的NaN不等于自身，以及+0等于-0。JavaScript 缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6 提出同值相等算法，用来解决这个问题。Object.is就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。

```
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false
```
不同之处只有两个：一是+0不等于-0，二是NaN等于自身。

```
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
* Object.assign()  **这个常用**

Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

```
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
如果只有一个参数，Object.assign会直接返回该参数。

```
const obj = {a: 1};
Object.assign(obj) === obj // true
```
由于undefined和null无法转成对象，所以如果它们作为参数，就会报错。

```
Object.assign(undefined) // 报错
Object.assign(null) // 报错
```
注意：Object.assign可以用来处理数组，但是会把数组视为对象。

```
Object.assign([1, 2, 3], [4, 5])
// [4, 5, 3]
//把数组视为属性名为 0、1、2 的对象，因此源数组的 0 号属性4覆盖了目标数组的 0 号属性1。
```
* Object.keys()
ES5 引入了Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```
var obj = { foo: 'bar', baz: 42 };
Object.keys(obj)
// ["foo", "baz"]
```
ES2017 引入了跟Object.keys配套的Object.values和Object.entries，作为遍历一个对象的补充手段，供for...of循环使用。

```
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```
* Object.values()

Object.values方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

```
const obj = { foo: 'bar', baz: 42 };
Object.values(obj)
// ["bar", 42]
```
返回数组的成员顺序

```
const obj = { 100: 'a', 2: 'b', 7: 'c' };
Object.values(obj)
// ["b", "c", "a"]
```
**上面代码中，属性名为数值的属性，是按照数值大小，从小到大遍历的，因此返回的顺序是b、c、a。**
* Object.entries

Object.entries方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

```
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj)
// [ ["foo", "bar"], ["baz", 42] ]
```
除了返回值不一样，该方法的行为与Object.values基本一致。
#### 对象的扩展运算符
* 解构赋值

```
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```
由于解构赋值要求等号右边是一个对象，所以如果等号右边是undefined或null，就会报错，因为它们无法转为对象。

```
let { x, y, ...z } = null; // 运行时错误
let { x, y, ...z } = undefined; // 运行时错误
```
解构赋值必须是最后一个参数，否则会报错。

```
let { ...x, y, z } = obj; // 句法错误
let { x, ...y, ...z } = obj; // 句法错误
```
**注意，解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本。**

```
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```
* 扩展运算符

扩展运算符（...）用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。
```
let z = { a: 3, b: 4 };
let n = { ...z };
n // { a: 3, b: 4 }
```
这等同于使用Object.assign方法。
```
let aClone = { ...a };
// 等同于
let aClone = Object.assign({}, a);
```
## 第九章 symbol   ES6新增的数据类型
ES5里面对象的属性名都是字符串，如果你需要使用一个别人提供的对象，你对这个对象有哪些属性也不是很清楚，但又想为这个对象新增一些属性，那么你新增的属性名就很可能和原来的属性名发送冲突，显然我们是不希望这种情况发生的。所以，我们需要确保每个属性名都是独一无二的，这样就可以防止属性名的冲突了。因此，ES6里就引入了Symbol，用它来产生一个独一无二的值。
#### Symbol是什么
Symbol实际上是ES6引入的一种原始数据类型，除了Symbol，JavaScript还有其他5种原始数据类型，分别是Undefined、Null、Boolean、String、Number、对象，这5种数据类型都是ES5中就有的。
#### 怎么生成一个Symbol类型的值
Symbol值是通过Symbol函数生成的，如下：

```
let s = Symbol();
console.log(s);  // Symbol()
typeof s;  // "symbol"
```
#### Symbol函数前不能用new
Symbol函数不是一个构造函数，前面不能用new操作符。所以Symbol类型的值也不是一个对象，不能添加任何属性，它只是一个类似于字符型的数据类型。如果强行在Symbol函数前加上new操作符，会报错，如下：

```
let s = new Symbol();
// Uncaught TypeError: Symbol is not a constructor(…)
```
### Symbol函数的参数
#### 字符串作为参数
用上面的方法生成的Symbol值不好进行区分，Symbol函数还可以接受一个字符串参数，来对产生的Symbol值进行描述，方便我们区分不同的Symbol值。

```
let s1 = Symbol('s1');
let s2 = Symbol('s2');
console.log(s1);  // Symbol(s1)
console.log(s2);  // Symbol(s2)
s1 === s2;  //  false
let s3 = Symbol('s2');
s2 === s3;  //  false
```
1. 给Symbol函数加了参数之后，控制台输出的时候可以区分到底是哪一个值；
2. Symbol函数的参数只是对当前Symbol值的描述，因此相同参数的Symbol函数返回值是不相等的；
#### 对象作为参数
如果Symbol函数的参数是一个对象，就会调用该对象的toString方法，将其转化为一个字符串，然后才生成一个Symbol值。所以，说到底，Symbol函数的参数只能是字符串。
#### ymbol值不可以进行运算
既然Symbol是一种数据类型，那我们一定想知道Symbol值是否能进行运算。告诉你，Symbol值是不能进行运算的,不仅不能和Symbol值进行运算，也不能和其他类型的值进行运算，否则会报错。
Symbol值可以显式转化为字符串和布尔值，但是不能转为数值。

```
var mysym1 = Symbol('my symbol');

mysym1.toString() //  'Symbol('my symbol')'

String(mysym1)  //  'Symbol('my symbol')'

var mysym2 = Symbol();

Boolean(mysym2);  // true

Number(mysym2)  // TypeError: Cannot convert a Symbol value to a number(…)
```
#### Symbol作属性名
Symbol就是为对象的属性名而生，那么Symbol值怎么作为对象的属性名呢？有下面几种写法：

```
let a = {};
let s4 = Symbol();
// 第一种写法
a[s4] = 'mySymbol';
// 第二种写法
a = {
    [s4]: 'mySymbol'
}
// 第三种写法
Object.defineProperty(a, s4, {value: 'mySymbol'});
a.s4;  //  undefined
a.s4 = 'mySymbol';
a[s4]  //  undefined
a['s4']  // 'mySymbol'
```
1. 使用对象的Symbol值作为属性名时，获取相应的属性值不能用点运算符；
2. 如果用点运算符来给对象的属性赋Symbol类型的值，实际上属性名会变成一个字符串，而不是一个Symbol值；
3. 在对象内部，使用Symbol值定义属性时，Symbol值必须放在方括号之中，否则只是一个字符串。

#### Symbol值作为属性名的遍历
使用for...in和for...of都无法遍历到Symbol值的属性，Symbol值作为对象的属性名，也无法通过Object.keys()、Object.getOwnPropertyNames()来获取了。但是，不同担心，这种平常的需求肯定是会有解决办法的。我们可以使用Object.getOwnPropertySymbols()方法获取一个对象上的Symbol属性名。也可以使用Reflect.ownKeys()返回所有类型的属性名，包括常规属性名和 Symbol属性名。

```
let s5 = Symbol('s5');
let s6 = Symbol('s6');
let a = {
    [s5]: 's5',
    [s6]: 's6'
}
Object.getOwnPropertySymbols(a);   // [Symbol(s5), Symbol(s6)]
a.hello = 'hello';
Reflect.ownKeys(a);  //  ["hello", Symbol(s5), Symbol(s6)]
```
利用Symbol值作为对象属性的名称时，不会被常规方法遍历到这一特性，可以为对象定义一些非私有的但是又希望只有内部可用的方法。
#### Symbol.for()和Symbol.keyFor()
Symbol.for()函数也可以用来生成Symbol值，但该函数有一个特殊的用处，就是可以重复使用一个Symbol值。
```
let s1 = Symbol.for("s11");
let s2 = Symbol.for("s22");

console.log(s1===s2)//false

let s3 = Symbol("s33");
let s4 = Symbol("s33");

console.log(s3===s4)//false

console.log(Symbol.keyFor(s3))//undefined
console.log(Symbol.keyFor(s2))//"s22"
console.log(Symbol.keyFor(s1))//"s11"
```
**Symbol.for()**函数要接受一个字符串作为参数，先搜索有没有以该参数作为名称的Symbol值，如果有，就直接返回这个Symbol值，否则就新建并返回一个以该字符串为名称的Symbol值。

**Symbol.keyFor()**函数是用来查找一个Symbol值的登记信息的，Symbol()写法没有登记机制，所以返回undefined；而Symbol.for()函数会将生成的Symbol值登记在全局环境中，所以Symbol.keyFor()函数可以查找到用Symbol.for()函数生成的Symbol值。
#### 内置Symbol值
ES6提供了11个内置的Symbol值，分别是Symbol.hasInstance 、Symbol.isConcatSpreadable 、Symbol.species 、Symbol.match 、Symbol.replace 、Symbol.search 、Symbol.split 、Symbol.iterator 、Symbol.toPrimitive 、Symbol.toStringTag 、Symbol.unscopables 等。
有兴趣的可以自行了解: [地址](http://es6.ruanyifeng.com/#docs/symbol)
## 第十章 Set 和 Map 数据结构 (也就用过[...new Set([1, 2, 3, 1])]去重)
### Set
* 基本用法

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。
Set 本身是一个构造函数，用来生成 Set 数据结构。

```
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
//通过add方法向 Set 结构加入成员，结果表明 Set 结构不会添加重复的值。
```
Set 函数可以接受一个数组作为参数，用来初始化。

```
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 例三
function divs () {
  return [...document.querySelectorAll('div')];
}

const set = new Set(divs());
set.size // 56

// 类似于
divs().forEach(div => set.add(div));
set.size // 56
```
#### 对数组去重

```
// 去除数组的重复成员
[...new Set(array)]
```
1. 向 Set 加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。
2. Set 内部判断两个值是否不同，使用的算法叫做“Same-value equality”，它类似于精确相等运算符（===），主要的区别是NaN等于自身，而精确相等运算符认为NaN不等于自身。

**注意：两个对象总是不相等的。**
#### Set 实例的属性和方法 
* Set 结构的实例有以下属性：

```
- Set.prototype.constructor：构造函数，默认就是Set函数。
- Set.prototype.size：返回Set实例的成员总数。
Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。

```
* 四个操作方法：

```
- add(value)：添加某个值，返回 Set 结构本身。
- delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
- has(value)：返回一个布尔值，表示该值是否为Set的成员。
- clear()：清除所有成员，没有返回值。

```
* Array.from()可以将 Set 结构转为数组。
```
const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);
```
* 遍历操作

```
Set 结构的实例有四个遍历方法，可以用于遍历成员。

- keys()：返回键名的遍历器
- values()：返回键值的遍历器
- entries()：返回键值对的遍历器
- forEach()：使用回调函数遍历每个成员

keys()，values()，entries()

keys方法、values方法、entries方法返回的都是遍历器对象。
由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致。

let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```
Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的values方法。
* forEach()

Set 结构的实例与数组一样，也拥有forEach方法，用于对每个成员执行某种操作，没有返回值。

```
let set = new Set([1, 4, 9]);
set.forEach((value, key) => console.log(key + ' : ' + value))
// 1 : 1
// 4 : 4
// 9 : 9
```
forEach方法还可以有第二个参数，表示绑定处理函数内部的this对象。
### WeakSet (本人表示基本没用过)
WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别：

1. WeakSet 的成员只能是对象，而不能是其他类型的值。
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。

由于上面这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。
#### 用法跟set差不多

```
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]}

//下面的写法不行
const b = [3, 4];
const ws = new WeakSet(b);
// Uncaught TypeError: Invalid value used in weak set(…)
```
WeakSet 结构有以下三个方法。

- WeakSet.prototype.add(value)：向 WeakSet 实例添加一个新成员。
- WeakSet.prototype.delete(value)：清除 WeakSet 实例的指定成员。
- WeakSet.prototype.has(value)：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。

WeakSet 没有size属性，没有办法遍历它的成员。
### Map (基本没用过)
#### 含义和基本用法
JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

为了解决这个问题，ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

```
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```
作为构造函数，Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。

```
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```
注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
```
如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等，Map 将其视为一个键，比如0和-0就是一个键，布尔值true和字符串true则是两个不同的键。另外，undefined和null也是两个不同的键。虽然NaN不严格相等于自身，但 Map 将其视为同一个键。
#### 实例的属性和操作方法 

```
1. size属性		返回成员总数
2. set(key,value)       设置键值对，返回Map结构
3. get(key)             读取key对应的值，找不到就是undefined
4. has(key)             返回布尔值，表示key是否在Map中
5. delete(key)          删除某个键，返回true，失败返回false
6. clear()              清空所有成员，没有返回值
```
#### 遍历方法

```
Map 结构原生提供三个遍历器生成函数和一个遍历方法。

- keys()：返回键名的遍历器。
- values()：返回键值的遍历器。
- entries()：返回所有成员的遍历器。
- forEach()：遍历 Map 的所有成员。

需要特别注意的是，Map 的遍历顺序就是插入顺序。遍历行为基本与set的一致。
```
#### 与其他数据结构的互相转换
* Map 转为数组

```
const myMap = new Map()
  .set(true, 7)
  .set({foo: 3}, ['abc']);
[...myMap]
```
* 数组 转为 Map

```
new Map([
  [true, 7],
  [{foo: 3}, ['abc']]
])
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```
* Map 转为对象
如果所有 Map 的键都是字符串，它可以转为对象。

```
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}

const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)
// { yes: true, no: false }
```
* 对象转为 Map

```
function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})
// Map {"yes" => true, "no" => false}
```
* Map 转为 JSON
Map 转为 JSON 要区分两种情况。一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。

```
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'

```
另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。

```
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'
```
* JSON 转为 Map
JSON 转为 Map，正常情况下，所有键名都是字符串。

```
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}

jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false}
```
但是，有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是数组转为 JSON 的逆操作。

```
function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```
### WeakMap 的语法 (同样没用过)
WeakMap只有四个方法可用：get()、set()、has()、delete()。

无法被遍历，因为没有size。无法被清空，因为没有clear()，跟WeakSet相似。
#### WeakMap 应用的典型

```
let myElement = document.getElementById('logo');
let myWeakmap = new WeakMap();

myWeakmap.set(myElement, {timesClicked: 0});

myElement.addEventListener('click', function() {
  let logoData = myWeakmap.get(myElement);
  logoData.timesClicked++;
}, false);
```
上面代码中，myElement是一个 DOM 节点，每当发生click事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是myElement。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。
## 第十一章 Proxy
Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”，即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。
**Vue3.0使用了proxy**

```
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(`setting ${key}!`);
    return Reflect.set(target, key, value, receiver);
  }
});
// target表示要拦截的数据
// key表示要拦截的属性
// value表示要拦截的属性的值
// receiver表示Proxy{}
//上面代码对一个空对象架设了一层拦截，重定义了属性的读取（get）和设置（set）行为
obj.count = 1
//  setting count!
++obj.count
//  getting count!
//  setting count!
//  2
```
上面代码说明，Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```
let proxy = new Proxy(target, handler);
Proxy 对象的所有用法，都是上面这种形式，不同的只是handler参数的写法。其中，new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。
```
**Proxy 对象的所有用法，都是上面这种形式，不同的只是handler参数的写法。其中，new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为。**

```
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});
proxy.time = 10
proxy.time // 35  // 拦截了所有的获取属性,都会返回35
proxy.name // 35
proxy.title // 35
```
如果handler没有设置任何拦截，那就等同于直接通向原对象。

```
var target = {};
var handler = {};
var proxy = new Proxy(target, handler);
proxy.a = 'b';
target.a // "b"
```
上面代码中，handler是一个空对象，没有任何拦截效果，访问proxy就等同于访问target。

同一个拦截器函数，可以设置拦截多个操作。

对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。

**下面是 Proxy 支持的拦截操作一览，一共 13 种:**

- get(target, propKey, receiver)：拦截对象属性的读取，比如proxy.foo和proxy['foo']。
- set(target, propKey, value, receiver)：拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。
- has(target, propKey)：拦截propKey in proxy的操作，返回一个布尔值。
- deleteProperty(target, propKey)：拦截delete proxy[propKey]的操作，返回一个布尔值。
- ownKeys(target)：拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。
- getOwnPropertyDescriptor(target, propKey)：拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。
- defineProperty(target, propKey, propDesc)：拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。
- preventExtensions(target)：拦截Object.preventExtensions(proxy)，返回一个布尔值。
- getPrototypeOf(target)：拦截Object.getPrototypeOf(proxy)，返回一个对象。
- isExtensible(target)：拦截Object.isExtensible(proxy)，返回一个布尔值。
- setPrototypeOf(target, proto)：拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- apply(target, object, args)：拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
- construct(target, args)：拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

**例如：**

deleteProperty方法用于拦截delete操作，如果这个方法抛出错误或者返回false，当前属性就无法被delete命令删除。

apply方法拦截函数的调用、call和apply操作。

get方法用于拦截某个属性的读取操作。

```
let obj2 = new Proxy(obj,{
  get(target,property,a){
    //return 35;
    /*console.log(target)
   				console.log(property)*/
    let Num = ++wkMap.get(obj).getPropertyNum;
    console.log(`当前访问对象属性次数为：${Num}`)
    return target[property]

  },
  deleteProperty(target,property){
    return false;
  },
  apply(target,ctx,args){
    return Reflect.apply(...[target,[],args]);;
  }

})
```
#### Proxy.revocable()
Proxy.revocable方法返回一个可取消的 Proxy 实例。

```
let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo // 123

revoke();
proxy.foo // TypeError: Revoked
```
Proxy.revocable方法返回一个对象，该对象的proxy属性是Proxy实例，revoke属性是一个函数，可以取消Proxy实例。上面代码中，当执行revoke函数之后，再访问Proxy实例，就会抛出一个错误。

Proxy.revocable的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。
#### this 问题
虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的this关键字会指向 Proxy 代理。

```
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};

const proxy = new Proxy(target, handler);

target.m() // false
proxy.m()  // true
//一旦proxy代理target.m，后者内部的this就是指向proxy，而不是target。
```
## 第十二章 Promise (重点章节)
#### 概念
Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。

所`Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
#### 特点
1. 对象的状态不受外界影响。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。
#### 状态
Promise对象代表一个异步操作，有三种状态：

pending（进行中）、fulfilled（已成功）和rejected（已失败）。

只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
#### 缺点
1. 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
2. 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
3. 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）
#### 用法
写js必然不会对异步事件陌生。

```
settimeout(()=>{
  console.log("123")
},0)

console.log("abc")
//先输出谁？
```
答案我想我不用说,大家都知道

如果abc需要在123执行结束后再输出怎么办？

当然，可以使用callback，但是callback使用起来是一件很让人绝望的事情。

这时：Promise这个为异步编程而生的对象站了出来....

```
let p = new Promise((resolve,reject)=>{
  //一些异步操作
  setTimeout(()=>{
    console.log("123")
    resolve("abc");
    reject("我是错误信息")
  },0)
})
.then(function(data){
  //resolve状态
  console.log(data)
},function(err){
  //reject状态
  console.log(err)
})
//'123'
//'abc'
// 我是错误信息
```
这时候你应该有两个疑问：

1.包装这么一个函数有毛线用？

2.resolve('123');这是干毛的？

Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。

也就是说，状态由实例化时的参数（函数）执行来决定的，根据不同的状态，看看需要走then的第一个参数还是第二个。

resolve()和reject()的参数会传递到对应的回调函数的data或err

then返回的是一个新的Promise实例，也就是说可以继续then

#### 链式操作的用法
所以，从表面上看，Promise只是能够简化层层回调的写法，而实质上，Promise的精髓是“状态”，用维护状态、传递状态的方式来使得回调函数能够及时调用，它比传递callback函数要简单、灵活的多。所以使用Promise的正确场景是这样的：

```
runAsync1()
.then(function(data){
    console.log(data);
    return runAsync2();
})
.then(function(data){
    console.log(data);
    return runAsync3();
})
.then(function(data){
    console.log(data);
});
//异步任务1执行完成
//随便什么数据1
//异步任务2执行完成
//随便什么数据2
//异步任务3执行完成
//随便什么数据3
```
runAsync1、runAsync2、runAsync3长这样↓

```
function runAsync1(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务1执行完成');
            resolve('随便什么数据1');
        }, 1000);
    });
    return p;            
}
function runAsync2(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务2执行完成');
            resolve('随便什么数据2');
        }, 2000);
    });
    return p;            
}
function runAsync3(){
    var p = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务3执行完成');
            resolve('随便什么数据3');
        }, 2000);
    });
    return p;            
}
```
在then方法中，你也可以直接return数据而不是Promise对象，在后面的then中也可以接收到数据：

```
runAsync1()
.then(function(data){
    console.log(data);
    return runAsync2();
})
.then(function(data){
    console.log(data);
    return '直接返回数据';  //这里直接返回数据
})
.then(function(data){
    console.log(data);
});
//异步任务1执行完成
//随便什么数据1
//异步任务2执行完成
//随便什么数据2
//直接返回数据
```
#### reject的用法
前面的例子都是只有“执行成功”的回调，还没有“失败”的情况，reject的作用就是把Promise的状态置为rejected，这样我们在then中就能捕捉到，然后执行“失败”情况的回调。

```
let num = 10;
let p1 = function() {
   	return new Promise((resolve,reject)=>{
      if (num <= 5) {
        resolve("<=5，走resolce")
        console.log('resolce不能结束Promise')
      }else{
        reject(">5，走reject")
        console.log('reject不能结束Promise')
      }
    }) 
}

p1()
  .then(function(data){
    console.log(data)
  },function(err){
    console.log(err)
  })
//reject不能结束Promise
//>5，走reject
```
resolve和reject永远会在当前环境的最后执行，所以后面的同步代码会先执行。

如果resolve和reject之后还有代码需要执行，最好放在then里。

然后在resolve和reject前面写上return。
#### Promise.prototype.catch()
Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。

```
// 接着上面的例子
p1()
  .then(function(data){
    console.log(data)
  })
  .catch(function(err){
  	console.log(err)
  })
//reject不能结束Promise
//>5，走reject 	
```
#### Promise.all() 
Promise.all方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```
const p = Promise.all([p1, p2, p3]);
```
p的状态由p1、p2、p3决定，分成两种情况。

1. 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled。 此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
2. 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

promises是包含 3 个 Promise 实例的数组，只有这 3 个实例的状态都变成fulfilled，或者其中有一个变为rejected，才会调用Promise.all方法后面的回调函数。

如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法，如果没有参数没有定义自己的catch，就会调用Promise.all()的catch方法。
#### Promise.race()
Promise.race方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

```
const p = Promise.race([p1, p2, p3]);
// 上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。
// 那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。
```
#### Promise.resolve()
有时需要将现有对象转为 Promise 对象，Promise.resolve方法就起到这个作用。

```
const jsPromise = Promise.resolve('123');
```
上面代码将123转为一个 Promise 对象。

Promise.resolve等价于下面的写法。

```
Promise.resolve('123')
// 等价于
new Promise(resolve => resolve('123'))
```
##### Promise.resolve方法的参数分成四种情况。
* 参数是一个 Promise 实例
* 参数是一个thenable对象

thenable对象指的是具有then方法的对象，比如下面这个对象。
```
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

```
Promise.resolve方法会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then方法。

```
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
  console.log(value);  // 42
});
```
上面代码中，thenable对象的then方法执行后，对象p1的状态就变为resolved，从而立即执行最后那个then方法指定的回调函数，输出 42。
* 参数不是具有then方法的对象，或根本就不是对象

如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态为resolved。

```
const p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello
```
上面代码生成一个新的 Promise 对象的实例p。由于字符串Hello不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是resolved，所以回调函数会立即执行。Promise.resolve方法的参数，会同时传给回调函数。
* 不带有任何参数

Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。

所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法。

```
const p = Promise.resolve();

p.then(function () {
  // ...
});
```
上面代码的变量p就是一个 Promise 对象。

需要注意的是，立即resolve的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时。

```
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```
上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。
#### Promise.reject()
Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。

```
const p = Promise.reject('出错了');
// 等同于
const p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s) {
  console.log(s)
});
// 出错了

```
上面代码生成一个 Promise 对象的实例p，状态为rejected，回调函数会立即执行。

注意，Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。这一点与Promise.resolve方法不一致。

```
const thenable = {
  then(resolve, reject) {
    reject('出错了');
  }
};

Promise.reject(thenable)
.catch(e => {
  console.log(e === thenable)
})
// true
```
上面代码中，Promise.reject方法的参数是一个thenable对象，执行以后，后面catch方法的参数不是reject抛出的“出错了”这个字符串，而是thenable对象。
## 第十三章 Iterator
####概念
迭代器是一种接口、是一种机制。

为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

Iterator 的作用有三个：

1. 为各种数据结构，提供一个统一的、简便的访问接口；
2. 使得数据结构的成员能够按某种次序排列；
3. 主要供for...of消费。

Iterator本质上，就是一个指针对象。

过程是这样的：

（1）创建一个指针对象，指向当前数据结构的起始位置。

（2）第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的next方法，直到它指向数据结构的结束位置。

* 普通函数实现Iterator
* 
```
function myIter(obj){
  let i = 0;
  return {
    next(){
      let done = (i>=obj.length);
      let value = !done ? obj[i++] : undefined;
      return {
        value,
        done,
      }
    }
  }
}
```
原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- 函数的 arguments 对象
- NodeList 对象

下面的例子是数组的Symbol.iterator属性。

```
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```
下面是另一个类似数组的对象调用数组的Symbol.iterator方法的例子。

```
let iterable = {
  0: 'a',
  1: 'b',
  2: 'c',
  length: 3,
  [Symbol.iterator]: Array.prototype[Symbol.iterator]
};
for (let item of iterable) {
  console.log(item); // 'a', 'b', 'c'
}
```
注意，普通对象部署数组的Symbol.iterator方法，并无效果。

```
let iterable = {
  a: 'a',
  b: 'b',
  c: 'c',
  length: 3,
  [Symbol.iterator]: Array.prototype[Symbol.iterator]
};
for (let item of iterable) {
  console.log(item); // undefined, undefined, undefined
}
```
字符串是一个类似数组的对象，也原生具有 Iterator 接口。

```
var someString = "hi";
typeof someString[Symbol.iterator]
// "function"

var iterator = someString[Symbol.iterator]();

iterator.next()  // { value: "h", done: false }
iterator.next()  // { value: "i", done: false }
iterator.next()  // { value: undefined, done: true }
```
## 第十四章 Generator
#### 基本概念
Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。
* 跟普通函数的区别
1. function关键字与函数名之间有一个星号；
2. 函数体内部使用yield表达式，定义不同的内部状态。
3. Generator函数不能跟new一起使用，会报错。

```
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```
上面代码定义了一个 Generator 函数helloWorldGenerator，它内部有两个yield表达式（hello和world），即该函数有三个状态：hello，world 和 return 语句（结束执行）。

调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是遍历器对象。

下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

**ES6 没有规定，function关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。**

```
function * foo(x, y) { ··· }
function *foo(x, y) { ··· }
function* foo(x, y) { ··· }
function*foo(x, y) { ··· }
```
#### yield 表达式
由于 Generator 函数返回的遍历器对象，只有调用next方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。yield表达式就是暂停标志。

遍历器对象的next方法的运行逻辑如下。

（1）遇到yield表达式，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，作为返回的对象的value属性值。

（2）下一次调用next方法时，再继续往下执行，直到遇到下一个yield表达式。

（3）如果没有再遇到新的yield表达式，就一直运行到函数结束，直到return语句为止，并将return语句后面的表达式的值，作为返回的对象的value属性值。

（4）如果该函数没有return语句，则返回的对象的value属性值为undefined。

**yield表达式与return语句既有相似之处**

都能返回紧跟在语句后面的那个表达式的值。

**不同之处**

每次遇到yield，函数暂停执行，下一次再从该位置继续向后执行，而return语句不具备位置记忆的功能。一个函数里面，只能执行一次（或者说一个）return语句，但是可以执行多次（或者说多个）yield表达式。正常函数只能返回一个值，因为只能执行一次return；Generator 函数可以返回一系列的值，因为可以有任意多个yield。

**注意**：

yield表达式只能用在 Generator 函数里面，用在其他地方都会报错。

另外，yield表达式如果用在另一个表达式之中，必须放在圆括号里面。

```
console.log('Hello' + yield 123); // SyntaxError
console.log('Hello' + (yield 123)); // OK
```
#### 与 Iterator 接口的关系 
由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的Symbol.iterator属性，从而使得该对象具有 Iterator 接口。

```
Object.prototype[Symbol.iterator] = function* (){
  for(let i in this){
    yield this[i];
  }
}
//--------------
function* iterEntries(obj) {
  let keys = Object.keys(obj);
  for (let i=0; i < keys.length; i++) {
    let key = keys[i];
    yield [key, obj[key]];
  }
}

let myObj = { foo: 3, bar: 7 };

for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}

```
#### next 方法的参数

```
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}

var g = f();

g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```
这个功能有很重要的语法意义。

Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过next方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。

```
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```
#### for...of 循环
for...of循环可以自动遍历 Generator 函数时生成的Iterator对象，且此时不再需要调用next方法。
```
function *foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```

```
function* fibonacci() {
  let [prev, curr] = [1, 1];
  while(true){
    [prev, curr] = [curr, prev + curr];
    yield curr;
  }
}

for (let n of fibonacci()) {
  if (n > 10000000) break;
  console.log(n);
}
```
#### Generator.prototype.return()
Generator 函数返回的遍历器对象，还有一个return方法，可以返回给定的值，并且终结遍历 Generator 函数。

```
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()        // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```
#### yield* 
如果在 Generator 函数内部，调用另一个 Generator 函数，默认情况下是没有效果的。

```
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  foo();
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "y"
```
foo和bar都是 Generator 函数，在bar里面调用foo，是不会有效果的。

这个就需要用到yield*表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数。

```
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
```
再来看一个对比的例子。

```
function* inner() {
  yield 'hello!';
}

function* outer1() {
  yield 'open';
  yield inner();
  yield 'close';
}

var gen = outer1()
gen.next().value // "open"
gen.next().value // 返回一个遍历器对象
gen.next().value // "close"

function* outer2() {
  yield 'open'
  yield* inner()
  yield 'close'
}

var gen = outer2()
gen.next().value // "open"
gen.next().value // "hello!"
gen.next().value // "close"
```
上面例子中，outer2使用了yield*，outer1没使用。结果就是，outer1返回一个遍历器对象，outer2返回该遍历器对象的内部值。

从语法角度看，如果yield表达式后面跟的是一个遍历器对象，需要在yield表达式后面加上星号，表明它返回的是一个遍历器对象。这被称为yield*表达式。
#### 作为对象属性的 Generator 函数
如果一个对象的属性是 Generator 函数，可以简写成下面的形式。
```
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
```
**说实话学习了async await之后Generator 函数基本可以不用了**
## 第十五章 async 函数 (重点这个,贼好用,我项目中基本都用这个了)
ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

async 函数是 Generator 函数的语法糖。

什么是语法糖？

意指那些没有给计算机语言添加新功能，而只是对人类来说更“甜蜜”的语法。语法糖往往给程序员提供了更实用的编码方式，有益于更好的编码风格，更易读。不过其并没有给语言添加什么新东西。

反向还有语法盐：

主要目的是通过反人类的语法，让你更痛苦的写代码，虽然同样能达到避免代码书写错误的效果，但是编程效率很低，毕竟提高了语法学习门槛，让人齁到忧伤。。。

async函数使用时就是将 Generator 函数的星号（*）替换成async，将yield替换成await，仅此而已。

async函数对 Generator 函数的区别：

（1）内置执行器。

Generator 函数的执行必须靠执行器，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。

（2）更好的语义。

async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。

（3）正常情况下，await命令后面是一个 Promise 对象。如果不是，会被转成一个立即resolve的 Promise 对象。

（4）返回值是 Promise。

async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用then方法指定下一步的操作。

进一步说，async函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而await命令就是内部then命令的语法糖。
#### 错误处理
如果await后面的异步操作出错，那么等同于async函数返回的 Promise 对象被reject。
```
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了');
  });
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
// Error：出错了
```
上面代码中，async函数f执行后，await后面的 Promise 对象会抛出一个错误对象，导致catch方法的回调函数被调用，它的参数就是抛出的错误对象。具体的执行机制，可以参考后文的“async 函数的实现原理”。

防止出错的方法，也是将其放在try...catch代码块之中。

```
async function f() {
  try {
    await new Promise(function (resolve, reject) {
      throw new Error('出错了');
    });
  } catch(e) {
  }
  return await('hello world');
}
```
如果有多个await命令，可以统一放在try...catch结构中。

```
async function main() {
  try {
    const val1 = await firstStep();
    const val2 = await secondStep(val1);
    const val3 = await thirdStep(val1, val2);

    console.log('Final: ', val3);
  }
  catch (err) {
    console.error(err);
  }
}
```
**应用**

```
var fn = function (time) {
  console.log("开始处理异步");
  setTimeout(function () {
    console.log(time);
    console.log("异步处理完成");
    iter.next();
  }, time);

};

function* g(){
  console.log("start");
  yield fn(3000)
  yield fn(500)
  yield fn(1000)
  console.log("end");
}

let iter = g();
iter.next();
```
下面是async函数的写法

```
var fn = function (time) {
  return new Promise(function (resolve, reject) {
    console.log("开始处理异步");
    setTimeout(function () {
      resolve();
      console.log(time);
      console.log("异步处理完成");
    }, time);
  })
};

var start = async function () {
  // 在这里使用起来就像同步代码那样直观
  console.log('start');
  await fn(3000);
  await fn(500);
  await fn(1000);
  console.log('end');
};

start();
```
## 第十六章 Class (重点这个,贼好用,我项目中基本都用这个了)
class跟let、const一样：不存在变量提升、不能重复声明...

es5面向对象写法跟传统的面向对象语言（比如 C++ 和 Java）差异很大，很容易让新学习这门语言的程序员感到困惑。

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。

ES6 的class可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

```
//es5
function Fn(x, y) {
  this.x = x;
  this.y = y;
}

Fn.prototype.add = function () {
  return this.x + this.y;
};
//等价于
//es6
class Fn{
  constructor(x,y){
    this.x = x;
    this.y = y;
  }
  
  add(){
    return this.x + this.y;
  }
}

var F = new Fn(1, 2);
console.log(F.add()) //3
```
构造函数的prototype属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的prototype属性上面。

```
class Fn {
  constructor() {
    // ...
  }

  add() {
    // ...
  }

  sub() {
    // ...
  }
}

// 等同于

Fn.prototype = {
  constructor() {},
  add() {},
  sub() {},
};
```
类的内部所有定义的方法，都是不可枚举的（non-enumerable），这与es5不同。

```
//es5
var Fn = function (x, y) {
  // ...
};

Point.prototype.add = function() {
  // ...
};

Object.keys(Fn.prototype)
// ["toString"]
Object.getOwnPropertyNames(Fn.prototype)
// ["constructor","add"]

//es6
class Fn {
  constructor(x, y) {
    // ...
  }

  add() {
    // ...
  }
}

Object.keys(Fn.prototype)
// []
Object.getOwnPropertyNames(Fn.prototype)
// ["constructor","add"]
```
#### 严格模式
类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。

考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。
#### constructor
onstructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。

```
class Fn {
}

// 等同于
class Fn {
  constructor() {}
}
```
constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。

```
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false
//constructor函数返回一个全新的对象，结果导致实例对象不是Foo类的实例。
```
#### 类必须使用new调用
类必须使用new调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。

```
class Foo {
  constructor() {
    return Object.create(null);
  }
}

Foo()
// TypeError: Class constructor Foo cannot be invoked without 'new'
```
#### Class 表达式
与函数一样，类也可以使用表达式的形式定义。

```
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```
上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是MyClass而不是Me，Me只在 Class 的内部代码可用，指代当前类。

```
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```
如果类的内部没用到的话，可以省略Me，也就是可以写成下面的形式。

```
const MyClass = class { /* ... */ };
```
采用 Class 表达式，可以写出立即执行的 Class。

```
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"

```
上面代码中，person是一个立即执行的类的实例。
#### 私有方法和私有属性
私有方法/私有属性是常见需求，但 ES6 不提供，只能通过变通方法模拟实现。（以后会实现）

通常是在命名上加以区别。

```
class Fn {

  // 公有方法
  foo () {
    //....
  }

  // 假装是私有方法（其实外部还是可以访问）
  _bar() {
    //....
  }
}
```
#### 原型的属性
class定义类时，只能在constructor里定义属性，在其他位置会报错。

如果需要在原型上定义方法可以使用：

1. Fn.prototype.prop = value;
2. Object.getPrototypeOf()获取原型，再来扩展
3. Object.assign(Fn.prototype,{在这里面写扩展的属性或者方法})
#### Class 的静态方法
类相当于实例的原型，所有在类中定义的方法，都会被实例继承。

如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

ES6 明确规定，Class 内部只有静态方法，没有静态属性。

```
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function

//静态属性只能手动设置
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```
#### get、set

```
class Fn{
	constructor(){
		this.arr = []
	}
	get bar(){
		return this.arr;
	}
	set bar(value){
		this.arr.push(value)
	}
}


let obj = new Fn();

obj.menu = 1;
obj.menu = 2;

console.log(obj.menu)//[1,2]
console.log(obj.arr)//[1,2]
```
#### 继承
* 用法

```
class Fn {
}

class Fn2 extends Fn {
}
```
* 注意

子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象。
```
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor() {
    super()//必须调用
  }
}

let cp = new ColorPoint(); // ReferenceError
```
**父类的静态方法也会被继承。**
#### Object.getPrototypeOf()
Object.getPrototypeOf方法可以用来从子类上获取父类。

```
Object.getPrototypeOf(Fn2) === Fn
// true
```
**因此，可以使用这个方法判断，一个类是否继承了另一个类。**
#### super 关键字
super这个关键字，既可以当作函数使用，也可以当作对象使用。在这两种情况下，它的用法完全不同。

第一种情况，super作为函数调用时，代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次super函数。

作为函数时，super()只能用在子类的构造函数之中，用在其他地方就会报错。

```
class A {}

class B extends A {
  constructor() {
    super();
  }
}
```
上面代码中，子类B的构造函数之中的super()，代表调用父类的构造函数。这是必须的，否则 JavaScript 引擎会报错。

注意，super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B，因此super()在这里相当于A.prototype.constructor.call(this)。

第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

```
class A {
  p() {
    return 2;
  }
}

class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}

let b = new B();
```
上面代码中，子类B当中的super.p()，就是将super当作一个对象使用。这时，super在普通方法之中，指向A.prototype，所以super.p()就相当于A.prototype.p()。

由于this指向子类，所以如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性。

```
class A {
  constructor() {
    this.x = 1;
  }
}

class B extends A {
  constructor() {
    super();
    this.x = 2;
    super.x = 3;
    console.log(super.x); // undefined
    console.log(this.x); // 3
  }
}

let b = new B();
```
上面代码中，super.x赋值为3，这时等同于对this.x赋值为3。而当读取super.x的时候，读的是A.prototype.x，所以返回undefined。
## 第十七章 Module
ES6 的模块自动采用严格模式，不管你有没有在模块头部加上"use strict";。
#### export 命令
模块功能主要由两个命令构成：export和import。

export命令用于规定模块的对外接口。

import命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。

export输出变量的写法：

```
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```
还可以：

```
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
//跟上面写法等价，推荐这种写法。
```
export命令除了输出变量，还可以输出函数或类（class）。

```
export function multiply(x, y) {
  return x * y;
};
```
通常情况下，export输出的变量就是本来的名字，但是可以使用as关键字重命名。

```
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```
**需要特别注意的是，export命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。**

```
// 报错
export 1;

// 报错
var m = 1;
export m;

//正确写法
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```
同样的，function和class的输出，也必须遵守这样的写法。

```
// 报错
function f() {}
export f;

// 正确
export function f() {};

// 正确
function f() {}
export {f};
```
export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。

```
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```
上面代码输出变量foo，值为bar，500 毫秒之后变成baz。
export命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错，import命令也是如此。
#### import 命令
使用export命令定义了模块的对外接口以后，其他 JS 文件就可以通过import命令加载这个模块。

```
// main.js
import {firstName, lastName, year} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```
上面代码的import命令，用于加载profile.js文件，并从中输入变量。import命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块（profile.js）对外接口的名称相同。

如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名

```
import { lastName as surname } from './profile';
```
import后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径，.js后缀可以省略。

注意，import命令具有提升效果，会提升到整个模块的头部，首先执行。

```
foo();

import { foo } from 'my_module';
//import的执行早于foo的调用。这种行为的本质是，import命令是编译阶段执行的，在代码运行之前。
```
由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

```
import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```
##### 模块的整体加载
除了指定加载某个输出值，还可以使用整体加载，即用星号（*）指定一个对象，所有输出值都加载在这个对象上面。

注意，模块整体加载所在的那个对象，不允许运行时改变。下面的写法都是不允许的。


```
import * as circle from './circle';

// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```
#### export default
使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。

```
// export-default.js
export default function () {
  console.log('foo');
}
```
其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。

```
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```
**需要注意的是，这时import命令后面，不使用大括号。**
export default命令用在非匿名函数前，也是可以的。

```
// export-default.js
export default function foo() {
  console.log('foo');
}

// 或者写成

function foo() {
  console.log('foo');
}

export default foo;
```
上面代码中，foo函数的函数名foo，在模块外部是无效的。加载的时候，视同匿名函数加载。

下面比较一下默认输出和正常输出。

```
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```
上面代码的两组写法，第一组是使用export default时，对应的import语句不需要使用大括号；第二组是不使用export default时，对应的import语句需要使用大括号。

export default命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此export default命令只能使用一次。所以，import命令后面才不用加大括号，因为只可能唯一对应export default命令。

本质上，export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的。

```
// modules.js
function add(x, y) {
  return x * y;
}
export {add as default};
// 等同于
// export default add;

// app.js
import { default as foo } from 'modules';
// 等同于
// import foo from 'modules';
```
正是因为export default命令其实只是输出一个叫做default的变量，所以它后面不能跟变量声明语句。

```
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 错误
export default var a = 1;
```
上面代码中，export default a的含义是将变量a的值赋给变量default。所以，最后一种写法会报错。

同样地，因为export default命令的本质是将后面的值，赋给default变量，所以可以直接将一个值写在export default之后。

```
// 正确
export default 42;

// 报错
export 42;
```
#### export 与 import 的复合写法
如果在一个模块之中，先输入后输出同一个模块，import语句可以与export语句写在一起。

```
export { foo, bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
export { foo, bar };
```
模块的接口改名和整体输出，也可以采用这种写法。

```
// 接口改名
export { foo as myFoo } from 'my_module';

// 整体输出
export * from 'my_module';
```
****
**以上就是我对ES6总结的笔记了, 断断续续差不多用了一周的时间,很多代码因为我项目中也用不到,都要自己先敲一遍看看用法在总结,在加班上班比较忙, 当然我的笔记是总结的很细的,项目中很多都可以用上面的内容进行优化代码, 希望我的笔记对你也能有所帮助, 如果有错误的地方请你在评论区指出, 非常感谢, 感觉对你有用的话请关注点赞哈!!!**