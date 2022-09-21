绑定事件监听器。事件类型由参数指定。表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略。

用在普通元素上时，只能监听原生DOM事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件。
### 绑定事件机制
Vue中提供了v-on：绑定事件机制（冒号后面跟着事件名称）

```
使用方式：
new app({
    el: "#ap",
    methods: {
        add () {
            alert("我是P标签的点击触发的")
        }
    }
})
<p v-on:click="add"></p>** // 注意: 绑定事件的触发方法需要在配置对象的methods属性中定义
简写方式:
<p @click="add"></p>
```
#### 事件修饰符

```
.stop - 调用 event.stopPropagation()。 // 阻止冒泡
.prevent - 调用 event.preventDefault()。// 组织默认事件

//capture添加事件侦听器时使用事件捕获模式 //事件触发顺序从外往里
.capture - 添加事件侦听器时使用 capture 模式。 

// 只会组织自己身上冒泡行为的触发, 并不会真正阻止冒泡的行为
// self实现只有点击当前元素时候,才会触发事件处理函数
.self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。 

.{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
.native - 监听组件根元素的原生事件。
.once - 只触发一次回调。
.left - (2.2.0) 只当点击鼠标左键时触发。
.right - (2.2.0) 只当点击鼠标右键时触发。
.middle - (2.2.0) 只当点击鼠标中键时触发。
.passive - (2.3.0) 以 { passive: true } 模式添加侦听器
使用方式: 
<p @click.stop="add"></p> // @事件.事件修饰符
```
#### 按键修饰符
* 自定义全局按键修饰符

```
// 全局按键修饰符(所有的Vue实例都能调用)定义语法
创建方式：
// F2是定义键盘上的按键名称，等于号后面是对应的键盘码码值
Vue.config.keycodes.F2 = 113
```
* Vue官方提供的按键修饰符

```
// 为了在必要的情况下支持旧浏览器，Vue 提供了绝大多数常用的按键码的别名：
.enter // 回车键抬起
.tab // tab切换键抬起
.delete (捕获“删除”和“退格”键)
.esc //退出键抬起
.space // 空格键抬起
.up // 上
.down // 下
.left // 左
.right // 右
.对应键盘码 // 使用键盘码是要注意如果不是以上对应的键盘修饰符，需要创建按键修饰符
使用方式:
<p @keyup.enter="add"></p> // @click.按键修饰符
<p @keyup.13="add"></p> // @click.对应键盘码
```
### Vue中的过滤器
自定义过滤器,可被用作于常见的文本格式化,用在两个地方,mustachc插值和v-bind表达式,过滤器添加在JS表达式尾部,由"管道"符指示

过滤器调用的时候,采用就近原则,如果私有过滤器和全局过滤器名称一致,优先调用私有过滤器

过滤器调用时的格式  {{ msg | myFilter }}
* 自定义全局过滤器

```
// 注意: 函数内部第一个参数一定是你要过滤的这个msg
// 注意: 过滤器myFilter也可以进行传参
// 注意: 过滤器可以多次调用
创建方式:  // myFilter是过滤器的名字
Vue.filter("myFilter", function( msg ){
    return msg.toUpperCase()
})
```
* 自定义局部过滤器

```
new app({
    el: "#app",
    filters: {
        myFilter: function (val) {
            return val.toUpperCase()
        }
    }
})
```
### Vue中的监听属性
#### watch属性
使用这个属性,可以监听data中数据的变化,然后触发这个watch中对应的属性处理函数
* 监听属性
```
// watch可以用来监视一些非DOM元素的改变, 这就是他的优势
// watch是一个对象, 键是需要观察的表达式, 值是对应的回调函数
// 主要用来监听某些特定数据的变化,从而进行某也具体的业务逻辑操作,可以看做是computed和methods的结合体
使用方式:
new app({
    el: "#app",
    data: {
        count: 1
    },
    watch: {
        // 函数中有两个参数,一个是newVal, 一个是oldVal具体怎么用去看下官网 很简单
        count: function () {
            console.log("当data中的count属性的值发生变化时被我监听到了")
        }
    }
})
// 使用双向数据绑定count
<input >{{ count }}</input>
```
* 监听路由
watch监视路由变化

```
// 路由身上有一个this.$router.path属性, 指向当前路由的地址
使用方式: 
new app({
    el: "#app",
    data: {
        count: 1
    },
    watch: {
        // 函数中有两个参数,一个是newVal, 一个是oldVal具体怎么用去看下官网 很简单
        "$route.path": function(newVal, oldVal){
            // 在这里我们就可根据路由的变化去做一些认证, 跳转页面等等的操作
            console.log(`路由新地址${newVale}---路由旧地址${oldVal}`)
        }
    }
})
```
#### computed属性
在 computed 中,可以定一些属性, 这些属性叫做计算属性

计算属性的本质就是一个方法, 只不过我们在使用这些计算属性的时候是吧它们的名称直接当做属性来使用的, 并不会把计算属性当做方法去调用

computed属性的结果会被缓存, 除非依赖的响应式属性变化才会重新计算, 主要当做属性来使用

```
使用方式:
new app({
    el: "#app",
    data: {
        count: 1
    },
    computed: {
        countComputed () {
            return count + 1
        }
    },
    methods: {
        add () {
            count++
        }
    }
})
<button @click=add>自增</button> // 点击按钮的的时候count自增加1 
<input>{{ countComputed }}</input> // countComputed属性的值永远依赖于count, 并且比它大1

// 注意 : 计算属性在引用的时候一定不要加()去调用,直接把它当做普通属性去引用就好
// 注意 : 计算属性这个function内部所用到的任何data中的数据发生了变化,就会立即重新计算这个计算属性的值
// 注意 : 计算属性的求值结果会被缓存起来, 方便下次直接使用
// 注意 : 如果计算属性方法中, 所依赖的任何数据,都没有发生任何变化,则不会重新对计算属性求值
```