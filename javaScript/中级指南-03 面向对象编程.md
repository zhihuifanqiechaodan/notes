````
今天在群里有大佬发了一份杭州二线公司中的某一道面试题, 引起了讨论, 因此我顺便回顾并总结了一下关于面向对象编程的笔记。

```
function Child() {}
function Parent() {
  this.a = 1
}
const c = new Child()
Child.prototype = new Parent()
console.log(c.a) // 答案是什么?
```
## 面向对象 VS 面向过程
在没有学习面向对象的编程时, 我所有的代码都是基于面向过程编程的, 那么面向对象编程和面向过程编程是如何理解呢?
### 面向过程 

```
面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了。
```
但是如果想要复用(例如一个小弹窗)和扩展就比较麻烦, 需要去修改代码才能实现
### 面向对象

```
面向对象是把构成问题事务分解成各个对象，建立对象的目的不是为了完成一个步骤，
而是为了描叙某个事物在整个解决问题的步骤中的行为。
```
例如: 我们封装了一个类(抽象的写成一个类),还是弹窗的例子,我们只需要将触发弹窗的元素和不用的样式传入即可展示不同的弹窗
## 面向对象
### 三大特性
* 封装
* 继承
* 多态
### 关于new 
* 知识点一: 普通函数和new执行函数的区别

```
function fn () {console.log(this)}
fn() // this指向window
new fn() // this指向{}
```
`1. new执行的函数, 函数内部默认生成了一个对象`

`2. 函数内部的this默认指向了这个new生成的对象`

`3. new执行函数生成的这个对象, 是函数的默认返回值`
* 知识点二: 万物皆对象

```
在我之前的JavaScript基础笔记中大家可以去查看js中的七大数据类型
其创建方式有:
let str = '番茄' // 字面量
let str = String('番茄') // 字面量
let str = new String('番茄') // 直接量, 字符串类型对象
// 其余的数据类型大致相同
```
看到了这里大家大概应该理解为什么会说万物皆对象了, 大家可以自己实践去打印一下最顶层的__proto__
### 构造函数 / 类

```
function Person () {} // 行业内默认函数名首字母大写即是构造函数 / 类
let p1 = new Person()
let p2 = new Person()
console.log(p1 === p2) // false 
```
**构造函数new执行返回的对象每次都是一个最新的, 永远不会有相等的情况**
* 私有属性

```
function Person (obj) {
    this.name = obj.name
    this.age = obj.age
} 
let p1 = new Person({name: '番茄', age: 18})
let p2 = new Person({name: '炒蛋', age: 22})
console.log(p1.name) // 番茄
console.log(p2.name) // 炒蛋
```
* 共有属性
```
function Person () {} 
// prototype ===> 原型
Person.prototype = {
    playGame(value) {
        console.log(value)
    }
}
let p1 = new Person()
let p2 = new Person()
p1.playGame('打篮球') // 打篮球
p1.playGame('踢足球') // 踢足球
```
通过上述列子, 可以得出一个结论, 在构造函数Person身上的prototype也就是原型中的方法, 是大家共有的方法, Person的实例化对象都可以调用。
* 高大上的名字

```
function Person () {}   // 构造函数或者类
Person.prototype        // 构造函数的原型
new Person()            // 构造函数的实例化或者类的实例化
let p1 = new Person()   // 实例或者Person的实例化对象
```
### 原型
原型本质是一个JSON格式的对象{}

**考虑一个问题,对象是被这个对象对应的构造函数/类实例出来的, 那么原型也是个对象,原型这个对象也是这个原型对象对应的类的实例出来的, 由此而延伸出原型链**

哈哈 有没有一种鸡生蛋,蛋生鸡的赶脚~~
### 原型的扩展

```
function Person () {}
// 方法一
Person.prototype.add = function () {}
// 方法二
Person.prototype = {
    // 注意如果通过等号赋值扩展原型, 一定要把constructor属性指向当前构造函数 / 类
    constructor: Person, 
    add () {}
}
```
### 继承
* 私有属性的继承是模仿一个相似的
* 原型的继承是指同一个
#### ES5构造函数的写法
```
 function Person (name, age) {
    this.name = name
    this.age = age
 }
 Person.prototype = {
    constructor: Person,
    add (){},
    sub (){},
    ......
 }
 const obj = new Person('番茄', 20)
```
#### ES5构造函数的继承方法
关于继承可以理解为 "儿子继承老子, 老子有的儿子都有"
* 方案一(有缺陷)

```
function Person(obj) {
    this.name = obj.name
    this.age = obj.age
}
Person.prototype.add = function(value){
    console.log(value)
}
var p1 = new Person({name:"番茄", age: 18})

function Person1(obj) {
    Person.call(this, obj)
    this.sex = obj.sex
}
// 注意犹豫原型是对象,引用型数据,Person1原型的扩展会导致Person的原型也出现扩展的方法
Person1.prototype = Person.prototype 
Person1.prototype.play = function(value){
    console.log(value)
}
var p2 = new Person1({name:"鸡蛋", age: 118, sex: "男"})
```
**方案一 : 实现继承, 但是子类的原型扩展父类也可以用,不合适PASS**
* 方案二(有缺陷)
```
function Person(obj) {
    this.name = obj.name
    this.age = obj.age
}
Person.prototype.add = function(value){
    console.log(value)
}
var p1 = new Person({name:"番茄", age: 18})

function Person1(obj) {
    Person.call(this, obj)
    this.sex = obj.sex
}
Person1.prototype = new Person({})
Person1.prototype.play = function(value){
    console.log(value)
}
var p2 = new Person1({name:"鸡蛋", age: 118, sex: "男"})
```
**方案二 : 实现继承, 并且子类原型扩展不会影响父类原型, 但是子类的原型上出现了父类的属性,并且值为undefined, 不合适PASS**
* 方案三(解决方案)

借助一个无用的构造函数
```
function Person(obj) {
    this.name = obj.name
    this.age = obj.age
}
Person.prototype.add = function(value){
    console.log(value)
}
var p1 = new Person({name:"番茄", age: 18})

function Person1(obj) {
    Person.call(this, obj)
    this.sex = obj.sex
}
// 这是一个无用的构造函数, 没有私有属性
function Person2(){}
Person2.prototype = Person.prototype
// 这是一个无用的构造函数, 它的原型等于Person的原型
Person1.prototype = new Person2()
Person1.prototype.play = function(value){
    console.log(value)
}
var p2 = new Person1({name:"鸡蛋", age: 118, sex: "男"})
```
* 方案四(完美解决方案)

借助寄生组合继承
```
function Person(obj) {
    this.name = obj.name
    this.age = obj.age
}
Person.prototype.add = function(value){
    console.log(value)
}
var p1 = new Person({name:"番茄", age: 18})

function Person1(obj) {
    Person.call(this, obj)
    this.sex = obj.sex
}
Person1.prototype = Object.create(Person.prototype)
Person1.prototype.play = function(value){
    console.log(value)
}
var p2 = new Person1({name:"鸡蛋", age: 118, sex: "男"})
```
#### ES6构造函数的写法

```
// 通过class关键字声明
class Person {
    // 私有属性(当然这不是真正意义上的私有属性, 只是为了区分)
    constructor(name, age) {
        this.name = name
        this.age = age
    }
    // 公有方法, 注意ES6写法中, constructor之外的方法都是公有方法, 且不允许定义属性。ES5写法是可以的。
    add () {}
    sub () {}
}
const obj = new Person('番茄', 18) // 注意ES6写法必须通过new执行, 否则报错
```
#### ES6构造函数的继承方法

```
class Parent {
    constructor(data){
        this.name = data.name
    }
    add (value) {
        console.log(value)
    }
}
// 子类继承Person
class Person extends Parent {
    constructor(data) {
        super(data) // 子类继承父类, 必须在子类的constructor方法中调用super方法
        this.age = data.age
    }
    play (value) {
        console.log(value)
    }
}
let p1 = new Parent({name: "番茄"})
let p2 = new Person({name: "番茄", age: 18})
```
实例对象的__proto__和构造函数的prototype的关系
```
function Person () {}
Person.prototype.add = function () {}
const p1 = new Person()

// 仔细观察这个对象会发现
console.log(p1) 
// 实例的__proto__等于构造函数的prototype
console.log(p1.__proto__ === Person.prototype) 
```
**注意: 原型的扩展一定要在实例化之前**

**得出结论: 实例的__proto__指向了这个实例对应的类的prototype**
### for in (题外话)
for in 遍历性能极其的差, 原因就是它在遍历的过程中会遍历原型上的属性
### hasOwnProperty(题外话)

```
console.log(实例.hasOwnProperty('属性名')) // 判断是否是原型上的属性
```
### 关于对象的深浅拷贝(题外话)
* 方案一 ( 支持 {}, [], function拷贝 )
```
// data要拷贝的数组或者对象, deep是否开启深浅拷贝
// 支持函数拷贝, 因为函数一般在定义的时候就已经规定了好了执行的内容, 只是传参的不同
function extend(data, deep) {
    // deep true  深拷贝, false 浅拷贝
    var obj = {}
    // 判断要拷贝的数据是不是数组
    if (data instanceof Array) {
        obj = []
    }
    for (let key in data) {
        let value = data[key]
        // 确定value是不是引用型数据, 前提deep 是true, 并且value的类型为对象, 且不为null
        //  deep不传为undefined, 双重取反即为false, 默认浅拷贝
        obj[key] = (!!deep && typeof value === 'object' && value !== null) ? extend(value, deep) : value
    }
    return obj
}
```
* 方案二 ( 支持 {}, [] )

简单粗暴的方法, 但是它不支持函数
```
var obj = {
    a: 1,
    b: {
        bb: 1,
        cc: 2
    }
}
var obj2 = JSON.parse(JSON.stringify(obj)) // 数组也一样
```xxxxxxxxxx var obj = {    a: 1,    b: {        bb: 1,        cc: 2    }}var obj2 = JSON.parse(JSON.stringify(obj)) // 数组也一样
````