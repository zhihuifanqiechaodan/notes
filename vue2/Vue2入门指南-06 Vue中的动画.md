Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。
包括以下工具：

* 在 CSS 过渡和动画中自动应用 class
* 可以配合使用第三方 CSS 动画库，如 Animate.css
* 在过渡钩子函数中使用 JavaScript 直接操作 DOM
* 可以配合使用第三方 JavaScript 动画库，如 Velocity.js
## Vue中的全程动画
第一步： 需要把动画控制的元素用一个transition元素包裹起来，这个元素是Vue官方提供的。

```
例如：
// <transition> <h3>我被transition元素包裹有了动画效果</h3> </transition>
```
第二步： 需要在style中定义你要控制元素的动画效果, Vue官方提供了6个class切换。

```
<style>
    // 一般情况下， 元素的其实状态和终止状态的动画是一样的。
    // v-enter（这是一个时间点）是元素进入之前的其实状态，此时还没有开始进入。
    // v-lealve-to（这是一个时间点）是动画离开之后的终止状态，此时动画已经结束。
    v-enter, v-leave-to{
        opctity: 0 translateX(150px)
    }
    // v-enter-active是入场动画的时间段
    // v-leave-active是离场动画的时间段
    v-enter-active，v-leave-active{
        transition： all 0.4s ease
    }
</style>
```
第三步： 可以给动画添加时间属性 :duration

```
// 设置 :duration="毫秒值" 属性来统一设置入场和离场动画的时长
例如：设置入场和离场的动画时长为200毫秒
<transition :duration="200"> <h3>我被transition元素包裹有了动画效果</h3> </transition>
// 可以给:duration的值为一个对象，分别设置入场和离场的动画时长
例如：
<transition :duration="{enter:200, leave:400}"> <h3>我设置了入场和离场的动画时长</h3> </transition>
```
第四步： 我们可以自定义动画的前缀 v- 将其替换为自定义的

```
例如：
<style>
    my-enter, my-leave-to{
        opctity: 0 translateX(150px)
    }
    my-enter-active，my-leave-active{
        transition： all 0.4s ease
    }
</style>
<transition name="my"> <h3>自定义动画的v-前缀</h3> </transition>
```
* 注意：在实现列表过滤的时候，如果需要过度的元素是通过v-for循环出来的，不能使用 transition 元素包裹，需要使用 transition-group 元素包裹。
* 注意：再给 transition 和 transition-group 元素添加 appear 属性，可以实现页面创建出来的入场时候的动画。
* 注意：通过为 transition 和 transition-group 元素设置 tag 属性来指定渲染的标签元素，如果不指定默认渲染为 span 标签元素。
* 注意：通过为 transition 和 transition-group 元素设置 mode 属性来提供过度模式

```
// 当前元素先进行过度，完成之后新元素过度进入
<transition mode="out-in"> <h3>设置动画过度模式</h3> </transition>
// 新元素先进行过度，完成之后当前元素过度离开
<transition mode="in-out"> <h3>设置动画过度模式</h3> </transition>
```
## Vue中的半程动画

```
// 第一步同样将被动画控制的元素用transition元素包裹起来
// 第二步调用钩子函数， 这里只介绍部分钩子，其余的动画钩子请移步到官网查看
<transition 
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
>
<h1>半程动画</h1></transition>
// 第三步在配置对象中的methods属性中定义方法
new Vue({
    el: "#app",
    methods: {
        // 注意：动画钩子函数的第一个参数 el 表示要执行动画的那个DOM元素，是个原生的js DOM元素
        // beforeEnter函数表示动画入场之前，此时动画还未开始，此时编辑动画之前的样式
        beforeEnter (el) {
            el.style.transform = "translate(0,0)"
        },
        // enter函数表示动画开始之后，这里可以设置完成动画之后的结束状态
        enter (el) {
            // 注意：这里要加一句el.offsetWidth/Height/Left/Right,这句话没有实际的作用。
            // 但是如果不写，不会出来动画效果，这里可以认为它会强制动画刷新。
            el.offsetWidth
            el.style.transform = "translate(150px, 450px)"
            el.style.transiton = all 1s ease
            done()
        },
        // 动画完成之后会调用这个函数
        afterEnter (el) {
            // 这里写动画完成以后的样式
        }
    }
})
```
* 注意：在只用JavaScript过度的时候，在enter和leave中必须使用done进行回调，否则他们将被同步调用，过度会立即完成，看不到动画效果。
## Vue中使用第三方类实现动画（全程）
我们可以通过以下特性来自定义过渡类名：
* enter-class
* enter-active-class
* enter-to-class (2.1.8+)
* leave-class
* leave-active-class
* leave-to-class (2.1.8+)

他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用。

例如： 引入第三方 Animate.css 动画库

```
// 在动画库中选取我们想要的动画效果名称，例如入场选 bounceln, 离场 bounceOut
<transition 
    enter-active-class="animated bounceln"
    leave-active-class="animated bounceOut"
> <h3>引用第三方动画库</h3> </transition>
```
* 注意：在引用第三方动画库选定动画类的的时候还要在其前面加上一个基本的类animated

个人推荐在使用Vue的时候如果动画效果简单自己写就行，复杂的话在引用第三方动画库。而且在给元素添加动画的时候，要考虑清楚是加全程动画还是半程动画就够类。
