目前社区有很多 Vue.js 的源码解析文章，很多大牛写的都非常详细，但说到底。光看文章自己不去研究源码和总结笔记，终究不会深入了解和记忆。

本篇文章将自己研究 Vue.js源码的一些内容做成笔记并且记录下来。加深印象和理解，俗话说读书百遍不如手写一遍。

### 理解什么是MVVM模式？
`MVC`模式是指用户操作会请求服务端路由，路由会调用对应的控制器来处理,控制器会获取数据。将结果返回给前端,页面重新渲染。并且前端会将数据手动的操作DOM渲染到页面上，非常消耗性能。

虽然没有完全遵循 [MVVM 模型](https://zh.wikipedia.org/wiki/MVVM)，但是 Vue 的设计也受到了它的启发。Vue中则不再需要用户手动操作DOM元素，而是将数据绑定到`viewModel`层上，数据会自动渲染到页面上，视图变化会通知`viewModel层`更新数据。`ViewModel`就是我们`MVVM`模式中的桥梁.
****
### Vue中响应式数据的原理是什么（2.x版本）？
Vue2.x版本响应式数据的原理是 [Object.defineProperty](https://juejin.im/post/6844903949087014926)(Es6笔记中有详细介绍)

Vue在初始化的时候，也就是new Vue()的时候，会调用底层的一个initData()方法，方法中有一个observe()会将初始化传入的data进行数据响应式控制，其中会对data进行一系列操作，判断是否已经被观测过。`判断观测的数据是对象还是数组。`

#### 观测的数据是对象
倘若观测的是一个对象，会调用一个walk()方法其内部内就是调用Object.defineProperty进行观测，倘若对象内部的属性还是一个对象的话，就会进行递归观测。

这时当对当前对象取值的时候就会调用get方法，get方法中就进行依赖收集(watcher),如果对当前对象进行赋值操作，就会调用set方法，set方法中会判断新旧值是否不一样，不一样就会调用一个notify方法去触发数据对应的依赖收集进行更新。

#### 观测的数据是一个数组
倘若观测的是一个数组，数组不会走上面的方法进行依赖收集，Vue底层重写了数组的原型方法，当前观测的是数组时，Vue将数组的原型指向了自己定义的原型方法。并且只拦截了以下7个数组的方法。

```
// 因为只有以下7中数组方法才会去改变原数组。
push, pop, shift, unshift, splice, sort, reverse
```
原型方法内部采用的是函数劫持的方式，如果用户操作的是以上7中数组方法，就会走Vue重写的数组方法。这时候就可以在数组发生变化时候，去手动调用notify方法去更新试图。

`当然在对数据进行数据更新的时候，也会对新增的数据进行依赖收集观测。`

`如果数组中的数据也是对象，它会继续调用Object.defineProperty对其进行观测。`

**知道以上内容，你就可以理解为何数组通过下标修改数据，数据变化了但是视图没有更新的原因。**

**观测对象核心代码**

```
Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      const value = getter ? getter.call(obj) : val
      if (Dep.target) {
        dep.depend() // ** 收集依赖 ** /
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            dependArray(value)
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      const value = getter ? getter.call(obj) : val
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      val = newVal
      childOb = !shallow && observe(newVal)
      dep.notify() /** 通知相关依赖进行更新 **/
    }
  })
```
**观测数组核心代码**

```
const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
methodsToPatch.forEach(function (method) { // 重写原型方法
  const original = arrayProto[method] // 调用原数组的方法
  def(arrayMethods, method, function mutator (...args) {
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // notify change
    ob.dep.notify() // 当调用数组方法后，手动通知视图更新
    return result
  })
})

this.observeArray(value) // 进行深度监控
```
**以上内容最好下载Github上Vue源码一起看。**
****
### Vue采用异步渲染的原因是什么？
`首先我们要知道Vue是组件级更新。`

当我们操作某个组件中的方法进行数据更新的时候，例如

```
data() {
    return {
        msg: 'hello word',
        name: '只会番茄炒蛋'
    }
}
methods:{
    add() {
        this.msg = '我更改了 => hello word'
        this.name = '我更改了 => 只会番茄炒蛋'
    }
}
```
倘若一旦更改数据就进行视图的渲染(以上更改了两次数据)，必然会影响性能，因此Vue采用异步渲染方式，也就是多个数据在一个事件中同时被更改了，同一个watcher被多次触发，只会被推入到队列中一次。当最后数据被更改完毕以后调用nexttick方法去异步更新视图。

`内部还有一些其他的操作，例如添加 watcher 的时候给一个唯一的id, 更新的时候根据 id 进行一个排序，更新完毕还会调用对应的生命周期也就是 beforeUpdate 和 updated 方法等。`

**以上内容最好下载Github上Vue源码一起看。**
****
### Vue中nextTick实现原理是什么?
在了解nextTick实现原理之前，你需要掌握什么Event Loop，并且了解微任务和宏任务，这里我简单介绍一下。

#### Event Loop
大家也知道了当我们执行 JS 代码的时候其实就是往执行栈中放入函数，那么遇到异步代码的时候该怎么办？其实当遇到异步的代码时，会被挂起并在需要执行的时候加入到 Task（有多种 Task） 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行，所以本质上来说 JS 中的异步还是同步行为。

不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（microtask） 和 宏任务（macrotask）。在 ES6 规范中，microtask 称为 jobs，macrotask 称为 task。

`微任务包括 process.nextTick ，promise.then ，MutationObserver，其中 process.nextTick 为 Node 独有。`

`宏任务包括 script ， setTimeout ，setInterval ，setImmediate ，I/O ，UI rendering。`

**简单了解 Event Loop 之后继续学习 Vue 中 nextTick 实现原理**

Vue 在内部对异步队列尝试使用原生的 Promise.then、MutationObserver 和 setImmediate，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替。

`官方原话`

当你设置vm.someData = 'new value'，该组件不会立即重新渲染。当刷新队列时，组件会在下一个事件循环“tick”中更新。多数情况我们不需要关心这个过程，但是如果你想基于更新后的 DOM 状态来做点什么，这就可能会有些棘手。虽然 Vue.js 通常鼓励开发人员使用“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们必须要这么做。为了在数据变化之后等待 Vue 完成更新 DOM，可以在数据变化之后立即使用 Vue.nextTick(callback)。这样回调函数将在 DOM 更新完成后被调用。

`总结：nextTick方法主要是使用了宏任务和微任务,定义了一个异步方法，多次调用nextTick会将方法存入队列中，通过这个异步方法清空当前队列。 所以这个nextTick方法就是异步方法`


**nextTick原理核心代码**
```
let timerFunc  // 会定义一个异步方法
if (typeof Promise !== 'undefined' && isNative(Promise)) {  // promise
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && ( // MutationObserver
  isNative(MutationObserver) ||
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' ) { // setImmediate
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  timerFunc = () => {   // setTimeout
    setTimeout(flushCallbacks, 0)
  }
}
// nextTick实现
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
}
```
**以上内容最好下载Github上Vue源码一起看。**
****
### Vue中 computed 的原理
一般面试题中都问会问到 computed 和 watch 的区别，实际上 computed 和 watch 的原理都是使用watcher实现的，而他俩的区别就是 computed 具有缓存的功能。

#### computed 缓存功能
当我们默认初始化创建计算属性的时候，它会创建一个watcher, 并且这个watcher有两个个属性`lazy:true，dirty: true`, 也就是说当创建一个计算属性的时候，默认是不执行的，只有当用户取值的时候(也就是在组件上使用的时候)，它会判断如果`dirty: true`的话就会让这个watcher执行去取值，并且在求值结束后，更改`dirty: false`，这样当你再次使用这个计算属性的时候，判断条件走到`dirty: false`的时候，就不在执行watcher求值操作，而是直接返回上次求值的结果。

**那么什么时候会重新计算求职呢？**

只有当计算属性的值发生变化的时候，它会调用对应的update方法，然后更改`dirty: true`，然后执行的时候根据条件重新执行watcher求值。

**computed原理核心代码**

```
function initComputed (vm: Component, computed: Object) {
  const watchers = vm._computedWatchers = Object.create(null)
  const isSSR = isServerRendering()
  for (const key in computed) {
    const userDef = computed[key]
    const getter = typeof userDef === 'function' ? userDef : userDef.get
    if (!isSSR) {
      // create internal watcher for the computed property.
      watchers[key] = new Watcher(
        vm,
        getter || noop,
        noop,
        computedWatcherOptions
      )
    }

    // component-defined computed properties are already defined on the
    // component prototype. We only need to define computed properties defined
    // at instantiation here.
    if (!(key in vm)) {
      defineComputed(vm, key, userDef)
    } else if (process.env.NODE_ENV !== 'production') {
      if (key in vm.$data) {
        warn(`The computed property "${key}" is already defined in data.`, vm)
      } else if (vm.$options.props && key in vm.$options.props) {
        warn(`The computed property "${key}" is already defined as a prop.`, vm)
      }
    }
  }
}
function createComputedGetter (key) {
  return function computedGetter () {
    const watcher = this._computedWatchers && this._computedWatchers[key]
    if (watcher) {
      if (watcher.dirty) { // 如果依赖的值没发生变化,就不会重新求值
        watcher.evaluate()
      }
      if (Dep.target) {
        watcher.depend()
      }
      return watcher.value
    }
  }
}
```
**以上内容最好下载Github上Vue源码一起看。**
****
### Watch中的 deep : true 是如何实现的？
Vue官方关于watch的介绍
* 类型：`{ [key: string]: string | Function | Object | Array }`

* 详细：`一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 $watch()，遍历 watch 对象的每一个属性。`

通常我们在项目中一般使用watch来监听路由或者data中的属性发生变化时作出对应的处理方式。

那么deep : true的使用场景就是当我们监测的属性是一个对象的时候，我们会发现watch中监测的方法并没有执行，原因是受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

deep的意思就是深入观察，监听器会一层层的往下遍历，给对象的所有属性都加上这个监听器，但是这样性能开销就会非常大了，任何修改obj里面任何一个属性都会触发这个监听器里的 handler。

这时候我们可以优化这个问题，通过以下方式

```
// 使用字符串形式监听具体对象中的某个值。
watch: {
  'obj.a': {
    handler(newName, oldName) {
      console.log('obj.a changed');
    },
    immediate: true, // 立即执行一次handler方法
    deep: true // 深度监测
  }
} 
```
需要注意的是，当我们通过下标去修改数组中某个值的时候，也不会引起watch的变化，原理请看上面`Vue中响应式数据的原理是什么?`

当然除了改变数组的方法可以进行监测数组变化，Vue也提供来Vue.set()方法。

**Watch中的 deep : true 核心代码**

```
get () {
    pushTarget(this) // 先将当前依赖放到 Dep.target上
    let value
    const vm = this.vm
    try {
      value = this.getter.call(vm, vm)
    } catch (e) {
      if (this.user) {
        handleError(e, vm, `getter for watcher "${this.expression}"`)
      } else {
        throw e
      }
    } finally {
      if (this.deep) { // 如果需要深度监控
        traverse(value) // 会对对象中的每一项取值,取值时会执行对应的get方法
      }
      popTarget()
    }
    return value
}
function _traverse (val: any, seen: SimpleSet) {
  let i, keys
  const isA = Array.isArray(val)
  if ((!isA && !isObject(val)) || Object.isFrozen(val) || val instanceof VNode) {
    return
  }
  if (val.__ob__) {
    const depId = val.__ob__.dep.id
    if (seen.has(depId)) {
      return
    }
    seen.add(depId)
  }
  if (isA) {
    i = val.length
    while (i--) _traverse(val[i], seen)
  } else {
    keys = Object.keys(val)
    i = keys.length
    while (i--) _traverse(val[keys[i]], seen)
  }
}
```
**以上内容最好下载Github上Vue源码一起看。**
****
### Vue中的生命周期
附上Vue官方关于生命周期的介绍图
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/23/1710542df4c283eb~tplv-t2oaga2asx-image.image)

#### 每个生命周期函数在什么时候调用？
* beforeCreate(){}

```
在实例初始化以后，数据观测(data observe)之前进行调用，此时获取不到data中的数据。
```
* created(){}

```
在实例创建完成之后调用，这时候实例已经完成了数据观测(data observe)，属性和方法的运算，watch/event 事件回调。
注意：这里没有$el
```
* beforeMount(){}

```
在挂载之前调用，相关的render函数首次被调用。
```
* mounted(){}

```
el绑定的元素被内部新创建的$el替换掉，并且挂载到实例上去之后调用。
```
* beforeUpdate(){}

```
数据更新时调用，发生在虚拟DOM重新渲染和打补丁之前。
```
* updated(){}

```
由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
```
* beforeDestroy(){}

```
实例销毁之前调用。在这一步，实例仍然完全可用
```
* destroyed(){}

```
Vue实例销毁后调用。调用后，Vue实例指示的所有东西都会解绑定，所有的事件监听器会被移除。
所有的子实例也会被销毁，该钩子在服务器端渲染期间不被调用。
```
#### 常用生命周期内部可以做的事情。
* created(){}

```
通常我们在项目中会在created(){}生命周期中去调用ajax进行一些数据资源的请求，但是由于当前生命周期无法操作DOM
所以一般在项目中，所有的请求我都会统一放到mounted(){}生命周期中。
```
* mounted(){}

```
在当前生命周期中，实例已经挂载完成，通常我会将ajax请求放到这个生命周期函数中。
如果有一些需要根据获取的数据并去初始化DOM操作，在这里是最佳方案。
```
* beforeUpdate(){}

```
可以在这个生命周期函数中进一步地更改状态，这不会触发附加的重渲染过程
```
* updated(){}

```
可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致更新无限循环。 
该钩子在服务器端渲染期间不被调用。
```
* destroyed(){}

```
可以执行一些优化操作,清空定时器，解除绑定事件
```

**通过以上描述我们可以总结出以下结论**

1. ajax请求放在通常放在created(){}或者mounted(){}生命周期中。 并且在created的时候，视图中的`dom`并没有渲染出来，所以此时如果直接去操`dom`节点，无法找到相关的元素，在mounted中，由于此时`dom`已经渲染出来了，所以可以直接操作`dom`节点 ，一般情况下都放到`mounted`中,保证逻辑的统一性,因为生命周期是同步执行的，`ajax`是异步执行的。

    **注意：服务端渲染不支持mounted方法，所以在服务端渲染的情况下统一放到created中**

2. 倘若当前组件中有定时器，使用了$on方法，绑定 `scroll mousemove`等事件，需要在beforeDestroy钩子中去清除。
****
### Vue中模板编译原理 
查看源码后发现，Vue在底层会调用一个parseHTML方法将模版转为AST语法树(内部通过正则走一些方法)，最后将AST语法树转为render函数(渲染函数)，渲染函数结合数据生成Virtual DOM树，Diff和Patch后生成新的UI。

#### 关于虚拟DOM
Vue的编译器在编译模板之后，会把这些模板编译成一个渲染函数。而函数被调用的时候就会渲染并且返回一个虚拟DOM的树。当我们有了这个虚拟的树之后，再交给一个Patch函数，负责把这些虚拟DOM真正施加到真实的DOM上。

在这个过程中，Vue有自身的响应式系统来侦测在渲染过程中所依赖到的数据来源。在渲染过程中，侦测到数据来源之后就可以精确感知数据源的变动。到时候就可以根据需要重新进行渲染。当重新进行渲染之后，会生成一个新的树，将新的树与旧的树进行对比，就可以最终得出应施加到真实DOM上的改动。

最后再通过Patch函数施加改动。简单点讲，在Vue的底层实现上，Vue将模板编译成虚拟DOM渲染函数。结合Vue自带的响应系统，在应该状态改变时，Vue能够智能地计算出重新渲染组件的最小代价并应到DOM操作上。

实际上这一部分的源码还是比较多的。这里我简单的理解了一些。
****
### 关于v-if和v-show的区别以及底层是如何实现的。
这里我简单描述一下两者的区别
* v-if

```
如果当前条件判断不成立，那么当前指令所在节点的DOM元素不会渲染
```
* v-show

```
当前指令所在节点的DOM元素始终会被渲染，只是根据当前条件去动态改变 display: none || block
从而达到DOM元素的显示和隐藏。
```
#### 底层实现方式
* v-if

Vue底层封装了一些特殊的方法，代码位于此处。 vue/packages/weex-vue-framework/factory.js 
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/23/17107c29513818d4~tplv-t2oaga2asx-image.image)

```
VueTemplateCompiler.compile(`<div v-if="true"><span v-for="i in 3">hello</span></div>`);

with(this) {
    return (true) ? _c('div', _l((3), function (i) {
        return _c('span', [_v("hello")])
    }), 0) : _e() // _e()方法创建一个空的虚拟dom等等。
}
```
通过上述代码，可以得知如果当前条件判断不成立，那么当前指令所在节点的DOM元素不会渲染
* v-show

v-show编译出来里面没有任何东西，只有一个directives，它里面有一个指令叫做v-show
```
VueTemplateCompiler.compile(`<div v-show="true"></div>`);
/**
with(this) {
    return _c('div', {
        directives: [{
            name: "show",
            rawName: "v-show",
            value: (true),
            expression: "true"
        }]
    })
}
```
只有在运行的时候它会去处理这个指令，代码如下：

```
// v-show 操作的是样式  定义在platforms/web/runtime/directives/show.js
bind (el: any, { value }: VNodeDirective, vnode: VNodeWithData) {
    vnode = locateNode(vnode)
    const transition = vnode.data && vnode.data.transition
    const originalDisplay = el.__vOriginalDisplay =
      el.style.display === 'none' ? '' : el.style.display
    if (value && transition) {
      vnode.data.show = true
      enter(vnode, () => {
        el.style.display = originalDisplay
      })
    } else {
      el.style.display = value ? originalDisplay : 'none'
    }
}
```
通过源码我们可以清晰的看到它是在操作DOM的display属性。
****
### 在项目中 v-for 为什么不能和 v-if 连用
同样可以通过观看源码就知道原因

```
VueTemplateCompiler.compile(`<div v-if="false" v-for="i in 3">hello</div>`);

with(this) {
    return _l((3), function (i) {
        return (false) ? _c('div', [_v("hello")]) : _e()
    })
}
```
我们知道 v-for 的优先级比 v-if 高，那么在编译阶段会发现他给内部的每一个元素都加了 v-if，这样在运行的阶段会走验证，这样非常的消耗性能。因此在项目中我们要避免这样的操作。

当然如果我们有这样的需求的话也是可以实现的。

我们可以通过计算属性来达到目的

```
<div v-for="i in computedNumber">hello</div>

export default {
    data() {
        return {
            arr: [1, 2, 3]
        }
    },
    computed: {
        computedNumber() {
            return arr.filter(item => item > 1)
        }
    }
}
```
关于解析指令的源码，建议大家也去看看源码的实现过程
****
### 关于虚拟DOM的实现过程

在我理解来说，就是用一个对象来描述我们的虚拟DOM结构，例如：

```
<div id="container">
    <p></p>
</div>

// 简单用对象来描述的虚拟DOM结构
let obj = {
    tag: 'div',
    data: {
        id: "container"
    },
    children: [
        {
            tag: 'p',
            data: {},
            children: {}
        }
    ]
}
```
当然在Vue中的实现是比较复杂的，我这里添加来一些注视方便理解

```
const SIMPLE_NORMALIZE = 1
const ALWAYS_NORMALIZE = 2

function createElement (context, tag, data, children, normalizationType, alwaysNormalize) {

    // 兼容不传data的情况
    if (Array.isArray(data) || isPrimitive(data)) {
        normalizationType = children
        children = data
        data = undefined
    }

    // 如果alwaysNormalize是true
    // 那么normalizationType应该设置为常量ALWAYS_NORMALIZE的值
    if (alwaysNormalize) normalizationType = ALWAYS_NORMALIZE
        // 调用_createElement创建虚拟节点
        return _createElement(context, tag, data, children, normalizationType)
    }

    function _createElement (context, tag, data, children, normalizationType) {
        /**
        * 如果存在data.__ob__，说明data是被Observer观察的数据
        * 不能用作虚拟节点的data
        * 需要抛出警告，并返回一个空节点
        * 
        * 被监控的data不能被用作vnode渲染的数据的原因是：
        * data在vnode渲染过程中可能会被改变，这样会触发监控，导致不符合预期的操作
        */
        if (data && data.__ob__) {
            process.env.NODE_ENV !== 'production' && warn(
            `Avoid using observed data object as vnode data: ${JSON.stringify(data)}\n` +
            'Always create fresh vnode data objects in each render!',
            context
            )
            return createEmptyVNode()
        }

        // 当组件的is属性被设置为一个falsy的值
        // Vue将不会知道要把这个组件渲染成什么
        // 所以渲染一个空节点
        if (!tag) {
            return createEmptyVNode()
        }

        // 作用域插槽
        if (Array.isArray(children) && typeof children[0] === 'function') {
            data = data || {}
            data.scopedSlots = { default: children[0] }
            children.length = 0
        }

        // 根据normalizationType的值，选择不同的处理方法
        if (normalizationType === ALWAYS_NORMALIZE) {
            children = normalizeChildren(children)
        } else if (normalizationType === SIMPLE_NORMALIZE) {
            children = simpleNormalizeChildren(children)
        }
        let vnode, ns

        // 如果标签名是字符串类型
        if (typeof tag === 'string') {
            let Ctor
            // 获取标签名的命名空间
            ns = config.getTagNamespace(tag)

            // 判断是否为保留标签
            if (config.isReservedTag(tag)) {
                // 如果是保留标签,就创建一个这样的vnode
                vnode = new VNode(
                    config.parsePlatformTagName(tag), data, children,
                    undefined, undefined, context
                )

                // 如果不是保留标签，那么我们将尝试从vm的components上查找是否有这个标签的定义
            } else if ((Ctor = resolveAsset(context.$options, 'components', tag))) {
                // 如果找到了这个标签的定义，就以此创建虚拟组件节点
                vnode = createComponent(Ctor, data, context, children, tag)
            } else {
                // 兜底方案，正常创建一个vnode
                vnode = new VNode(
                    tag, data, children,
                    undefined, undefined, context
                )
            }

        // 当tag不是字符串的时候，我们认为tag是组件的构造类
        // 所以直接创建
        } else {
            vnode = createComponent(tag, data, context, children)
        }

        // 如果有vnode
        if (vnode) {
            // 如果有namespace，就应用下namespace，然后返回vnode
            if (ns) applyNS(vnode, ns)
            return vnode
        // 否则，返回一个空节点
        } else {
            return createEmptyVNode()
        }
    }
}
```
**以上内容最好下载Github上Vue源码一起看。**
****
### diff算法的时间复杂度，以及Vue中 diff 算法原理
算法方面不是很了解，这边也只是简单看视频和文章介绍描述一下。

 两个树的完全的`diff`算法是一个时间复杂度为 `O(n3) `,`Vue`进行了优化·*O(n3)* *复杂度*的问题转换成 O(n) *复杂度*的问题(只比较同级不考虑跨级问题)  在前端当中， 你很少会跨越层级地移动Dom元素。 所以 Virtual Dom只会对同一个层级的元素进行对比。 

 #### Vue中 diff 算法原理 (B站公开课视频及Vue源码)
 - 1.先同级比较，在比较子节点
- 2.先判断一方有儿子一方没儿子的情况 
- 3.比较都有儿子的情况
- 4.递归比较子节点


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/23/17107ec05b9eded2~tplv-t2oaga2asx-image.image)
**自我理解**
```
第一种情况：同级比较
当新节点和旧节点不相同情况，新节点直接替换旧节点。

第二种情况：同级比较，节点一致，但一方有子节点，一方没有
当新旧节点相同情况下，如果新节点有子节点，但旧节点没有，那么旧会直接将新节点的子节点插入到旧节点中。
当新旧节点相同情况下，如果新节点没有子节点，但旧节点有子节点，那么旧节点会直接删除子节点。

第三种情况：新旧节点相同，且都有子节点。(这时候旧用到了上图双指针比较方法。)
情况一：
    旧：1234
    新：12345
    当前双指针指向新旧1，1和4，5，判断首部节点一致，指针向后移继续判断，直到最后一项不相同，将新5插入到旧4后面。
    
情况二：
    旧：1234
    新：01234
    当前双指针指向新旧1，0和4，4 发现不想等时会从最后的指针查看，这时候发现相同后，会从后面往前移动指针进行判断。直到到达首部，将新0插入到旧1之前。

情况三：
    旧：1234
    新：4123
    当前发现头部和头部不想等，并且尾部和尾部不想等的时候，就混进行头尾/尾头的互相比较。这时候发现旧的4在新的第一位，就会将自己的4调整到1的前面。
    
情况四：
    旧：1234
    新：2341
    当前发现头部和头部不想等，并且尾部和尾部不想等的时候，就混进行头尾/尾头的互相比较。这时候发现旧的1在新的第四位，就会将自己的1调整到4的后面。
    
特殊情况五：（也就是我们循环数组时候需要加key值的原因）
    旧：1234
    新：2456
    这时候递归遍历会拿新的元素的Key去旧的比较然后移动位置，如果旧的没有就直接将新的放进去，反之将旧的中有，新的没有的元素删除掉。
```
通过上述内容我们大致旧了解diff算法的一部分了。

**核心源码**

core/vdom/patch.js
```
const oldCh = oldVnode.children // 老的儿子 
const ch = vnode.children  // 新的儿子
if (isUndef(vnode.text)) {
    if (isDef(oldCh) && isDef(ch)) {
        // 比较孩子
        if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue, removeOnly)
    } else if (isDef(ch)) { // 新的儿子有 老的没有
        if (isDef(oldVnode.text)) nodeOps.setTextContent(elm, '')
        addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue)
    } else if (isDef(oldCh)) { // 如果老的有新的没有 就删除
        removeVnodes(oldCh, 0, oldCh.length - 1)
    } else if (isDef(oldVnode.text)) {  // 老的有文本 新的没文本
        nodeOps.setTextContent(elm, '') // 将老的清空
    }
} else if (oldVnode.text !== vnode.text) { // 文本不相同替换
    nodeOps.setTextContent(elm, vnode.text)
}
```


```
function updateChildren (parentElm, oldCh, newCh, insertedVnodeQueue, removeOnly) {
    let oldStartIdx = 0
    let newStartIdx = 0
    let oldEndIdx = oldCh.length - 1
    let oldStartVnode = oldCh[0]
    let oldEndVnode = oldCh[oldEndIdx]
    let newEndIdx = newCh.length - 1
    let newStartVnode = newCh[0]
    let newEndVnode = newCh[newEndIdx]
    let oldKeyToIdx, idxInOld, vnodeToMove, refElm

    // removeOnly is a special flag used only by <transition-group>
    // to ensure removed elements stay in correct relative positions
    // during leaving transitions
    const canMove = !removeOnly

    if (process.env.NODE_ENV !== 'production') {
      checkDuplicateKeys(newCh)
    }

    while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
      if (isUndef(oldStartVnode)) {
        oldStartVnode = oldCh[++oldStartIdx] // Vnode has been moved left
      } else if (isUndef(oldEndVnode)) {
        oldEndVnode = oldCh[--oldEndIdx]
      } else if (sameVnode(oldStartVnode, newStartVnode)) {
        patchVnode(oldStartVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
        oldStartVnode = oldCh[++oldStartIdx]
        newStartVnode = newCh[++newStartIdx]
      } else if (sameVnode(oldEndVnode, newEndVnode)) {
        patchVnode(oldEndVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
        oldEndVnode = oldCh[--oldEndIdx]
        newEndVnode = newCh[--newEndIdx]
      } else if (sameVnode(oldStartVnode, newEndVnode)) { // Vnode moved right
        patchVnode(oldStartVnode, newEndVnode, insertedVnodeQueue, newCh, newEndIdx)
        canMove && nodeOps.insertBefore(parentElm, oldStartVnode.elm, nodeOps.nextSibling(oldEndVnode.elm))
        oldStartVnode = oldCh[++oldStartIdx]
        newEndVnode = newCh[--newEndIdx]
      } else if (sameVnode(oldEndVnode, newStartVnode)) { // Vnode moved left
        patchVnode(oldEndVnode, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
        canMove && nodeOps.insertBefore(parentElm, oldEndVnode.elm, oldStartVnode.elm)
        oldEndVnode = oldCh[--oldEndIdx]
        newStartVnode = newCh[++newStartIdx]
      } else {
        if (isUndef(oldKeyToIdx)) oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx)
        idxInOld = isDef(newStartVnode.key)
          ? oldKeyToIdx[newStartVnode.key]
          : findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx)
        if (isUndef(idxInOld)) { // New element
          createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
        } else {
          vnodeToMove = oldCh[idxInOld]
          if (sameVnode(vnodeToMove, newStartVnode)) {
            patchVnode(vnodeToMove, newStartVnode, insertedVnodeQueue, newCh, newStartIdx)
            oldCh[idxInOld] = undefined
            canMove && nodeOps.insertBefore(parentElm, vnodeToMove.elm, oldStartVnode.elm)
          } else {
            // same key but different element. treat as new element
            createElm(newStartVnode, insertedVnodeQueue, parentElm, oldStartVnode.elm, false, newCh, newStartIdx)
          }
        }
        newStartVnode = newCh[++newStartIdx]
      }
    }
    if (oldStartIdx > oldEndIdx) {
      refElm = isUndef(newCh[newEndIdx + 1]) ? null : newCh[newEndIdx + 1].elm
      addVnodes(parentElm, refElm, newCh, newStartIdx, newEndIdx, insertedVnodeQueue)
    } else if (newStartIdx > newEndIdx) {
      removeVnodes(oldCh, oldStartIdx, oldEndIdx)
    }
  }
```
****
### v-for中为什么要用key
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/25/17110dcc128dd6fb~tplv-t2oaga2asx-image.image)
通过上图以及diff算法的第五种复杂情况可以得知原因。

例如当我们利用for循环出三个chenckbox, 当我们通过一个按钮将当前循环数组的第一项删除的时候，会发现第一项依旧是选中状态，而最后一项被删除了，原因就是diff的过程中。当对比新旧虚拟dom的时候，发现DOM一致，这时候内部复用了当前要删除的第一项DOM（内容会是要现实的内容，而不是删除的内容），做完比对后，将旧dom最后一项删除了。

```
1 （1是选中状态）                1 （1是选中状态）
2                              2
3                              3 （被删除了）
```
描述的可能有些混乱，大家可以自己在项目中实践一下。（ps：v-for循环一定要加上key且key不能为index下标）