```
该篇文章的讲解能能够带你快速入门vue,尽可能多的讲解vue中的各个知识点,让你能够快速上手使用vue发开你的第
一个项目, 当然已经学习使用了vue的同学可以查漏补缺,看看那些是自己学习但长时间不用已经忘记的
```
## 为什么要学习vue
通过学习Vue提供的指令, 很方便的就能把数据渲染到页面上, 不在需要手动操作DOM元素, 前端的Vue之类的框架, 不提倡手动操作DOM元素。
### 什么是MVVM模式
MVVM 是Model-View-ViewModel 的缩写，它是一种基于前端开发的架构模式，其核心是提供对View 和 ViewModel 的双向数据绑定，这使得ViewModel 的状态改变可以自动传递给 View，即所谓的数据双向绑定。

```
其中 M 层 是vue中的data, V层是el绑定的HTML元素, VM是new实例的vue
```
### 第一章 - 导入 vue 创建一个VM 实例, 传入配置对象, 了解配置对象中的各个属性
我们在页面中通过script标签引入我们需要的vue
```
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">
  {{ message }}  // 通过差值表达式的方式将数据渲染到页面
</div>
var VM = new Vue({
  el: '#app', // 表示当我们new的这个Vue实例, 要控制页面上的那个区域
  data: { // data属性中，存放的是el中要用到的数据,这里的data就是MVVM中的M专门用来保存每个页面的数据
    message: 'Hello Vue!'
  },
  methods : {}, // 这个methods属性中定义了当前Vue实例所有可用的方法,主要写业务逻辑
  computed: {}, // 在computed中,可以定一些属性, 这些属性叫做计算属性,计算属性的本质就是一个方法,只不过我们在使用这些计算属性的时候是吧它们的名称直接当做属性来使用的,并不会把计算属性当做方法去调用
  filters : {}, // 这个filters属性中定义了当前Vue实例中所有可用的过滤的方法 
  watch: {}, // 使用这个属性,可以监听data中数据的变化,然后触发这个watch中对应的function处理函数
  router, // 挂载路由对象
  directives：{}, // 这个directives属性定义了当前Vue实例中所有可用的自定义指令
  beforeCreate () {}, // 生命周期函数: 表示实例完全被创建之前,会执行这个函数
  created () {}, // 生命周期函数: 表示实例被创建之后
  beforeMounted () {}, // 生命周期函数: 表示模板已经编译完成,但是还没有把模板渲染到页面中
  mounted () {}, // 生命周期函数:表示模板已经编译完成,内存中的模板已经真实的渲染到了页面中去,已经可以看到渲染好的页面了
  beforeUpdate () {}, // 生命周期函数: 表示当前界面还没有被更新,数据肯定被更新了
  update () {}, // 生命周期函数: 表示当前页面和数据保持同步了,都是最新的
  beforeDestroy () {}, // 生命周期函数: 表示Vue实例已经从运行阶段进入到销毁阶段
  destroyed () {} // 生命周期函数: 表示组件已经完全被销毁了
})
```