![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/964f9f1aa9c44bcea4b4f45de3fa3eba~tplv-k3u1fbpfcp-watermark.image?)

> svg图片在项目中使用的非常广泛，今天记录一下我是如何在vue3 + vite 中使用svg图标。

### vite-plugin-svg-icons
-   **预加载** 在项目运行时就生成所有图标,只需操作一次 dom
-   **高性能** 内置缓存,仅当文件被修改时才会重新生成

#### 安装 

> **node version:**  >=12.0.0 **vite version:**  >=2.0.0

```js
yarn add vite-plugin-svg-icons -D
# or
npm i vite-plugin-svg-icons -D
# or
pnpm install vite-plugin-svg-icons -D
```

#### 使用

-   vite.config.ts 中的配置插件

```js
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'
import path from 'path'

export default () => {
  return {
    plugins: [
      createSvgIconsPlugin({
        // 指定需要缓存的图标文件夹
        iconDirs: [path.resolve(process.cwd(), 'src/icons')],
        // 指定symbolId格式
        symbolId: 'icon-[dir]-[name]',

        /**
         * 自定义插入位置
         * @default: body-last
         */
        // inject?: 'body-last' | 'body-first'

        /**
         * custom dom id
         * @default: __svg__icons__dom__
         */
        // customDomId: '__svg__icons__dom__',
      }),
    ],
  }
}
```

-   在 src/main.js 内引入注册脚本

```js
import 'virtual:svg-icons-register'
```

#### 如何在组件中使用

##### 创建SvgIcon组件

/src/components/SvgIcon/index.vue

```js
<template>
  <svg aria-hidden="true" class="svg-icon" :width="props.size" :height="props.size">
    <use :xlink:href="symbolId" :fill="props.color" />
  </svg>
</template>

<script setup>
import { computed } from 'vue'
const props = defineProps({
  prefix: {
    type: String,
    default: 'icon'
  },
  name: {
    type: String,
    required: true
  },
  color: {
    type: String,
    default: '#333'
  },
  size: {
    type: String,
    default: '1em'
  }
})

const symbolId = computed(() => `#${props.prefix}-${props.name}`)
</script>
```

##### icons目录结构

```js
# src/icons

- icon1.svg
- icon2.svg
- icon3.svg
- dir/icon1.svg
```

##### 全局注册组件

```js
# src/main.js

import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

import svgIcon from "@/components/SvgIcon/index.vue";
import 'virtual:svg-icons-register'

createApp(App)
    .use(ElementPlus)
    .use(router)
    .component('svg-icon', svgIcon)
    .mount('#app')

```

##### 页面使用

```js
<template>
    <svg-icon v-if="props.icon" :name="props.icon" />
    <span v-if="props.title" slot='title'>{{ props.title }}</span>
</template>

<script setup>

const props = defineProps({
    icon: {
        type: String,
        default: ''
    },
    title: {
        type: String,
        default: ''
    }
})
</script>
```

#### 获取所有 SymbolId

```js
import ids from 'virtual:svg-icons-names'
// => ['icon-icon1','icon-icon2','icon-icon3']
```