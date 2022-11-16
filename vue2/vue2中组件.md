学习组件，我们首先要了解什么是组件。组件是Vue可以复用的Vue实例，且带有一个名字，在Vue中我们可以定义多个组件，在多个不同的页面的中引用多个多不同的组件，减少开发的工作量。因为组件是可复用的Vue实例，所以它们与new Vue接收相同的选项，例如：data,computed,watch,methods以及生命周期钩子等等，仅有的例外是像el这样根实例特有的选项。
### 创建组件的几种方式
* 第一种使用Vue.extend来创建全局的Vue组件

```
// 创建组件模版对象
let mycomponent = Vue.extend({
    // 通过template属性指定了组件要展示的HTML结构
    template:"<div>我是使用Vue.extend创建的组件</div>" // 
})

// 使用Vue.component("组件名称", 创建出来的组件模版对象)
Vue.component("custom", mycomponent) 

// 直接把组件的名称以HTML标签的形式引入到页面中就可以使用了
<custom></custom> 
```
**注意 ：不论是那种方式创建出来的组件，组件的template属性指向的模版内容，必须有且只能有一个唯一的根元素。**

**注意 ：在使用Vue.component定义全局组件的时候，组件名称使用驼峰命名，则在引用组件的时候，需要把大写的驼峰改为小写的字母，同时两个单词之间使用 - 连接，例如：**

```
Vue.component("customLabel", mycomponent) // 组件名称驼峰命名
<custom-label></custom-label> // 驼峰改为小写的字母，同时两个单词之间使用 - 连接
```
注册全局组件引用的简写方式：

```
Vue.component("custom", Vue.extend({
    // 通过template属性指定了组件要展示的HTML结构
    template:"<div>我是使用Vue.extend创建的组件</div>" // 
})) 
```
* 第二种使用Vue.component创建全局的Vue组件

```
Vue.component("custom", {
    // 通过template属性指定了组件要展示的HTML结构
    template:"<div>我是使用Vue.component创建的组件</div>" // 
})

// 通过Vue.component的另一种书写方式创建全局组件
Vue.component("custom", {template:"#tmp"}) //  template属性指向一个id
// 在被控制的Vue实例el绑定的元素外，使用template元素定义组件的HTML模版结构
<template id="tmp">
    <div>
        <h1>我是Vue.component创建全局组件的第二种方式</h1>
    </div>
</template>
```
* 第三种通过Vue实例的components属性创建Vue局部组件（实例内部私有组件）

```
<div id="app">
    <custom>/custom>
    <customTwo></customTwo>
    <customThree></customThree>
</div>
<template id="tmp">
    <div>
        <h1>我是components创建的局部组件利用template元素</h1>
    </div>
</template>
<script>
    let customThree = {
        template: "<div><h1>我是Vue实例外创建的组件模版对象</h1></div>"
    }
    new Vue({
    el: "#app",
    components: {
        // 第一种方式
        "custom": {template:"<div><h1>我是components创建的局部组件</h1></div>"},
        // 第二种方式
        "customTwo"{template:"#tmp"}，
        // 第三种方式
        "customThree": customThree // 自定义组件名称和组件模版对象名称一致可以简写为一个
    }
})
</script>
```
**注意 ： 组件中也可以定义数据和属性**

```
// 但是组件中的data和实例中的data不一样，实例中的data可以为一个对象，但组件中的data必须是一个方法。
// 这个方法内部还必须return一个对象出来才行。
// 组件中data的数据和实例中data数据的使用方式完全一样
// 例如：
new Vue({
    el: "#app",
    data: {
        msg: "我是实例中data的数据"
    }
})
Vue.components("custom",{
    template:"<div> <h1>{{ msg }}</h1></div>",
    data () {
        return {
            msg: "我是组件中data的数据"
        }
    }
})
```
### Vue提供了components元素来展示对应名称的组件

```
// components是一个占位符， :is 属性可以用来指定要展示的组件名称
// 注意 :is 属性的值为要展示的组件名称，但是也可以写成一个变量，方便某些时候动态切换要展示的组件。
<div id="app">
    <component :is="msg"></component>
    <button @click="change">切换组件</button>
</div>
<script>
    new Vue({
    el: "#app",
    data: {
        msg: "custom"
    },
    components: {
        "custom": {template:"<div><h1>我是components创建的局部组件custom</h1></div>"}，
        "customTwo": {template:"<div><h1>我是components创建的局部组件customTwo</h1></div>"}
    },
    methods: {
        change () {
            // 通过chage方法动态改变msg的值来切换要展示的组件
        }
    }
})
</script>
```
### 组件通信 => 父子组件，兄弟组件之间通信
* 组件通信中，子组件是无法访问到父组件中的数据和方法的。
* 父组件可以在引用子组件的时候，通过属性绑定(v-bind)的形式，把数据传递给子组件使用。
* 父组件通过自定义属性传过来的数据，需要子组件在props属性上接收才能使用。
#### 父传子值

```
<div id="app">
    <custom :parentmsg="msg"></custom>
</div>
<script>
    new Vue({
    el: "#app",
    data: {
        msg: "我是父组件中的数据"
    },
    components: {
        "custom": {
            props: ["parentmsg"]，// 子组件通过props属性接收父组件传来的数据
            template:"<div><h1>我要引用父组件中的数据----{{ parentmsg }}</h1></div>"
        }
    }
})
</script>
```
### 父传子方法, 子传父数据
父组件向子组件传递方法，使用的是事件绑定机制v-on,当我们自定义事件属性后，那么子组件就可以通过某些方式来调用父组件传递的这个方法。

```
<div id="app">
    // 通过自定义事件名称func以事件绑定机制@func="show"将父组件的show方法传递给子组件
    <custom @func="show"></custom> 
</div>
<script>
    new Vue({
    el: "#app",
    data: {
        msg: "我是父组件中的数据"
    },
    components: {
        "custom": {
            props: ["parentmsg"]，// 子组件通过props属性接收父组件传来的数据
            data () {
                return {
                    childmsg: "我是子组件的数据"
                }
            },
            template:`
                <div>
                    <h1>我要调用父组件传递过来的方法</h1>
                    <button @click="emit">点我调用父组件传递的方法</button>
                </div>`，
            methods: {
                emit () {
                    // 触发父组件传过来的func方法，配合参数可以将子组件的数据传递给父组件
                    // vm.$emit(evebtNane, [...args]) 触发当前实例上的事件，附加参数会传给监听器回调。
                    this.$emit("func"，this.childmsg) 
                }
            }
        }，
    }，
    methods: {
        // 子组件触发了我，并且给我传递了子组件的数据data
        show (data) {
            console.log("我是父组件中的show方法")
            console.log("接收到了子组件传递的数据---" + data)
        }
    }
})
</script>
```
* 注意组件中所有props属性中的数据，都是通过父组件传递给子组件的。
* props中的数据都是只读不可写的，data上的数据都是可读可写的，
* 子组件中data中的数据，并不是通过父组件传递过来的，而是子组件自己私有的。
### 兄弟组件之间数据传递
* 第一种借助中央事件总线

```
<div id="app">
    
</div>
<script>
    // 重新实例一个Vue
    let Bus = new Vue()
    
    new Vue({
        el: "#app",
        components: {
            "custom": {
                template:`<div @click = sendMsg>
                    <button>点我给customTwo组件传递我的数据</button>
                </div>`,
                data () {
                    return {
                        msg: "我是custom组件的数据"
                    }
                },
                methods: {
                    sendMsg () {
                        Bus.$emit("myFunc", this.msg) // $emit这个方法会触发一个事件
                    }
                }
            }，
            "customTwo": {
                template:`<div>
                    <h1>下面是我接收custom组件传来的数据</h1>
                    <p>{{ customTwoMsg }}</p>
                </div>`,
                data () {
                    return {
                        customTwoMsg: ""
                    }
                }，
                created () {
                    this.brotherFunc()
                },
                methods: {
                    brotherFunc () {
                        Bus.$on("myFunc", (data) => { // 这里要用箭头函数，否则this指向有问题。
                            this.customTwoMsg = data
                        })
                    }
                }
            }
        }
    })
</script>
```
这样借助总线机制，实现兄弟组件之间的数据共享。
* 第二种借助第三方vuex库（这是我推荐使用的）

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 devtools extension，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

具体用法请查看[vuex官方文档](https://vuex.vuejs.org/zh/guide/)超级详细。
