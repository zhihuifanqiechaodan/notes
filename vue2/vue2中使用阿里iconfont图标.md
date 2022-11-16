**我们在使用vue进行项目开发的时候,很多时候会手撸样式或者使用ui框架,这时候ui框架提供的原生icon图标可能会满足我们现有的需求,这时候我们就可以引用第三方图标库来达到我们的需求。**

这里讲解的是如何在vue中使用阿里图标;
```
阿里图标库有三种使用方式（Unicode、Font class、Symbol)
这里主要说明 Font class 的使用方法（其他方法类似）
```
### 一、引入
* 登录阿里图标官网注册一个帐号,在图标库中选取自己需要的图标加入购物车

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bb7dbdc9b5be~tplv-t2oaga2asx-image.image)
* 点击购物车查看我已经添加到购物车的图标,点击添加至项目,没有项目新建一个

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bbae6fd05f92~tplv-t2oaga2asx-image.image)
* 进入我的项目中,将图标下载至本地,在vue项目中assets文件下新建iconfont文件夹将下载的图标复制到这里

```
因为这里主要说明Font class 的使用方法,所以只需要拷贝这两个文件（其他方法类似）
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bec9394747a8~tplv-t2oaga2asx-image.image)
* 注意这里需要将iconfont.css文件中引用的路径全部修改为'./iconfont.ttf'

```
这里全部改为'./iconfont.ttf'是因为我们当前只使用Font class 的使用方法（其他方法类似）
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bf81b862e3d8~tplv-t2oaga2asx-image.image)
* 在main.js文件中引入阿里图标,将其挂载到全局,以后每个页面都可以使用
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bee0a5b8575c~tplv-t2oaga2asx-image.image)
```
这里引入阿里图标样式可能会报错,原因是没有css-loader依赖包,安装一下就可以了
npm install css-loader -S
```
* 这里阿里图标的引入就全部完成了,接下来使用方式如图:

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bf0acce32c39~tplv-t2oaga2asx-image.image)
****
**这里使用阿里图标都需要加iconfont前缀类名,否则不会显示出来的,当然这个类名是可以在阿里图标官网自己编辑的,默认都是iconfont**
* 修改默认iconfont前缀类名

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bf30de05180a~tplv-t2oaga2asx-image.image)
* 修改Font Famliy 修改为myicon,点击保存重新下载至本地替换当前阿里图标即可

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bf3f690f3561~tplv-t2oaga2asx-image.image)
* 这时候使用图标时前缀加myicon即可了

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/7/26/16c2bf5f7a1c6ffc~tplv-t2oaga2asx-image.image)
**以上就是如何在vue中引用阿里图标的详细步骤了,完结撒花。**