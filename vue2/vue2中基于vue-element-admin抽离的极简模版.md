---
# 主题列表：juejin, github, smartblue, cyanosis, channing-cyan, fancy, hydrogen, condensed-night-purple, greenwillow, v-green, vue-pro, healer-readable, mk-cute
# 贡献主题：https://github.com/xitu/juejin-markdown-themes
theme: juejin
highlight:
---

## 基于vue-element-admin抽离的极简模版
因为公司项目用到的后台项目在逐渐的重构升级，因此将花裤衩大佬的开源后台做了一个抽离，只保留了基础架子。其余的组件页面根据需求开发，并且升级了部分包。

[项目地址](https://github.com/zhihuifanqiechaodan/vue-element-admin-template)
[在线预览](http://www.zhihuifanqiechaodan.com/)
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d3bc3c00e1943178f9cccfd53d8ff42~tplv-k3u1fbpfcp-watermark.image)
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/15d60a82f73c4f53987cff21ae7632ca~tplv-k3u1fbpfcp-watermark.image)

### 极简模版保留的功能
* utils/error-log.js	错误日志
* src/permission.js	路由权限控制
* utils/storage-service.js	浏览器缓存接口
* utils/httpclient-service.js	axios请求封装
* utils/get-page-title.js	全局页面标题设置
* utils/validate.js 部分正则验证
### src/services 文件夹封装了项目中使用的所有请求
```js
// src/services/index.js
import ServiceFactory from './service-factory'
const getService = (serviceName) => ServiceFactory[serviceName]
export default getService
```
```js
// src/services/user-service.js
import { request } from '@/utils/httpclient-service'
import {
    API_USER
} from '@/config'

/**
 * @method addLogin 用户登录
 * @param {*} data 登录信息
 */
function addUserLogin(data) {
    return request.post(API_USER + '/login', data)
}

/**
 * @method getUserInfo 获取用户信息
 * @param {*} param0 token
 */
function getUserInfo({ token }) {
    return request.get(API_USER + '/info', { params: { token } })
}

export default {
    addUserLogin,
    getUserInfo
}
```
```js
// src/services/service-factory.js
import UserService from './user-service'
import EchartService from './echart-service'

export default {
    UserService,
    EchartService
}
```
```js
// 页面中使用方式如下：
this.$getService("EchartService").getEchartList()
```
**参考代码**
### 打包优化处理
* babel-plugin-transform-remove-console => 生产环境去除console
```js
// .babel.config.js
const prodPlugin = []
const isProd = process.env.NODE_ENV === 'production'

if (isProd) {
  // 如果是生产环境，则自动清理掉打印的日志，但保留error 与 warn
  prodPlugin.push([
    'transform-remove-console',
    {
      // 保留 console.error 与 console.warn
      exclude: ['error', 'warn']
    }
  ])
}
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset'
  ],
  plugins: [
    ...prodPlugin
  ]
}

```
* compression-webpack-plugin => 生产环境开启gzip压缩
```js
// .vue.config.js
// 开启Gzip
config
  .when(isProd, config => {
    config
      .plugin('gzip-plugin')
      .use('compression-webpack-plugin', [{
        filename: '[path].gz[query]',
        algorithm: 'gzip',
        test: /\.js$|\.html$|\.json$|\.css$|\.ttf$/,
        threshold: 10240, // 只有大小大于该值的资源会被处理
        minRatio: 0.8, // 只有压缩率小于这个值的资源才会被处理
        deleteOriginalAssets: false // 删除源文件
      }])
      .end()
  })
```
* externals 生产环境cdn引入vue element-ui echarts 提升打包后页面加载速度
```js
configureWebpack: {
  // provide the app's title in webpack's name field, so that
  // it can be accessed in index.html to inject the correct title.
  name: name,
  resolve: {
    alias: {
      '@': resolve('src')
    }
  },
  externals: {
    'vue': 'Vue',
    'element-ui': 'ELEMENT',
    'echarts': 'echarts'
  }
},
chainWebpack(config) {
  const cdn = {
      css: [
        // element-ui css
        'https://unpkg.com/element-ui/lib/theme-chalk/index.css'
      ],
      js: [
        // vue must at first!
        'https://unpkg.com/vue/dist/vue.js',
        // element-ui js
        'https://unpkg.com/element-ui/lib/index.js',
        // echart
        'https://cdn.staticfile.org/echarts/4.3.0/echarts.min.js'
      ]
    }
    config.plugin('html')
      .tap(args => {
        args[0].cdn = cdn
        return args
    })
}
```