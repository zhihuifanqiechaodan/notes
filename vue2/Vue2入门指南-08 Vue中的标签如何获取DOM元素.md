Vue中提供了一些固定的标签来方便我们在开发过程中使用，并且在开发过程中，我们很有可能因为要使用某些第三方库而不得已要操作DOM元素，因此如何获取并操作DOM元素的使用呢。
### Vue中提供的标签
* component

这个标签是用来展示组件的

```
<div id="app">
    <component :is="要展示的组件名称"></component> 
    <custom></custom> // 直接通过自定义组件名称当作标签使用
</div>
new Vue({
    el: "#app",
    components: {
        "要展示的组件名称": {
            template: `<div> // 注意自定义组件的模版对象需要有且只有一个根标签。
                <h1>我是自定的组件一</h1>
            </div>`
        }，
        "custom": {
            template: `<div>
                <h1>我是自定义组件二</h1>
            </div>`
        }
    }
})

```
* template

这个标签用来定义组件的HTML结构

```
<div id="app">
    <custom></custom>
</div>
<template id="tmp">
    <div>
        <h1>我是用template标签定义组件的HTML模版</h1>
    </div>
</template>
new Vue({
    el: "#app",
    components: {
        "custom": {
            template: "#tmp"
        }
    }
})
```
* transition

这个标签是用来把需要被动画控制的元素包裹起来，展示自定义的动画效果

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
<div id="app">
    <transition>
        <h1>我是有动画效果的</h1>
    </transition>
</div>
```
* transition-group

通过v-for渲染出来的标签不能使用transition包裹， 需要使用transition-group包裹添加动画。


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
<div id="app">
    <transition-group tag="ul">
        <li v-for="item of list" :key="item.id">
            <h1>我是有动画效果的</h1>
        </li>
    </transition-group>
</div>
new Vue({
    el: "#app",
    data: {
        list:[
            {name:"fanqie", id: 1},
            {name: "chaofan", id: 2}
        ]
    }
})
```
* keep-alive

当前标签包裹组件时，会缓存不活动的组件实例，而不是销毁它们，keep-alive是一个抽象组件：它自身不会渲染一个DOM元素，也不会出现在父组件中。

当组件在<keep-alive>内被切换，它的 activated 和 deactivated 这个两个生命周期钩子函数将会被对应执行。

```
// 主要用于保留组件状态或避免重新渲染。
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>

<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>

<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```
注意，keep-alive 是用在其一个直属的子组件被开关的情形。如果你在其中有 v-for 则不会工作。如果有上述的多个条件性的子元素，keep-alive 要求同时只有一个子元素被渲染。
* solt

slot 元素作为组件模板之中的内容分发插槽。slot 元素自身将被替换。

```
// 和HTML元素一样，我们经常需要向组件传递内容，例如：
// custom 是自定义的组件
<custom> 
    <div>我是在组件内添加的标签</div>
</custom> 
```
但是我们渲染出来的却是这样：

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/4/1/169d6d92a7b2eebb~tplv-t2oaga2asx-image.image)
幸好，Vue 自定义的 <slot> 元素让这变得非常简单：

```
Vue.component('custom', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```
### Vue中获取DOM元素
在我们的vue项目中，难免会因为引用第三方库而需要操作DOM标签，vue为我们提供了ref属性。
ref 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例：

```
<p ref="p"></p>
// 现在在你已经定义了这个 ref 的组件里，你可以使用：
this.$refs.p 来操作这个DOM元素来。
```
vue不提倡我们操作DOM元素，因此在引用第三方插件或者项目中，尽量少的不要出现操作DOM元素。
