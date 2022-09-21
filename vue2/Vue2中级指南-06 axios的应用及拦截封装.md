## 一 概述
在 vue2.0 项目中， 我们主要通过使用 axios 进行 http 请求。
axios 是一个基于 Promise 的 HTTP 库， 可以在 node 和浏览器中使用。
### 特征
* 从浏览器中创建XMLHttpRequests。
* 从 node.js 创建 http 请求。
* 支持 PromiseAPI
* 支持拦截请求和响应
* 支持转换请求数据和响应数据
* 可以取消请求
* 自动转换 JSON 数据
* 客户端支持防御 XSRF
## 二 axios拦截
1. npm 安装 axios

```
npm install axios --save
```
2. 搭建结构，新建一个vue-axios文件夹， 并且初始化文件夹运行 npm init -y
3. 在该文件夹下新建一个 src 文件夹，src文件夹下新建axios文件夹， 并在 axios 文件下新建index.js文件

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/3/26/169b84fb25106ca6~tplv-t2oaga2asx-image.image)
4. index.js中进行axios的拦截封装


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/3/26/169b8964b479fec4~tplv-t2oaga2asx-image.image)


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/3/26/169b896e154251f6~tplv-t2oaga2asx-image.image)
5. 在 main.js 入口文件中引入 axios.js 文件

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/3/26/169b897fc5416746~tplv-t2oaga2asx-image.image)
在vue中， 通过 this.$axios.get('路径')方式使用

### 以上只是一个简单的封装， 具体详细封装可以根据项目需要自行查看官网进行封装