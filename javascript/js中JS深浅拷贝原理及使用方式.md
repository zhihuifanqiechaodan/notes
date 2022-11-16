深浅拷贝在我们开发项目中是经常使用到的，本篇文章带你详细了解和使用深浅拷贝的原理及使用方式。

首先先了解一下JS中的数据类型
### JS中的数据类型
* Null
* String
* Boolean
* undefined
* Number
* Object
* BigInt(ES10新增)
* Symbol(ES6新增)

```
基本数据类型： undefined、null、Boolean、Number、String  和Symbol(ES6) 还有 BigInt(ES10新增)
引用数据类型： Object(Array, Date, RegExp, Function)
```
### 深浅拷贝
深浅拷贝只是针对引用类型的，因为引用类型是存放在堆内存中，在栈地址有一个或者多个地址来指向推内存的某一数据
### 浅拷贝
被复制对象的所有变量都含有与原来的对象相同的值，且所有对其对象的引用不会指向原来的对象的内存地址。

浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象，也就是说浅拷贝只能拷贝第一层，更加深一层次的对象依然是赋值操作，更改第二层对象的值依旧会改变原对象。

### 深拷贝

深拷贝是一个整个独立的对象拷贝，深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。

深拷贝把要复制的对象所引用的对象都复制了一遍。如果你修改了“副本”的值，那么原来的对象不会被修改，两者是相互独立的。

### JSON实现深拷贝

```
let arr = [0, 1, 2, 3]
let newArr = JSON.parse(JSON.stringify(arr))
newArr[0] = 4
console.log(newArr, arr)
```
结果如图：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/6/1701630744400fef~tplv-t2oaga2asx-image.image)
对比之前赋值的结果，我们发现原来的数组arr并没有被改变，可以说两者是相互独立的
很显然，这就是一个深拷贝的例子

`原理：
JSON.parse() 方法用于将一个 JSON 字符串转换为对象。
JSON.stringify() 方法用于将 JavaScript 值（通常为对象或数组）转换为 JSON 字符串。
在JSON.stringify()完成后，对象就转为了字符串，也就可以说实实在在的复制了一个值，不存在引用之说。
再利用JSON.parse()转为对象，这样达到深拷贝的目的`

### JSON实现深拷贝的缺陷

```
let obj = {
    nul: null,
    und: undefined,
    sym: Symbol('sym'),
    str: 'str',
    bol: true,
    num: 45,
    arr: [1, 4],
    reg: /[0-9]/,
    dat: new Date(),
    fun: function() {},  
}
console.log(JSON.parse(JSON.stringify(obj)))
```
结果如图：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/6/1701631ea6008f30~tplv-t2oaga2asx-image.image)
我们可以发现有些属性被忽略了：

* undefined
* symbol
* function

可以看得出来JSON实现深拷贝也有不足之处。

### ES6新特性实现浅拷贝
* Object.assign()

Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。
Object.assign(target, ...sources)
target目标对象。 sources源对象。 返回的是目标对象
```
let obj = {
    a: '1',
    b: {
        bb: '2'
    }
}

let newObj = Object.assign({}, obj)
newObj.a = '11'
newObj.b.bb = '22'
console.log(newObj, obj)
```
结果如图：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/6/17016386ced4c4f2~tplv-t2oaga2asx-image.image)
我们可以看到a1的值被改变，而b的值没有被改变，说明了：

`Obejct.assign()只能对一层进行深拷贝
如果拷贝的层数超过了一层的话，那么就会进行浅拷贝`

* 展开运算符(...)

对象的扩展运算符（...）用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。

```
let obj = {
    a: '1',
    b: {
        bb: '2'
    }
}

let newObj = {...obj}
newObj.a = '11'
newObj.b.bb = '22'
console.log(newObj, obj)
```
结果如图：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/6/170163a412513076~tplv-t2oaga2asx-image.image)
`扩展运算符只能对一层进行深拷贝
如果拷贝的层数超过了一层的话，那么就会进行浅拷贝`


`原理：
对于引用类型，赋值操作符只是把存放在栈内容中的指针赋值给另外一个变量。
所以在赋值完成后，在栈内存就有两个指针指向堆内存同一个数据。
也就可以说两个变量在共用着同一个数据，这就是浅拷贝。`

### 尾声
我们可以看到Object.assign() 和 展开原算符对于深浅拷贝的结果是一样。

如果拷贝的层数超过了一层的话，那么就会进行浅拷贝


**所以要慎用这两个来进行拷贝!!!**

**一般我们在项目中需要深浅拷贝的数据一般也不会具有以下三种类型**
* undefined
* symbol
* function

**所有正常来说JSON实现拷贝就够用了，如果有特殊需求，我们可以通过自己封装一个深拷贝的函数。**
## ❤️ 看完帮个忙

如果你觉得这篇内容对你挺有启发，我想邀请你帮我个小忙：

1.**点赞**，让更多的人也能看到这篇内容（收藏不点赞，都是耍流氓 -_-）

2.**关注公众号「番茄学前端」**，我会定时更新和发布前端相关信息和项目案例经验供你参考。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/13/1703dfeb7a3b7dc8~tplv-t2oaga2asx-image.image)