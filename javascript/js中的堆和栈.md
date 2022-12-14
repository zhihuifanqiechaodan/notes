学习javascript一般都会了解到数据类型，在js中的数据类型分为基本类型和引用类型，这两种类型的数据分别存栈和堆中。以下内容将初步带你了解栈和堆。

### 栈和堆
* 栈（stack）

栈会自动分配内存空间，会自动释放，存放基本类型，简单的数据段，占据固定大小的空间

```
基本类型包括：
    Number, String, Boolean, Undefined, Null, Symbol(ES6新增)
```
* 堆（heao）

动态分配的内存，大小不定也不会自动释放，存放引用类型，指那些可能由多个值构成的对象。保存在堆中，包含引用类型的变量，实际上保存的不是变量本身，而是指向该对象的指针。
```
引用类型：
    Object, Function, Array, 正则等， 除了基本类型都是引用类型数据。
```
### 栈和堆的区别

* 栈

所有在方法中定义的变量都是存放在栈内存中，随着方法的执行结束，这个方法的内存栈也自然销毁了。

```
优点：
    存取速度比堆快，仅次于直接位于CPU中的寄存器，数据可以共享。
缺点：
    存在栈中的数据大小于生存期是确定的，缺乏灵活性
```
* 堆

堆内存中的对象不会随着方法的结束而销毁，即使这个方法结束后，这个对象还可能被另外一个变量所引用（参数传递）。创建对象是为了反复利用，这个对象将被保存到运行时数据去。

### 栈和堆的溢出
栈：可以递归调用方法，这样随着栈深度的增加，JVM维持着一条长长的方法调用轨迹，知道内存不够分配，产生栈溢出。

堆：循环创建对象，通俗点就是不断的new 一个对象。

### 传值和传址的区别
* 基本类型

```
let a = 1;
let b = a; 
console.log(a === b)
a = 2;
console.log(a, b)
console.log(a === b)
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/3/1709fa6b29856415~tplv-t2oaga2asx-image.image)
`当赋值操作的右侧为基本类型时，直接拷贝其值。`
* 引用类型

```
let obj = {a:1, b: 2, c: 3}
let newObj = obj
newObj.a = 11

console.log(obj, newObj)
console.log(obj === newObj)
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/3/1709fa61364ea638~tplv-t2oaga2asx-image.image)
`当赋值操作的右侧为引用类型时，直接拷贝其址。`
### 深浅拷贝

```
浅拷贝：浅拷贝通过ES6新特性Object.assign()或者通过扩展运算法...来达到浅拷贝的目的，浅拷贝修改
副本，不会影响原数据，但缺点是浅拷贝只能拷贝第一层的数据，且都是值类型数据，如果有引用型数据，修改
副本会影响原数据。

深拷贝：通过利用JSON.parse(JSON.stringify())来实现深拷贝的目的，但利用JSON拷贝也是有缺点的，
当要拷贝的数据中含有undefined/function/symbol类型是无法进行拷贝的，当然我们想项目开发中需要
深拷贝的数据一般不会含有以上三种类型，如有需要可以自己在封装一个函数来实现。
```

## ❤️ 看完帮个忙
如果你觉得这篇内容对你挺有启发，我想邀请你帮我个小忙：

1. **点赞**，让更多的人也能看到这篇内容（收藏不点赞，都是耍流氓 -_-）

2. **关注公众号「番茄学前端」**，我会定时更新和发布前端相关信息和项目案例经验供你参考。

3. **加个好友**， 虽然帮不上你大忙，但是一些业务问题大家可以探讨交流。
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/22/1706c520546b2c2d~tplv-t2oaga2asx-image.image)