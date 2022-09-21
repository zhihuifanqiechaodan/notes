### 前言
vue3.x相关的生态已经在不断的完善中，相应的UI/路由/pinia等都已成熟，新的项目也在考虑使用新版本开发了，开一个帖子记录一下搭建移动端简易模版的过程，方便以后回顾。

#### vite前端构建工具

> 兼容性注意
>
> Vite 需要 [Node.js](https://nodejs.org/en/) 版本 14.18+，16+。然而，有些模板需要依赖更高的 Node 版本才能正常运行，当你的包管理器发出警告时，请注意升级你的 Node 版本。

使用 NPM:
```js
npm create vite@latest
```
使用 Yarn:
```js
yarn create vite
```
使用 PNPM:
```js
pnpm create vite
```

选择自己需要的模版以后安装依赖并启动

#### CSS 预处理器安装

**sass**

Vite 提供了对 `.scss`, `.sass`, `.less`, `.styl` 和 `.stylus` 文件的内置支持。没有必要为它们安装特定的 Vite 插件，但必须安装相应的预处理器依赖：


```js
yarn add sass -D
```

#### 移动端适配

**viewport**
之前做移动端适配通常都是使用`lib-flexible`+`postcss-pxtorem`的方案，但是随着viewport单位得到越来越多浏览器的支持，lib-flexible 官方也基本已经废弃，建议大家都使用viewport方案。

**postcss-px-to-viewport**

将px单位转换为视口单位的 (vw, vh, vmin, vmax) 的 [PostCSS](https://github.com/postcss/postcss) 插件.

将px自动转换成viewport单位vw，vw本质上还是一种百分比单位，100vw即等于100%

1. 安装

使用 NPM:
```js
npm install postcss-px-to-viewport --save-dev
```

使用 Yarn:
```js
yarn add -D postcss-px-to-viewport
```
2. 在项目根目录下创建 postcss.config.cjs 文件


```js
module.exports = {
    plugins: {
        'postcss-px-to-viewport': {
            unitToConvert: 'px', // 需要转换的单位，默认为"px"
            viewportWidth: 375, // 设计稿的视口宽度
            exclude: [/node_modules/], // 解决vant375,设计稿750问题。忽略某些文件夹下的文件或特定文件
            unitPrecision: 5, // 单位转换后保留的精度
            propList: ['*'], // 能转化为vw的属性列表
            viewportUnit: 'vw', // 希望使用的视口单位
            fontViewportUnit: 'vw', // 字体使用的视口单位
            selectorBlackList: [], // 需要忽略的CSS选择器，不会转为视口单位，使用原有的px等单位。
            minPixelValue: 1, // 设置最小的转换数值，如果为1的话，只有大于1的值会被转换
            mediaQuery: false, // 媒体查询里的单位是否需要转换单位
            replace: true, //  是否直接更换属性值，而不添加备用属性
            landscape: false, // 是否添加根据 landscapeWidth 生成的媒体查询条件 @media (orientation: landscape)
            landscapeUnit: 'vw', // 横屏时使用的单位
            landscapeWidth: 1125 // 横屏时使用的视口宽度
        }
    }
}
```

3. 这样就配置好了，开发可以根据设计稿的尺寸去配置viewportWidth属性就可以达到一比一的视图开发了，不在需要额外的继续尺寸。

#### Vant

Vant 是一个**轻量、可靠的移动端组件库**，于 2017 年开源。

目前 Vant 官方提供了 [Vue 2 版本](https://vant-contrib.gitee.io/vant/v2)、[Vue 3 版本](https://vant-contrib.gitee.io/vant)和[微信小程序版本](http://vant-contrib.gitee.io/vant-weapp)，并由社区团队维护 [React 版本](https://github.com/3lang3/react-vant)和[支付宝小程序版本](https://github.com/ant-move/Vant-Aliapp)。

1. 安装


使用 NPM:
```js
npm install vant
```

使用 Yarn:
```js
yarn add vant
```

2. 按需引入组件

在基于 `vite`、`webpack` 或 `vue-cli` 的项目中使用 Vant 时，推荐安装 [unplugin-vue-components](https://github.com/antfu/unplugin-vue-components) 插件，它可以自动按需引入组件。

安装插件
```js
# 通过 npm 安装
npm i unplugin-vue-components -D

# 通过 yarn 安装
yarn add unplugin-vue-components -D

# 通过 pnpm 安装
pnpm add unplugin-vue-components -D
```

配置插件

如果是基于 `vite` 的项目，在 `vite.config.js` 文件中配置插件：


```js
import vue from '@vitejs/plugin-vue';
import Components from 'unplugin-vue-components/vite';
import { VantResolver } from 'unplugin-vue-components/resolvers';

export default {
  plugins: [
    vue(),
    Components({
      resolvers: [VantResolver()],
    }),
  ],
};
```

如果是基于 `vue-cli` 的项目，在 `vue.config.js` 文件中配置插件：

```js
const { VantResolver } = require('unplugin-vue-components/resolvers');
const ComponentsPlugin = require('unplugin-vue-components/webpack');

module.exports = {
  configureWebpack: {
    plugins: [
      ComponentsPlugin({
        resolvers: [VantResolver()],
      }),
    ],
  },
};

```

如果是基于 `webpack` 的项目，在 `webpack.config.js` 文件中配置插件：


```js
const { VantResolver } = require('unplugin-vue-components/resolvers');
const ComponentsPlugin = require('unplugin-vue-components/webpack');

module.exports = {
  plugins: [
    ComponentsPlugin({
      resolvers: [VantResolver()],
    }),
  ],
};
```

3. 引入组件


```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { Button, Loading, Empty } from 'vant'

const app = createApp(App)
app
    .use(Button)
    .use(Loading)
    .use(Empty)
    .use(router)
    .mount('#app')
```

4. 引入函数组件的样式

Vant 中有个别组件是以函数的形式提供的，包括 `Toast`，`Dialog`，`Notify` 和 `ImagePreview` 组件。在使用函数组件时，`unplugin-vue-components` 无法自动引入对应的样式，因此需要手动引入样式。


```js
// Toast
import { Toast } from 'vant';
import 'vant/es/toast/style';

// Dialog
import { Dialog } from 'vant';
import 'vant/es/dialog/style';

// Notify
import { Notify } from 'vant';
import 'vant/es/notify/style';

// ImagePreview
import { ImagePreview } from 'vant';
import 'vant/es/image-preview/style';
```

#### vue-router

Vue Router 是 [Vue.js](http://v3.vuejs.org/) 的官方路由。它与 Vue.js 核心深度集成，让用 Vue.js 构建单页应用变得轻而易举。功能包括：

-   嵌套路由映射
-   动态路由选择
-   模块化、基于组件的路由配置
-   路由参数、查询、通配符
-   展示由 Vue.js 的过渡系统提供的过渡效果
-   细致的导航控制
-   自动激活 CSS 类的链接
-   HTML5 history 模式或 hash 模式
-   可定制的滚动行为
-   URL 的正确编码

安装


```js
yarn add vue-router
```

src/router/index.js


```js
import { createRouter, createWebHashHistory } from 'vue-router'

export const constantRoutes = [
    {
        path: '/',
        component: () => import('@/views/index.vue'),
    },
]

const router = createRouter({
    history: createWebHashHistory(),
    routes: constantRoutes
})

export default router
```

引入


```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { Button, Loading, Empty } from 'vant'

const app = createApp(App)
app
    .use(Button)
    .use(Loading)
    .use(Empty)
    .use(router)
    .mount('#app')
```

#### Pinia

Pinia [最初是在 2019 年 11 月左右重新设计使用](https://github.com/vuejs/pinia/commit/06aeef54e2cad66696063c62829dac74e15fd19e) [Composition API](https://github.com/vuejs/composition-api) 。从那时起，最初的原则仍然相同，但 Pinia 对 Vue 2 和 Vue 3 都有效，并且不需要您使用组合 API。 除了安装和 SSR 之外，两者的 API 都是相同的，并且这些文档针对 Vue 3，并在必要时提供有关 Vue 2 的注释，以便 Vue 2 和 Vue 3 用户可以阅读！

安装

```js
yarn add pinia
```

src/store/setting.js


```js
import { defineStore } from 'pinia'
export const useSettingsStore = defineStore('settings', {
    state: () => {
        return {
            count: 0
        }
    },
    actions: {
        addCount(){
            this.count++
        }
    }
})
```

页面使用


```js
<script setup>
import { useSettingsStore } from '@/stores/setting'

const settingsStore = useSettingsStore()

const clickCountButton = () => {
  settingsStore.addCount()
}
</script>
```

#### 配置别名

vite.config.js


```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import Components from 'unplugin-vue-components/vite';
import { VantResolver } from 'unplugin-vue-components/resolvers';
import path from 'path'

function resolve(dir) {
  return path.join(__dirname, dir)
}

// https://vitejs.dev/config/
export default defineConfig({
  resolve: {
    alias: {
      '@': resolve('src')
    }
  },
  plugins: [
    vue(),
    Components({
      resolvers: [VantResolver()],
    }),
  ]
})
```

#### 结尾

以上就是简单配置一个基于vue3+vite3+vant搭建移动端简易模版, 大家可以在此基础上去加各种插件，加代码规范等等，有任何问题可以在评论区和我沟通，我将第一时间反馈。