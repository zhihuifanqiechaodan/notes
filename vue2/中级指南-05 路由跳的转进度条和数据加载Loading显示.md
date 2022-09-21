我们在Vue项目中路由跳转时希望出现一个进度条来显示当前跳转状态,或者在数据发起请求时有个loading提示框来提示当前正在加载数据,本篇文章简单介绍一下我在项目中的使用方法。

### 路由跳转进度条
NProgress是页面跳转时出现在浏览器顶部的进度条 
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/2/16cf093d4834d6b0~tplv-t2oaga2asx-image.image)

* 官网：http://ricostacruz.com/nprogress/ 

* github：https://github.com/rstacruz/nprogress
* NProgress 安装

```
npm install --save nprogress
```
* NProgress 使用
```
// 如果在main.js引入并挂载在Vue实例上,请最在顶部引入,否则会出现问题。
// NProgress最重要两个API就是start()和done()，基本一般用这两个就足够了。
// router.js页面
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'

router.beforeEach((to, from, next) => {
  NProgress.start() // 显示进度条
  next()
})

router.afterEach(() => {
  NProgress.done() // 完成进度条
})
```
* NProgress 高级用法

```
// (1)百分比：通过设置progress的百分比，调用 .set(n)来控制进度，其中n的取值范围为0-1。
NProgress.set(0.0)   
NProgress.set(0.4)
NProgress.set(1.0)

// (2)递增：要让进度条增加，只要调用.inc()。这会产生一个随机增量，但不会让进度条达到100%。
// 此函数适用于图片加载或其他类似的文件加载。
NProgress.inc()

// (3)强制完成：通过传递 true 参数给done()，使进度条满格，即使它没有被显示
NProgress.done(true)
```
* NProgress 其他配置

```
// (1)minimum：设置最低百分比
NProgress.configure({minimum:0.1})

// (2)template：改变进度条的HTML结构。为保证进度条能正常工作，需要元素拥有role=’bar’属性。
NProgress.configure({
    template:"<div class='....'>...</div>"
})

// (3)ease：调整动画设置，ease可传递CSS3缓冲动画字符串例如:
// (ease、linear、ease-in、ease-out、ease-in-out、cubic-bezier)
// speed为动画速度（单位ms）
```
以上内容就是NProgress的用法了,是不是非常简单,大家动手实践一下很容易就明白了。
### 数据请求loading提示(Element-ui为例)
Element 提供了两种调用 Loading 的方法：指令和服务。对于自定义指令v-loading，只需要绑定Boolean即可。默认状况下，Loading 遮罩会插入到绑定元素的子节点，通过添加body修饰符，可以使遮罩插入至 DOM 中的 body 上。
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/2/16cf0f625aaba4be~tplv-t2oaga2asx-image.image)
* 自定义指令v-loading
```
// 通常展示表格数据的时候,会添加一个loading提示,当数据完全请求回来时关闭loading提示
<template>
  <div class="app-container">
    <!-- 当前元素添加loading提示 -->
    <el-tabs type="border-card" v-model="activeKey" v-loading="loading">
      <el-tab-pane
        v-for="item of tabsLisgt"
        :key="item.key"
        :label="item.label"
        :name="item.name"
        :value="item.key"
      >
      <!-- 自定义的组件 -->
        <TabPane :tabsContent="filterstabsContent" :activeKey="activeKey" />
      </el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
import filtersDate from "@/utils/filtersTime";
import TabPane from "./components/TabPane";
export default {
  components: {
    TabPane
  },
  data() {
    return {
      // tabs标签列表
      tabsLisgt: [
        { label: "全部", key: "0" },
        { label: "一级权限", key: "1" },
        { label: "二级权限", key: "2" },
        { label: "三级权限", key: "3" }
      ],
      //   tabs标签页面内容
      tabsContent: [],
      activeKey: "1",
      loading: true
    };
  },
  computed: {
    // 根据tabs页面状态展示对应数据
    filterstabsContent() {
      if (this.activeKey === "0") {
        return this.tabsContent;
      } else {
        return this.tabsContent.filter(item => {
          return item.status === parseInt(this.activeKey);
        });
      }
    }
  },
  methods: {
    // 获取tabs页面展示内容
    async getTabsContent() {
      let result = await this.$getService("TabsService").getTabs();
      if (result && result.length > 0) {
        for (let i in result) {
          // 转换时间格式
          result[i].createTime = filtersDate(result[i].createTime);
        }
        this.tabsContent = result;
      }
      // 关闭loading显示
      this.loading = false;
    }
  },
  async mounted() {
    // 获取tabs页面展示内容
    await this.getTabsContent();
  }
};
</script>
```
* 服务方式
```
// Loading 还可以以服务的方式调用。引入 Loading 服务
import { Loading } from 'element-ui';

// 在需要调用时：
Loading.service(options);

// 其中 options 参数为 Loading 的配置项，具体见下。
// LoadingService 会返回一个 Loading 实例,可通过调用该实例的 close 方法来关闭它：
let loadingInstance = Loading.service({ fullscreen: true });
this.$nextTick(() => { // 以服务的方式调用的 Loading 需要异步关闭
  loadingInstance.close();
})
```
在我的项目中,我是通过以下方式来实现的

```
// 封装axios
import axios from 'axios'
import {API_BASE_URL} from '../config'
import store from '../store'
import {Loading} from 'element-ui'

var apiClient = axios.create({
  baseURL: API_BASE_URL
})
apiClient.defaults.withCredentials = true
// 封装请求
apiClient.interceptors.request.use(function (config) {
  // 开启loading提示
  let loadingInstance = Loading.service({ fullscreen: true })
  // 通过vuex来发起一个异步事件,保存当前开启状态和loading实例
  store.dispatch('loading', {loadingStatus: true, loadingInstance: loadingInstance})
  return config
}, function (error) {
  // 关闭loading提示
  store.dispatch('loading', {loadingStatus: false})
  return Promise.reject(error)
})
apiClient.interceptors.response.use(function (response) {
  // 关闭loading提示
  store.dispatch('loading', {loadingStatus: false})
  return response.data.data
}, function (error) {
  // 关闭loading提示
  store.dispatch('loading', {loadingStatus: false})
  return Promise.reject(error)
})

export { apiClient }

// actions.js
loading: function ({ commit }, data) {
  commit(types.LOADING, data)
}

// mutaions.js
[types.LOADING]: function (state, data) {
state.loadingStatus = data.loadingStatus
  if (!data.loadingStatus) {
    state.loadingInstance.close()
  } else {
    state.loadingInstance = data.loadingInstance
  }
}
```
以上就是通过Element-ui提供的两种loading显示案例,大家可以手动实践一下,方便理解