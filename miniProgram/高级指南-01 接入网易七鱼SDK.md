> 网易七鱼微信小程序访客端SDK插件是一个微信小程序端客服系统访客解决方案，既包含了客服聊天逻辑管理，也提供了聊天界面，开发者可方便的将客服功能集成到自己的 微信小程序 中。


### 接入说明

1.  在微信小程序后台 - 设置 - 第三方设置 - 插件管理中申请使用七鱼小程序sdk插件，七鱼提供的微信小程序插件名称为 QIYUSDK，详细使用插件的方式请参考微信小程序插件的使用方式，[微信使用插件文档 (opens new window)](https://developers.weixin.qq.com/miniprogram/dev/framework/plugin/using.html)。
2.  七鱼对外提供名字为chat的页面插件，请确保在配置企业appKey后再跳转到chat页面申请客服，[接入 Demo](https://qiyukf.com/docs/res/miniprogram-demo.zip)。

### 接入代码示例
引入插件代码包:使用插件前，使用者要在 app.json 中声明需要使用的插件，由于目前七鱼插件包的大小问题，推荐将插件放置到分包中引入和使用。
```js
// app.json

  "subPackages": [
      {
          "root": "packageG",
          "name": "packageG",
          "pages": [
            "pages/qiyu/index"
          ],
          "plugins": {
            "qiyuSdk": {
              "version": "1.4.7",
              "provider": "wxae5e29812005203f"
            }
          }
      }
  ]
```
#### 初始化企业appkey

> 配置企业的appKey采用接口插件对外接口的形式，使用requirePlugin引用插件后调用 _$configAppKey(appKey) 方法。

**注册网易七鱼获取App Key以及App Id**

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d384b3e01fdb446b8038e5273018fc3d~tplv-k3u1fbpfcp-watermark.image?)

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/574a28d7b05f4d1083eda27b60e260df~tplv-k3u1fbpfcp-watermark.image?)

**初始化**


```js
// packageG/pages/qiyu/index.js
import { initQiyuSdk, redirectTo } from '~/utils/index';
const { globalData } = getApp();

Page({
  /**
   * 生命周期函数--监听页面加载
   */
  async onLoad(options) {
    // 防止生成多个实例
    await initQiyuSdk();
    const eventChannel = this.getOpenerEventChannel();
    // 监听acceptDataFromOpenerPage事件，获取上一页面通过eventChannel传送到当前页面的数据
    eventChannel.on('acceptDataFromOpenerPage', (data) => {
        const { productInfo = null } = data;
        const { qiyuSdkInfo } = globalData;
        const { qiyuSdkInterface } = qiyuSdkInfo;
        qiyuSdkInterface._$configProduct(productInfo);
        redirectTo({ url: 'plugin://qiyuSdk/chat' });
      }
    });
  }
});
```

```js
// utils.index.js
/**
 * @method initQiyuSdk 初始化七鱼sdk
 */
export const initQiyuSdk = () => {
  return new Promise((reslove) => {
    const { globalData, systemInfo } = getApp();
    const { qiyuSdkInfo, userInfo } = globalData;
    if (!qiyuSdkInfo) {
      globalData.qiyuSdkInfo = {};

      const qiyuSdkInterface = requirePlugin('qiyuSdk');

      // 初始化企业appKey
      qiyuSdkInterface._$configAppKey(qiyuConfigAppKey);

      // 不是微信的appId，是七鱼后台生成的appId
      qiyuSdkInterface.__configAppId(qiyuConfigAppId);

      // 全面屏适配, 由于微信小程序本身的限制，导致用户全局配置navigationStyle: 'custom'时，会导致插件页面的导航栏被隐藏从而无法进行页面后退，并且由于微信小程序并没有相关api提供到插件开发者去控制导航栏，目前考虑到部分用户业务场景无法通过配置单独的页面进行导航栏显示/隐藏控制，故七鱼插件为开发者提供了全面屏配置参数
      qiyuSdkInterface._$configFullScreen(true);

      // 关闭默认链接的复制操作
      qiyuSdkInterface._$configAutoCopy(false);

      // 上报用户信息
      qiyuSdkInterface._$setUserInfo({
        userId: userInfo.uid,
        data: [
          { key: 'real_name', value: userInfo.nickName },
          { key: 'mobile_phone', value: '', value: '' },
          { key: 'email', value: '' },
          { index: 1, key: 'uid', label: 'uid', value: userInfo.uid },
          { index: 2, key: 'point', label: '积分', value: userInfo.point },
          { index: 3, key: 'fab', label: 'fab值', value: userInfo.fab },
          { index: 4, key: 'recommendedSize', label: '推荐尺码', value: userInfo.recommendedSize ? userInfo.recommendedSize : '未选择尺码' },
          { index: 5, key: 'fresh', label: '新用户', value: userInfo.fresh ? '是' : '否' },
          { index: 6, key: 'avatar', label: '头像', value: userInfo.avatar },
          { index: 7, key: 'isDesigner', label: '设计师', value: userInfo.isDesigner ? '是' : '否' },
          { index: 8, key: 'inviteUserCount', label: '邀请好友数量', value: userInfo.inviteUserCount },
          {
            index: 9,
            key: 'userStatus',
            label: '用户状态',
            value: {
              0: '正常',
              1: '注销',
              2: '禁登录',
              3: '禁言',
            }[userInfo.userStatus],
          },
          { index: 8, key: 'deviceid', label: '设备id', value: systemInfo.deviceid },
          { type: 'crm_param', key: 'form', value: 'miniprogram' },
        ],
      });

      // 监听未读消息
      qiyuSdkInterface._$onunread((res) => {
        const { total, message } = res;
        const currentPageInfo = getCurrentPageInfo();
        if (currentPageInfo && `/${currentPageInfo.route}` === Message.path) {
          currentPageInfo.setData({
            'messageInfo.stylistMsgInit': message.content || null,
            'messageInfo.stylistMsgTime': dayjs(message.time || '').format('HH:mm'),
            'messageInfo.stylistMsgUnreadCount': total,
          });
        }
        if (message.content) {
          globalData.qiyuSdkInfo.unreadMessageInfo = message;
        }
        globalData.qiyuSdkInfo.unreadMessageTotal = total;
        log && console.log('========================👇 当前未读消息总数 👇========================\n\n', total, '\n\n');
        log && console.log('========================👇 新增未读消息 👇========================\n\n', message, '\n\n');
      });

      // 监听URL链接点击响应, 因小程序插件回调中，主程序无法直接调用自身的navigateTo函数进行跳转，所以额外提供参数供使用方进行页面跳转, cb回调中只允许使用data里的变量，不允许使用主程序变量
      qiyuSdkInterface._$onClickAction((data, navigateTo) => {
        const { url } = data;
        // 注意此处必须使用navigateTo
        navigateTo({
          url,
        });
        log && console.log('========================👇 点击事件参数 👇========================\n\n', data, '\n\n');
      });
      
            // 文件消息处理 默认情况下sdk自行处理包括图片、音频、视频类型的文件打开操作，但是由于小程序对于插件api调用的限制，sdk无法直接打开文档及其他类型的问题，此时需要用户自行监听事件进行处理
      qiyuInterface._$onFileOpenAction((fileObj) => {
        console.log(fileObj);
        const name = fileObj.name || '';
        const nameArr = name.split('.');
        const ext = (nameArr[nameArr.length - 1] || '').toLocaleLowerCase();
        // 针对文档类型，直接调用微信api进行打开
        if (['doc', 'docx', 'xls', 'xlsx', 'ppt', 'pptx', 'pdf'].includes(ext)) {
          wx.openDocument({
            filePath: fileObj.tempFilePath,
            fileType: ext,
            success: (value) => {},
          });
        } else {
          wx.showToast({
            title: '暂不支持该文件类型预览',
          });
        }
      });
      globalData.qiyuSdkInfo.qiyuSdkInterface = qiyuSdkInterface;
    }
    reslove();
  });
};
```
### 缺陷

1. 小程序接入七鱼sdk是通过插件的形式，但是七鱼插件内部的文件相关逻辑处理插件名称写死了为myPlugin, 因此当我将引入的七鱼插件使用自定插件名称的时候，打开文件类型的数据会直接跳转到一个未定义的页面报错。【1.4.7版本以下建议不要修改七鱼插件的引入名称myPlugin】

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ecb8913dce7843a5986f8175fa189d18~tplv-k3u1fbpfcp-watermark.image?)

2. 全面屏适配，当小程序使用自定义导航栏"navigationStyle": "custom"需要在七鱼插件中开启全面屏适配，但七鱼内部的页面并没有支持全面屏适配，样式会从顶部开始展示。


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b364e9c7daef4d2baaf3a903053fd6eb~tplv-k3u1fbpfcp-watermark.image?)

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dfd51584227542fa8f90853262ef4ad8~tplv-k3u1fbpfcp-watermark.image?)

3. 视频问题，当客服通过七鱼后台给访客发送视频或访客给用户发送视频时候，点击视频自动进入全屏，切没有退出全屏的功能，只能通过左滑返回上一个页面，还不是返回聊天页面。
4. 聊天页面无法自定义样式。
5. 发送产品卡片不支持自定义样式
6. 没有接口支持获取历史聊天记录

### 结束

以上就是我在小程序中接入七鱼sdk总结的经验，希望你有所帮助，有问题可在评论区回复，我会在第一时间与你沟通。