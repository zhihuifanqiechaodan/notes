## 天赋决定上限，努力决定下限
每天一个小技巧，每天一个优化点。慢慢积累慢慢总结。
## 生产环境去除console
* babel-plugin-transform-remove-console  [官方地址](https://www.npmjs.com/package/babel-plugin-transform-remove-console)

```
yarn add babel-plugin-transform-remove-console —D // 安装包
```

**在 babel.config.js 中配置**
```
// 所有生产环境
const prodPlugin = []

if (process.env.NODE_ENV === 'production') {

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
按照以上配置内容，打包后的项目只会保留error和warn的console。
****
## ❤️ 看完帮个忙
如果你觉得这篇内容对你挺有启发，我想邀请你帮我个小忙：

1. **点赞**，让更多的人也能看到这篇内容（收藏不点赞，都是耍流氓 -_-）

2. **关注公众号「番茄学前端」**，我会定时更新和发布前端相关信息和项目案例经验供你参考。

3. **加个好友**， 虽然帮不上你大忙，但是一些业务问题大家可以探讨交流。
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/2/22/1706c520546b2c2d~tplv-t2oaga2asx-image.image)