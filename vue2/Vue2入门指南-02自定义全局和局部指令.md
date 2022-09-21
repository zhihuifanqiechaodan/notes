## 第二章 学习vue中的自定义指令
自定义指令在我们的项目中很常用, 所以要认真学习。
### 第一部分: 使用Vue.directive()自定义全局的指令
**注意 :** 参数 1 是指令的名称, 在自定义指令的时候, 指令的名称前面不需要加 "v-"前缀

**注意 :** 参数 2 是一个对象, 对象身上有一些指令的相关函数, 这些函数可以在特定的阶段,
执行相关的操作句号

**注意 :** 在参数 2 中的相关函数中,第一个参数，永远是el,表示被绑定了指令的那个元素，这个el参数，是一个原生的js对象

**注意 :** 在参数 2 中的相关函数中,都有一个binding参数，是一个对象，它包含以下属性：name/指令名，value/指令的绑定值（例如v-mydirective="'red'"）值就为red，剩下的属性去看官网用的少
同样导入vue, 创建VM实例对象

```
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">
  {{ message }}  // 通过差值表达式的方式将数据渲染到页面
</div>
var VM = new Vue({
    el: '#app', // 表示当我们new的这个Vue实例, 要控制页面上的那个区域
    data: { // data属性中存放的是el中要用到的数据,这里的data就是MVVM中的M专门用来保存每个页面的数据
        message: 'Hello Vue!'
    },
})
// 自定义指令方法：
Vue.directive(“指令名称”，{ 
    bind: function(){}, 
    inserted: function(){}, 
    updata: function(){} 
})
```
**自定义指令中的bind函数** 

每当指令绑定到元素上的之后，会立即执行这个bind函数，只执行一次

**注意 :** 和样式相关的操作，一般都可以在bind执行，只要通过指令绑定了元素，不管这个元素有没有被插入到页面中去，这个元素肯定有了一个内联样式。

将来元素肯定会显示页面中去，这时候，浏览器的渲染引擎必然会解析样式，应用给这个元素

**注意 :** 在元素干绑定了指令的时候，还没有插入到DOM中去，这时候调用例如：el.focus（获取焦点）等js行为相关的操作，需要在inserted方法中去执行，防止js行为不生效

因为一个元素, 只有在插入DOM之后, 才能操作他的js行为

```
// 举例
Vue.directive(“color”，{ 
    bind: function(el, binding){
        //这个指令绑定的样式颜色是固定死的，我们可以通过指令的绑定值来动态改变样式颜色
        el.style.color ="red" 
    }, 
})
// 需要注意: 指令绑定的值如果不是字符串而是一个变量，就需要你在data中定义这个变量的值
// 下面展示通过使用指令传入的颜色来来定义绑定标签的颜色
<p v-color=" 'red' "></p> 
定义指令：Vue.directive(" color ",{ 
    bind: function(el, binding){ 
        el.style.color = binding.value 
    } 
})
// 在自定义局部指令的时候, 我们也可以通过给v-color绑定一个变量, 通过动态改变变量的值来控制标签的颜色 
```
**自定义指令中的inserted函数** 

表示元素插入到DOM中的时候会执行inserted函数（触发一次）

**注意 :** 和js行为相关的操作，需要在inserted方法中去执行，防止js行为不生效


```
// 例如: 
<p v-color=" 'red' "></p> 
定义指令：Vue.directive(" color ",{ 
    bind: function(el, binding){ 
        el.style.color = binding.value // 设置绑定该指令的标签颜色
    },
    inserted: function(el, binding){ 
        el.focus()  // 在这里执行获取焦点才管用
    } 
})
```
**自定义指令中的updata函数**

当组件更新的时候, 会执行updata函数, 可能会多次触发
### 第二部分: 使用Vue.directive()自定义全局的指令

使用方法和上面的全局指令一样。只是自定义局部指令需要在VM实例中定义

```
例如:
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">
  {{ message }}  // 通过差值表达式的方式将数据渲染到页面
</div>
var VM = new Vue({
    el: '#app', // 表示当我们new的这个Vue实例, 要控制页面上的那个区域
    data: { // data属性中存放的是el中要用到的数据,这里的data就是MVVM中的M专门用来保存每个页面的数据
        message: 'Hello Vue!'
    },
    // 自定义局部指令
    directives: {
        "color":{
           bind: function(){}, 
           inserted: function(){}, 
           updata: function(){} 
        }
        // 下面这个是简写方式: 简写的function等同于把代码写到了bind和updata函数中
        color: function(el, binding) {
            el.style.color = binding.value
        }
    }
})

```