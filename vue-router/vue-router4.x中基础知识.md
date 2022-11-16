> Vue Router 是 [Vue.js](http://v3.vuejs.org/) 的官方路由。它与 Vue.js 核心深度集成，让用 Vue.js 构建单页应用变得轻而易举。功能包括：

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

### 第一章 入门

> router 路由，因为vue是单页应用不会有那么多html让我们跳转所以要使用路由做页面的跳转，Vue 路由允许我们通过不同的 URL 访问不同的内容，通过 Vue 可以实现多视图的单页Web应用。

构建前端项目

```js
npm init vue@latest
//或者
npm init vite@latest
```

安装vue-router

```js
// 目前默认版本是4.x版本
yarn add vue-router
```

在src目录下面新建router文件夹，然后在router文件夹下面新建index.js

```js
//引入路由对象
import { createRouter, createWebHistory, createWebHashHistory, createMemoryHistory, RouteRecordRaw } from 'vue-router'
 
//vue2 mode history => vue3 createWebHistory
//vue2 mode  hash  => vue3  createWebHashHistory
//vue2 mode abstact => vue3  createMemoryHistory
 
// 定义一些路由
// 每个路由都需要映射到一个组件。
const routes: Array<RouteRecordRaw> = [{
    path: '/',
    component: () => import('../components/a.vue')
},{
    path: '/b',
    component: () => import('../components/b.vue')
}]
 
 
 
const router = createRouter({
    history: createWebHistory(),
    routes
})
 
//导出router
export default router
```

* router-link

> 请注意，我们没有使用常规的 `a` 标签，而是使用一个自定义组件 `router-link` 来创建链接。这使得 Vue Router 可以在不重新加载页面的情况下更改 URL，处理 URL 的生成以及编码。我们将在后面看到如何从这些功能中获益。

* router-view

> `router-view` 将显示与 url 对应的组件。你可以把它放在任何地方，以适应你的布局。

```js
<template>
  <div>
    <h1>只会番茄炒蛋</h1>
    <div>
      <!--使用 router-link 组件进行导航 -->
      <!--通过传递 `to` 来指定链接 -->
      <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
      <router-link to="/">跳转a</router-link>
      <router-link style="margin-left:200px" to="/b">跳转b</router-link>
    </div>
    <hr />
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
  </div>
</template>
```

最后在main.js挂载
```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'


createApp(App)
    .use(router)
    .mount('#app')

```

### 第二章 命名路由 or 编程式导航

#### 命名路由

除了path之外，还可以为任何路由提供name。这有以下几点优势：

-   没有硬编码的 URL
-   `params` 的自动编码/解码。
-   防止你在 url 中出现打字错误。
-   绕过路径排序（如显示一个）

```js
const routes = [
    {
        path: '/',
        name: 'A',
        component: () => import('../components/a.vue')
    },
    {
        path: '/b',
        name: 'B',
        component: () => import('../components/b.vue')
    },
]
```

router-link跳转的方式可以变更对象

```js
<template>
  <div>
    <h1>只会番茄炒蛋</h1>
    <div>
      <!--使用 router-link 组件进行导航 -->
      <!--通过传递 `to` 来指定链接 -->
      <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
      <router-link :to="{ name: 'A' }">跳转a</router-link>
      <router-link style="margin-left:200px" :to="{ name: 'B' }">跳转b</router-link>
    </div>
    <hr />
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
  </div>
</template>
```

#### 编程式导航

> 除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

* 字符串模式

```js
import { useRouter } from 'vue-router'

const router = useRouter()

const navigateTo = () => {
  router.push('/')
}
```

* 对象模式

```js
import { useRouter } from 'vue-router'

const router = useRouter()

const navigateTo = () => {
  router.push({
    path: '/'
  })
}
```

* 命名路由模式

```js
import { useRouter } from 'vue-router'

const router = useRouter()

const navigateTo = () => {
  router.push({
    name: 'A'
  })
}
```

* a标签跳转

> 直接通过a href也可以跳转, 但是会刷新页面

```js
 <a href="/b">a标签跳转</a>
```

### 第三章 历史记录

**replace的使用**

> 采用replace进行页面的跳转会同样也会创建渲染新的Vue组件，但是在history中不会重复保存记录，而是替换原有的vue组件；

* router-link 的使用方式

```js
<router-link replace :to="{ name: 'A' }">跳转a</router-link>
<router-link replace style="margin-left:200px" :to="{ name: 'B' }">跳转b</router-link>
```

* 编程式导航

```js
import { useRouter } from 'vue-router'

const router = useRouter()

const redirectTo = () => {
  router.replace({
    name: 'A'
  })
}
```

* 横跨历史

该方法采用了一个整数作为参数，表示在历史堆栈中前进或后头多少步

```js
import { useRouter } from 'vue-router'

const router = useRouter()

const navigateTo = () => {
  // 前进，数量不限于1
  // router.go(1)
  
  // 后退，数量不限于1
  // router.go(-1)
  
  // 后退
  router.back()
}
```

### 第四章 路由传参

#### query路由传参

> 编程式导航 使用router push 或者 replace 的时候 改为对象形式新增query属性

```js
import { useRouter } from 'vue-router'

const Router = useRouter()

const navigateTo = () => {
  Router.push({
    name: 'A',
    query: {
      name: '只会番茄炒蛋',
      age: 18
    }
  })
}
```

接受参数使用 useRoute 中的query

```js
import { useRoute } from 'vue-router'

const route = useRoute()
```

```js
<div>名字：{{route.query.name}}</div>
<div>年龄：{{route.query.age}}</div>
```

#### 动态路由传参

> 很多时候，我们需要将给定匹配模式的路由映射到同一个组件。例如，我们可能有一个 `User` 组件，它应该对所有用户进行渲染，但用户 ID 不同。在 Vue Router 中，我们可以在路径中使用一个动态字段来实现，我们称之为路径参数

```js
const routes = [
    {
        path: '/user/:id', // 动态字段以冒号开始
        name: 'User',
        component: () => import('../components/user.vue')
    }
]
```

现在像 `/users/johnny` 和 `/users/jolyne` 这样的 URL 都会映射到同一个路由。

*路径参数* 用冒号 `:` 表示。当一个路由被匹配时，它的 *params* 的值将在每个组件中以 `this.$route.params` 的形式暴露出来。

```js
import { useRoute } from 'vue-router'

const route = useRoute()

console.log(route.params)
```

**二者的区别**

- query 传参配置的是 path，而 params 传参配置的是name，在 params中配置 path 无效

- query 在路由配置不需要设置参数，而 params 必须设置

- query 传递的参数会显示在地址栏中

- params传参刷新会无效，但是 query 会保存传递过来的值，刷新不变 ;

- 路由配置

### 第五章 嵌套路由

> 一些应用程序的 UI 由多层嵌套的组件组成。在这种情况下，URL 的片段通常对应于特定的嵌套组件结构。

```js
const routes = [
    {
        path: '/user',
        name: 'User',
        component: () => import('../components/user.vue'),
        children: [{
            path: 'man',
            name: 'Man',
            component: () => import('../components/man.vue')
        }, {
            path: 'woman',
            name: 'WoMan',
            component: () => import('../components/woman.vue')
        }]
    },
]
```
如你所见，`children` 配置只是另一个路由数组，就像 `routes` 本身一样。因此，你可以根据自己的需要，不断地嵌套视图。

>! 不要忘记在嵌套路由的页面增加router-view

### 第六章 命名视图

命名视图可以在同一级（同一个组件）中展示更多的路由视图，而不是嵌套显示。 命名视图可以让一个组件中具有多个路由渲染出口，这对于一些特定的布局组件非常有用。 命名视图的概念非常类似于“具名插槽”，并且视图的默认名称也是 `default`。

> 一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 `components` 配置 (带上 **s**)

```js
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'
 
 
const routes = [
    {
        path: "/",
        components: {
            default: () => import('../components/layout/menu.vue'),
            header: () => import('../components/layout/header.vue'),
            content: () => import('../components/layout/content.vue'),
        }
    },
]
 
const router = createRouter({
    history: createWebHistory(),
    routes
})
 
export default router
```

对应router-view 通过name 对应组件

```js
<div>
    <router-view></router-view>
    <router-view name="header"></router-view>
    <router-view name="content"></router-view>
</div>
```

### 第七章 重定向 or 别名

#### 重定向

* 字符串形式配置

> 当访问/user 会重定向到 /user/man

```js
const routes = [
    {
        path: '/user',
        name: 'User',
        redirect: '/user/man',
        component: () => import('../components/user.vue'),
        children: [{
            path: 'man',
            name: 'Man',
            component: () => import('../components/man.vue')
        }, {
            path: 'woman',
            name: 'WoMan',
            component: () => import('../components/woman.vue')
        }]
    },
]
```

* 对象形式配置

```js
const routes = [
    {
        path: '/user',
        name: 'User',
        redirect: { path: '/user/man' },
        component: () => import('../components/user.vue'),
        children: [{
            path: 'man',
            name: 'Man',
            component: () => import('../components/man.vue')
        }, {
            path: 'woman',
            name: 'WoMan',
            component: () => import('../components/woman.vue')
        }]
    },
]
```

* 函数形式（可以传递参数）

```js
const routes = [
    {
        path: '/user',
        name: 'User',
        redirect: (to) => {
            return {
                path: '/user/man',
                query: to.query
            }
        },
        component: () => import('../components/user.vue'),
        children: [{
            path: 'man',
            name: 'Man',
            component: () => import('../components/man.vue')
        }, {
            path: 'woman',
            name: 'WoMan',
            component: () => import('../components/woman.vue')
        }]
    },
]
```

#### 别名

别名，意味着当用户访问别名和访问当前路径显示的组件是一样的。

```js
const routes = [
    {
        path: '/user',
        name: 'User',
        alias: ['/user1', '/user2'],
        redirect: (to) => {
            return {
                path: '/user/man',
                query: to.query
            }
        },
        component: () => import('../components/user.vue'),
        children: [{
            path: 'man',
            name: 'Man',
            component: () => import('../components/man.vue')
        }, {
            path: 'woman',
            name: 'WoMan',
            component: () => import('../components/woman.vue')
        }]
    },
]
```

### 第八章 导航首位

* 全局前置守卫
```js
router.beforeEach((to, form, next) => {
    console.log(to, form);
    next()
})
```

每个守卫方法接收三个参数：

```js
to: Route， 即将要进入的目标 路由对象；
from: Route，当前导航正要离开的路由；
next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
```
使用案例

```js
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

const whileList = ['/']
 
router.beforeEach((to, from, next) => {
    NProgress.start()
    let token = localStorage.getItem('token')
    //白名单 有值 或者登陆过存储了token信息可以跳转 否则就去登录页面
    if (whileList.includes(to.path) || token) {
        next()
    } else {
        next({
            path:'/login'
        })
    }
})
```

* 全局后置守卫

> 和前置守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

使用场景一般是路由跳转结束取消loading动画

```js
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

router.afterEach((to,from)=>{
    NProgress.done()
})
```

### 第九章 路由元信息

> 通过路由记录的 `meta` 属性可以定义路由的**元信息**。使用路由元信息可以在路由中附加自定义的数据例如：

-   权限校验标识。
-   路由组件的过渡名称。
-   路由组件持久化缓存 (keep-alive) 的相关配置。
-   标题名称

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      component: () => import('@/views/Login.vue'),
      meta: {
        title: "登录", icon: "round"
      }
    },
    {
      path: '/index',
      component: () => import('@/views/Index.vue'),
      meta: {
        title: "首页", icon: "eyes"
      }
    }
  ]
})
```

### 第十章 路由的滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。vue-router 可以自定义路由切换时页面如何滚动。

当创建一个 Router 实例，你可以提供一个 `scrollBehavior` 方法

> scrollBehavior 方法接收 to 和 from 路由对象。第三个参数 savedPosition 当且仅当 popstate 导航 (通过浏览器的 前进/后退 按钮触发) 时才可用。

```js
const router = createRouter({
  history: createWebHistory(),
  scrollBehavior: (to, from, savePosition) => {
    if (savePosition) {
      return savePosition
    } else {
      return {
        top: 0
      }
    }

  }
})
```

### 第十一章 动态路由

> 对路由的添加通常是通过 [`routes` 选项](https://router.vuejs.org/zh/api/#routes)来完成的，但是在某些情况下，你可能想在应用程序已经运行的时候添加或删除路由。

#### 添加路由

动态路由主要通过两个函数实现。`router.addRoute()` 和 `router.removeRoute()`。它们**只**注册一个新的路由，也就是说，如果新增加的路由与当前位置相匹配，就需要你用 `router.push()` 或 `router.replace()` 来**手动导航**，才能显示该新路由。我们来看一个例子：

想象一下，只有一个路由的以下路由：

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [{ path: '/:articleName', component: Article }],
})
```

进入任何页面，`/about`，`/store`，或者 `/3-tricks-to-improve-your-routing-code` 最终都会呈现 `Article` 组件。如果我们在 `/about` 上添加一个新的路由：

```js
router.addRoute({ path: '/about', component: About })
```

页面仍然会显示 `Article` 组件，我们需要手动调用 `router.replace()` 来改变当前的位置，并覆盖我们原来的位置（而不是添加一个新的路由，最后在我们的历史中两次出现在同一个位置）：

```js
router.addRoute({ path: '/about', component: About })
// 我们也可以使用 this.$route 或 route = useRoute() （在 setup 中）
router.replace(router.currentRoute.value.fullPath)
```

记住，如果你需要等待新的路由显示，可以使用 `await router.replace()`。

#### 删除路由

有几个不同的方法来删除现有的路由：

* 通过添加一个名称冲突的路由。如果添加与现有途径名称相同的途径，会先删除路由，再添加路由：

```js
router.addRoute({ path: '/about', name: 'about', component: About })
// 这将会删除之前已经添加的路由，因为他们具有相同的名字且名字必须是唯一的
router.addRoute({ path: '/other', name: 'about', component: Other })
```

* 通过调用 `router.addRoute()` 返回的回调：

```js
const removeRoute = router.addRoute(routeRecord)
removeRoute() // 删除路由如果存在的话
```

当路由没有名称时，这很有用。

* 通过使用 `router.removeRoute()` 按名称删除路由：

```js
router.addRoute({ path: '/about', name: 'about', component: About })
// 删除路由
router.removeRoute('about')
```

需要注意的是，如果你想使用这个功能，但又想避免名字的冲突，可以在路由中使用 `Symbol` 作为名字。

当路由被删除时，**所有的别名和子路由也会被同时删除**

#### 添加嵌套路由

要将嵌套路由添加到现有的路由中，可以将路由的 *name* 作为第一个参数传递给 `router.addRoute()`，这将有效地添加路由，就像通过 `children` 添加的一样：

```js
router.addRoute({ name: 'admin', path: '/admin', component: Admin })
router.addRoute('admin', { path: 'settings', component: AdminSettings })
```
这等效于：

```js
router.addRoute({
  name: 'admin',
  path: '/admin',
  component: Admin,
  children: [{ path: 'settings', component: AdminSettings }],
})
```

#### 查看现有路由

Vue Router 提供了两个功能来查看现有的路由：

-   [`router.hasRoute()`](https://router.vuejs.org/zh/api/#hasroute)：检查路由是否存在。
-   [`router.getRoutes()`](https://router.vuejs.org/zh/api/#getroutes)：获取一个包含所有路由记录的数组。