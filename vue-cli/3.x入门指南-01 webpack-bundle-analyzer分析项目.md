## 天赋决定上限，努力决定下限
每天一个小技巧，每天一个优化点。慢慢积累慢慢总结。
## 项目运行开启图形化分析项目优化
* webpack-bundle-analyzer [官方地址](https://www.npmjs.com/package/babel-plugin-transform-remove-console)
```
yarn add webpack-bundle-analyzer -D
```
**在vue.config文件中配置**
```
    chainWebpack(config) {
        config
            .plugin('webpack-bundle-analyzer')
            .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin)
    }
```
按照以上配置内容，项目打包或者开发环境运行都会开启8888端口跑起来图形化分析页面。方便你优化。当然你也可以设置只在生产环境才开启，简单实用。
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e3a4b5ae66244bf698224e1eb9aab0c9~tplv-k3u1fbpfcp-zoom-1.image)

****
## ❤️ 看完帮个忙
如果你觉得这篇内容对你挺有启发，我想邀请你帮我个小忙：

1. **点赞**，让更多的人也能看到这篇内容（收藏不点赞，都是耍流氓 -_-）

2. **关注公众号「番茄学前端」**，我会定时更新和发布前端相关信息和项目案例经验供你参考。

3. **加个好友**， 虽然帮不上你大忙，但是一些业务问题大家可以探讨交流。
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/22/1706c520546b2c2d~tplv-t2oaga2asx-image.image)