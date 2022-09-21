---
# 主题列表：juejin, github, smartblue, cyanosis, channing-cyan, fancy, hydrogen, condensed-night-purple, greenwillow, v-green, vue-pro, healer-readable, mk-cute, jzman, geek-black, awesome-green
# 贡献主题：https://github.com/xitu/juejin-markdown-themes
theme: juejin
highlight:
---
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fa590fb72f1047d9960db11ce1edddf2~tplv-k3u1fbpfcp-watermark.image)

终于 Vue3 的正式版发布了，两年多的开发，多位贡献者的努力。凝聚了这么多优秀开发者的智慧和努力，注定 Vue3 会成为下一个前端主流开发框架。本篇文章将带领大家一步步学习和了解使用vue3,当然更多的是我的学习过程。

前端就是这样，要时刻保持学习，每个月都有新技术的产生。如果每个知识都学，显然不现实，所以也要有选择性的学习。例如现在主流的三大框架，Vue/React/angular, 基于这三大框架的扩展框架我都应该有所涉及和了解。

有的小伙伴这时候会说，我们公司还没开始使用，可以先不学。确实是这样，我们公司也没有使用，但无论什么技术的产生都会有个过程，这个过程中有一个阶段是可以充分享受技术红利的。你现在学，无论技术红利什么时候到来，你都可以从容的抓住技术红利期，获得最大的收益。

当然本篇文章更多是讲如何使用，关于代码为何这样用，原理是什么我会在等以后深入了解以后在出一篇中级用法讲解。

俗话说读书百变不如手写一遍。希望大家还是能够自己手敲一遍。加深印象。

## 前置知识
虽然我讲解的都是零星的知识点和api用法，但是你要学习和了解还需要掌握以下的知识点。
- TypeScript - 你了解并且能够实用简单的用法
- Vue2.0基础 - 你起码要有Vue2.0的使用经验
- 你需要会使用npm或者yarn来安装依赖包
当然上述的前置知识你都不会的话。你可以在掘金或者我的文章中查看vue2.0基础笔记-高级笔记，TypeScript可以去官网学习简单使用。
## Vue3.0的一些新特性
- Vue3是向下兼容，Vue3 支持大多数 Vue2 的特性。拿 Vue2 的语法开发 Vue3，也是没有任何问题的。
- 性能的提升，打包大小减少 41%，初次渲染快 55%，更新快 133%，内存使用减少 54%。
- 新推出的Composition API ，在 Vue2 中遇到的问题就是复杂组件的代码变的非常麻烦，甚至不可维护。Composition API一推出，立马解决了这个问题，它是一系列 API 的合集。
- 其他新特性：Teleport(瞬移组件)、Suspense(解决异步加载组件问题)和全局 API 的修改和优化。
- 更好TypeScript支持
[B站 官方Vue3 发布会视频](https://www.bilibili.com/video/BV1iA411J7cA?from=search&seid=2979047174353974296)
### 使用vue-cli 搭建vue3开发环境
```!
Vue-cli ：Vue.js 开发的标准工具 ， 新版的 vue-cli 还提供了图形界面，如果你对命令行陌生，也可以使用图形界面。
```
具体安装步骤我这了就不详细描述了。官网都很齐全也通俗易懂。这里贴上[官网地址](https://cli.vuejs.org/zh/)
### 项目基本目录介绍
```js
|-node_modules       -- 所有的项目依赖包都放在这个目录下
|-public             -- 公共文件夹
---|favicon.ico      -- 网站的显示图标
---|index.html       -- 入口的html文件
|-src                -- 源文件目录，编写的代码基本都在这个目录下
---|assets           -- 放置静态文件的目录，比如logo.pn就放在这里
---|components       -- Vue的组件文件，自定义的组件都会放到这
---|App.vue          -- 根组件，这个在Vue2中也有
---|main.ts          -- 入口文件，因为采用了TypeScript所以是ts结尾
---|shims-vue.d.ts   -- 类文件(也叫定义文件)，因为.vue结尾的文件在ts中不认可，所以要有定义文件
|-.browserslistrc    -- 在不同前端工具之间公用目标浏览器和node版本的配置文件，作用是设置兼容性
|-.eslintrc.js       -- Eslint的配置文件，不用作过多介绍
|-.gitignore         -- 用来配置那些文件不归git管理
|-package.json       -- 命令配置和包管理文件
|-README.md          -- 项目的说明文件，使用markdown语法进行编写
|-tsconfig.json      -- 关于TypoScript的配置文件
|-yarn.lock          -- 使用yarn后自动生成的文件，由Yarn管理，安装yarn包时的重要信息存储到yarn.lock文件中
```
### main.ts入口文件
```js
import { createApp } from "vue"; // 引入vue文件，并导出`createApp`
import App from "./App.vue"; // 引入自定义组件，你在页面上看的东西基本都在这个组件里

createApp(App).mount("#app"); // 把App挂载到#app节点上，在public目录下的index.html找节点
```
### Composition API用法介绍
#### setup() 和 ref()的使用
使用setup()新语法，写了这个就不需像之前vue2语法需要写data了。
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{girl}}</div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
export default defineComponent({
  name: "App",
  setup() {
    const girl = ref('番茄女孩');
    return {
      girl
    };
  },
});
</script>
```
vue2中声明的变量需要放在data中声明使用。而vue3中通过ref声明变量，并且在setup函数中返回出去才可以在页面上使用。

vue2中修改data中的变量需要在methods中定义方法。然后通过事件触发修改变量。那么vue3中是如何定义方法和改变ref声明的变量呢？
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{girl}}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
export default defineComponent({
  name: "App",
  setup() {
    const girl = ref('番茄女孩');
    const changeGirls = () => {
    	girl.value = '番茄男孩'
    }
    return {
      girl,
      changeGirls
    };
  },
});
</script>
```
是的你没看错。通过ref声明的变量在赋值的时候需要通过变量.value去进行赋值和读取操作。并且事件方法不在需要声明在methods中了。 而是直接写在setup函数中就可以了。 是不是非常的简单。并且节省了很多代码。但是通过ref声明的变量更改需要通过变量.value的方式去修改。很不符合我们之前书写代码的用法。那么下面介绍的reactive方法就很符合我们之前的用法。
#### reactive()
它也是一个函数(方法)，里边接受的参数是一个 Object(对象)。

无论是变量和方法，都可以作为 Object 中的一个属性，而且在改变changeGirls值的时候也不用再加讨厌的value属性了。再return返回的时候也不用一个个返回了，只需要返回一个data,就可以了。
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ data.girl }}</div>
    <button @click="data.changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";
export default defineComponent({
  name: "App",
  setup() {
    const data = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
      },
    });

    return {
      data,
    };
  },
});
</script>
```
这样的写法看上去是不是别ref的用法更加简洁一些呢。 现在template中，每次输出变量前面都要加一个data。还是有点不方便。那么如何能像vue2一样直接书写属性呢。你可能会想到使用扩展运算符的方式。但是这样解构以后就变成普通变量了不在具有响应式的能力。解决这个办法就需要使用vue3的一个新函数toRefs()
#### toRefs()
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ girl }}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from "vue";
export default defineComponent({
  name: "App",
  setup() {
    const data = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
      },
    });

    return {
      ...toRefs(data),
    };
  },
});
</script>
```
`关于何如选择使用ref()和recative()`这个目前看来只是在写法上有不同，具体在性能和其他方面还有待后续查看。但是我自己更加倾向于使用reactive语法。
#### 给data增加类型注解
既然使用了typescript语法。我们在书写代码和方法的时候就要规范和按照ts语法来书写
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ girl }}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from "vue";
interface DataProps {
  girl: string;
  changeGirls: () => void;
}
export default defineComponent({
  name: "App",
  setup() {
    const data: DataProps = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
      },
    });

    return {
      ...toRefs(data),
    };
  },
});
</script>
```
#### vue3.x的生命周期和钩子函数
```
setup() :开始创建组件之前，在beforeCreate和created之前执行。创建的是data和method
onBeforeMount() : 组件挂载到节点上之前执行的函数。
onMounted() : 组件挂载完成后执行的函数。
onBeforeUpdate(): 组件更新之前执行的函数。
onUpdated(): 组件更新完成之后执行的函数。
onBeforeUnmount(): 组件卸载之前执行的函数。
onUnmounted(): 组件卸载完成后执行的函数
onActivated(): 被包含在<keep-alive>中的组件，会多出两个生命周期钩子函数。被激活时执行。
onDeactivated(): 比如从 A 组件，切换到 B 组件，A 组件消失时执行。
onErrorCaptured(): 当捕获一个来自子孙组件的异常时激活钩子函数（不太会用。还在了解中）
```
**注：使用keep-alive组件会将数据保留在内存中，比如我们不想每次看到一个页面都重新加载数据，就可以使用keep-alive组件解决。**
```js
 Vue2--------------vue3
beforeCreate  -> setup()
created       -> setup()
beforeMount   -> onBeforeMount
mounted       -> onMounted
beforeUpdate  -> onBeforeUpdate
updated       -> onUpdated
beforeDestroy -> onBeforeUnmount
destroyed     -> onUnmounted
activated     -> onActivated
deactivated   -> onDeactivated
errorCaptured -> onErrorCaptured
```

通过这样对比，可以很容易的看出 vue3 的钩子函数基本是再 vue2 的基础上加了一个on,但也有两个钩子函数发生了变化。
- BeforeDestroy变成了onBeforeUnmount
- destroyed变成了onUnmounted
**vue3还新增了onRenderTracked和onRenderTriggered函数，官方说是用来调试使用的，但是我还没太明白这俩调试的具体作用场景。**
- onRenderTracked状态跟踪
onRenderTracked直译过来就是状态跟踪，它会跟踪页面上所有响应式变量和方法的状态，也就是我们用return返回去的值，他都会跟踪。只要页面有update的情况，他就会跟踪，然后生成一个event对象，我们通过event对象来查找程序的问题所在。
- onRenderTriggered状态触发
onRenderTriggered直译过来是状态触发，它不会跟踪每一个值，而是给你变化值的信息，并且新值和旧值都会给你明确的展示出来。
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ girl }}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  reactive,
  toRefs,
  onRenderTracked,
  onRenderTriggered,
} from "vue";
interface DataProps {
  girl: string;
  changeGirls: () => void;
}
export default defineComponent({
  name: "App",
  setup() {
    const data: DataProps = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
      },
    });
     onRenderTracked((e) => {
       console.log(e);
       console.log("状态跟踪");
     });
    onRenderTriggered((e) => {
      console.log(e);
      console.log("状态跟踪");
    });
    return {
      ...toRefs(data),
    };
  },
});
</script>
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dca9c73232c745cfb3ee68dbc6a939ba~tplv-k3u1fbpfcp-watermark.image)
个人感觉onRenderTriggered应该会在以后用到的比较多。而onRenderTracked很有可能是在不知道哪里发生错误的时候需要跟踪所有属性的时候使用。这两个函数是不是和watch感觉很像呢。下面我们来学习一下vue3中watch的用法
#### vue3中watch的使用和注意事项
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ girl }}</div>
    <div>{{ boy }}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs, watch, ref } from "vue";
interface DataProps {
  girl: string;
  changeGirls: () => void;
}
export default defineComponent({
  name: "App",
  setup() {
    const boy = ref("我是男孩");
    const data: DataProps = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
        boy.value = data.girl;
      },
    });
    watch(boy, (newvalue, oldvalue) => {
      console.log(newvalue);
      console.log(oldvalue);
    });
    return {
      ...toRefs(data),
      boy,
    };
  },
});
</script>
```
通过上面的小例子是不是感觉vue3的watch很简单就能够使用。那么如何监听多个值的变化呢。当你要监听多个值的时候，不是写多个watch函数，而是要传入数组的形式。比如现在我们同时要监听data中方法，也就是changeGirls时，我们可以使用数组的形式。
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ girl }}</div>
    <div>{{ boy }}</div>
    <button @click="changeGirls">改变我的女孩</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs, watch, ref } from "vue";
interface DataProps {
  girl: string;
  changeGirls: () => void;
}
export default defineComponent({
  name: "App",
  setup() {
    const boy = ref("我是男孩");
    const data: DataProps = reactive({
      girl: "番茄女孩",
      changeGirls: () => {
        data.girl = "番茄男孩";
        boy.value = data.girl;
      },
    });
    watch([boy, () => data.changeGirls], (newvalue, oldvalue) => {
      console.log(newvalue);
      console.log(oldvalue);
    });
    return {
      ...toRefs(data),
      boy,
    };
  },
});
</script>
```
vue3这种监听用法。虽然可以监听到对应属性或者方法发生变化之后触发watch。但是针对于某个变量发生改变做出对应的事件处理。我没有想到更好的办法去做判断。有知道的小伙伴可以评论区告诉我。
#### vue3中的模块化使用
通过上述的案例我们可以发现。如果当前组件内声明的变量和方法多了以后。还是会增加代码量和可读性。这时候我们可以将耦合性低的一些代码和方法作为模块化提取出去。在需要使用的组建内直接引用即可。例如一个时间组件
```js
// src/hooks/timeHooks.ts
import { ref } from 'vue'


const nowTime = ref('00:00:00')
const setNowTime = () => {
    nowTime.value = new Date().toString()
}

export {
    nowTime,
    setNowTime
}
```
```js
<template>
  <div>
    <h2>vue3新语法</h2>
    <div>{{ nowTime }}</div>
    <button @click="setNowTime">现在时间</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { nowTime, setNowTime } from "../hooks//timeHook";

export default defineComponent({
  name: "App",
  setup() {
    return {
      nowTime,
      setNowTime,
    };
  },
});
</script>
```
现在可以看出这种方式，比vue2中要好很多，不用再使用mixins(混入)要好很多。
#### Teleport组件
我把这个函数叫独立组件，它可以把你写的组件挂载到任何你想挂载的DOM上，所以说是很自由很独立的。 在使用Vue2的时候是作不到的。

```js

// 在/src/components/目录下，创建一个Modal.vue的组件
<template>
  <teleport to="#modal">
    <div id="center">
      <h2>只会番茄炒蛋</h2>
    </div>
  </teleport>
</template>
<script lang="ts">
export default {};
</script>
<style>
#center {
  width: 200px;
  height: 200px;
  border: 2px solid black;
  background: white;
  position: fixed;
  left: 50%;
  top: 50%;
  margin-left: -100px;
  margin-top: -100px;
}
</style>
```
然后我们在打开/public/index.html,增加一个model节点。
```js
<div id="app"></div>
<div id="modal"></div>
```
这时候在浏览器中预览，就会发现，现在组件已经挂载到model节点上了，这就是teleport组件的使用了。说实话这个组件的用法我暂时有点懵逼。比如单独挂在节点。那么底层的响应式开发以及数据传递如何处理。这些后续估计要看源码解析才能明白了。
#### Suspense组件
简单看了一下，尝试写了一下用法。但是感觉用起来很简单。我个人感觉应该是还没详细研究明白他的使用场景和具体的用法。这个组件等我深入研究以后在写个全面的。
```js
<template>
  <div>
    <Suspense>
      <template #default>
      	// 请求回来展示，AsyncComponent组件中有异步请求。
         <AsyncComponent />
      </template>
      <template #fallback>
      	// 请求中和失败展示
        <h1>Loading...</h1>
      </template>
    </Suspense>
  </div>
</template>
```
****
以上呢就是我对vue3部分内容的学习笔记。后续学习还在继续中。大家可以参考一二
## ❤️ 看完帮个忙
如果你觉得这篇内容对你挺有启发，我想邀请你帮我个小忙：

1. **点赞**，让更多的人也能看到这篇内容（收藏不点赞，都是耍流氓 -_-）

2. **关注公众号「番茄学前端」**，我会定时更新和发布前端相关信息和项目案例经验供你参考。

3. **加个好友**， 虽然帮不上你大忙，但是一些业务问题大家可以探讨交流。