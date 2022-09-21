> 由于英文文档对于某些小伙伴阅读起来比较有困难，因此我自行总结翻译整理中文文档，希望能对大家有所帮助。当然大家有能力的还是希望直接去看英文官网，毕竟中文文档是按照我的理解来翻译书写的。

### 安装

> wechaty 0.x 推荐 v14.18.1 本人版本。

> wechaty 1.x 需要 Node.js v16 或更高版本 (推荐 v16.13.0 本人版本)。你可以使用 n，nvm 或 nvm-windows 在同一台电脑中管理多个 Node 版本。

可以使用下列任一命令安装这个包的默认版本

```
npm install wechaty -S
# OR
yarn add wechaty
```

当然针对0.x版本, 我推荐安装以下版本，1.x版本默认安装即可。

#### 0.x

```
npm install wechaty@0.69.58 -s
# OR
yarn add wechaty@0.69.58
```

**切记1.x版本将Node环境切换至16以上版本。**

### 导入API

> 使用import语法，在package.josn中加入"type": "module"

> bot实例话对象可以存储在全局对象中，方便其他事件函数中使用。

#### 0.x

```
import { Wechaty } from 'wechaty'

const bot = new Wechaty(WechatyOptions)
```

#### 1.x

```
import { WechatyBuilder } from 'wechaty'

const bot = WechatyBuilder.build(WechatyOptions)
```

### wechaty.on(event, listener)

> wechaty 在运行中提供了很多事件函数，这些事件函数能够帮助你从从启动到停止这期间想要做的任何逻辑处理。

#### bot.on('scan', (qrcode, status) => {})

> bot启动完成后，监听到scan事件函数的触发，通过回调函数我们可以将回调参数进行处理来进行扫码登陆操作。

0.  安装qrcode-terminal

> qrcode-terminal这个包用来将二维码展示在控制台上

```
npm install qrcode-terminal -S
# OR
yarn add qrcode-terminal
```

2.  控制台打印登陆二维码

```
import QrcodeTerminal from 'qrcode-terminal'
import { qrcodeValueToImageUrl, ScanStatus } from 'wechaty'

bot
  .on('scan', (qrcode, status) => {
          if (status === ScanStatus.Waiting) {
                console.log(`========================👉二维码状态：${status}👈========================\n\n`)
                console.log(`========================👉二维码地址：${qrcodeValueToImageUrl(qrcode)}👈========================\n\n`)

                QrcodeTerminal.generate(qrcode, {
                    small: true
                })
      // do something
          }
  })
  .start()
```

**ScanStatus**

```
{
  Unknown   = 0,
  Cancel    = 1,
  Waiting   = 2,
  Scanned   = 3,
  Confirmed = 4,
  Timeout   = 5,
}
```

> 可通过源码了解更多。

#### bot.on('login', (user) => {})

> bot扫码登陆后，监听到login事件函数的触发，通过回调函数我们可以拿到当前登陆的机器人信息。

```
bot
  .on('login', (user) => {
    // do something
    // 通常我会在这里存储当前登陆的机器人信息，然后发送一个webhook消息通知到我当前机器人登陆成功了，等等操作
  })
  .start()
```

**user**

```
{
    "_events":{

    },
    "_eventsCount":0,
    "id":"",
    "_payload":{
        "address":"",
        "alias":"",
        "avatar":"",
        "city":"",
        "corporation":"",
        "coworker":true,
        "description":"",
        "friend":true,
        "gender":1,
        "id":"",
        "name":"",
        "phone":[

        ],
        "province":"",
        "signature":"",
        "star":false,
        "title":"",
        "type": Number,
        "weixin":""
    }
}
```

#### bot.on('ready', () => {})

> 当监听到ready事件函数的触发，意味着所有数据加载完成，意味着它完成了同步联系人和房间。

> 你可以在这个事件函数的回调中去获取群列表和联系人列表

```
bot
	.on('ready', () => {
		// do something
	})
	.start()
```

#### bot.on('heartbeat', (data) => {})

> 心跳连接，每隔一段时间会主动出发一次当前事件函数。

```
bot
	.on('heartbeat', (data) => {
		// do something
	})
	.start()
```

#### bot.on('message', () => {})

> 当前机器人收到消息的时候，会触发当前事件函数，在回调函数中通过回调参数来处理对应的一些业务逻辑处理。当前消息包括私聊消息和群聊消息。

```
bot
	.on('message', (message) => {
		// do something
	})
	.start()
```

**message**

```
WechatifiedMessageImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: '',
  _payload: {
    filename: '',
    fromId: '',
    id: '',
    mentionIdList: [],
    roomId: '',
    text: '',
    timestamp:,
    toId: '',
    type: Number
  },
  [Symbol(kCapture)]: false
}
```

> 我们可以通过消息自身的某些方法用来判断消息来自于哪里，处理消息内容等，具体看Message说明。

#### bot.on('friendship', (friendship) => {})

> 当有人给机器人发送好友申请时会出发当前事件函数，在回调函数中通过回调参数来处理业务逻辑。

> 通常可以用来处理自动好友通过，关键字自动通过，好友申请通过后通过bot实例邀请当前好友入群等等。

```
bot
	.on('friendship', (friendship) => {
		// do something
	})
	.start()
```

**friendship**

```
WechatifiedFriendshipImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: '',
  _payload: {
    contactId: '',
    hello: '',
    id: '',
    scene: Number,
    stranger: '',
    ticket: '',
    type: Number
  },
  [Symbol(kCapture)]: false
}
```

> 关于friendship介绍请看后续Friendship

#### bot.on('room-invite', (roomInvitation) => {})

> 当有人邀请机器人入群时会触发当前事件函数，返回roomInvitation参数用来处理邀请入群的后续业务逻辑。

> 关于更多roomInvitation的信息，请查看RoomInvitation说明。

```
bot
	.on('room-invite', async roomInvitation => {
  		const inviter = await roomInvitation.inviter()
  		const name = inviter.name()
  		console.log(`received room invitation event from ${name}`)
	}
	.start()
```

#### bot.on('room-join', (room, inviteeList, inviter) => {})

> 当有人进入机器人所在的群或者机器人主动进入某个微信群，那样会触发这个事件函数。

```
bot
	.on('room-join', async (room, inviteeList, inviter) => {}
	.start()
```

0.  room房间信息
0.  inviteeList邀请的成员列表信息
0.  inviter邀请者信息

#### bot.on('room-topic', (room, topic, oldTopic, changer) => {})

> 当有人修改群名称的时候会触发这个事件函数。

```
bot
	.on('room-topic', async (room, topic, oldTopic, changer) => {}
	.start()
```

0.  room 群聊房间信息
0.  topic 群聊名称
0.  oldTopic 旧的群聊名称
0.  changer 更改群聊名称的操作者信息

#### bot.on('room-leave', (room, leaver) => {})

> 当机器人把群里某个用户移出群聊的时候会触发这个事件，用户主动退群是无法检测到的。

```
bot
	.on('room-leave', async (room, leaver) => {}
	.start()
```

#### bot.on('logout', (user) => {})

> 当机器人退出登陆的时候，会触发这个事件函数，回调参数中包含当前机器人的信息。

```
bot
	.on('logout', (user) => {}
	.start()
```

#### bot.on('error', (error) => {})

> 当机器人出错时，会触发这个事件函数，回调参数中包含错误信息。

```
bot
	.on('error', (error) => {}
	.start()
```

### wechaty.start() ⇒ Promise <void>

> 提示：所有bot操作都需要在start ( )完成后触发

当你启动bot时，bot将开始登录，需要你微信扫描二维码登录

```
bot.start()
```

### wechaty.stop() ⇒ Promise <void>

> 停止机器人

```
// 根据某些条件处理，主动停止机器人。
await bot.stop()
// 机器人停止后做某些事情。
```

### wechaty.logout( ) ⇒ Promise <void>

> 注销机器人

```
// 根据某些条件处理，主动注销机器人。
await bot.logout()
// 机器人注销后做某些事情。
```

### wechaty.logonoff ( ) ⇒ boolean

> 获取登录/注销状态

```
// 根据当前机器人登录/注销的状态做某些事情
if (bot.logonoff()) {
  console.log('Bot logined')
} else {
  console.log('Bot not logined')
}
```

### wechaty.userSelf ( ) ⇒ ContactSelf

> 获取当前用户

```
const contact = bot.userSelf()
console.log(`Bot is ${contact.name()}`)
```

### wechaty.say ( textOrContactOrFileOrUrl ) ⇒ Promise

> 向 userSelf 发送消息，换句话说，bot 向自身发送消息。

> 这个功能依赖于你使用的Puppet的实现，差异见[puppet功能差异](https://wechaty.js.org/docs/puppet-services/compatibility/)

### WechatyOptions

> 创建机器人实例的option参数

#### name

> wechaty名字，当设置这个属性的时候，如果你使用的是web协议，会生成一个wechatyName.memory-card.json，这个文件会存储机器人的登录信息，在文件有效期内，机器人可以自动登录，不需要在扫描二维码登录。

#### pupet

> puppet服务商的名字，例如：wechaty-puppet-service，其他见[Wechaty Puppet Providers](https://wechaty.js.org/docs/puppet-providers/)

#### puppetOptions

> Puppet TOKEN, 如果你使用的puppet服务商是需要token的那么你需要像你使用的puppet服务商购买或者申请token来使用

```
{
	name: "zhihuifanqiechaodan",
	puppet: "wechaty-puppet-service",
	puppetOptions: {
		token: "对应poput服务商的token"
	}
}
```

#### ioToken

> 嗯～这个我也不太懂做什么的。有知道的朋友可以评论告诉我。

\