> 本篇文章将详细介绍消息类的方法和用途，在wechaty中所有微信消息都被封装在此。

### Message

> 重要说明，这里Message类实际上就是在Wechaty文章中bot.on('message', message => {})事件函数中返回的回调参数，这个类内部提供了以下方法。

#### from() => talker()

> 调用此方法可以获取当前消息的发送者详情，目前from() 已变更为talker()。

```
bot.on('message', message => {
  const contact = message.talker()
})
```

**contact**

```
WechatifiedContactImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: '7881302689943729',
  _payload: {
    address: '',
    alias: 'admin',
    avatar: 'http://wx.qlogo.cn/mmhead/OVExThfzrxicfAFAegxmK1dYrbeG7mFRUw0cxsGZPGiaI/0',
    city: '',
    corporation: '微信',
    coworker: false,
    description: '',
    friend: true,
    gender: 1,
    id: '7881302689943729',
    name: '只会番茄炒蛋',
    phone: [],
    province: '',
    signature: '',
    star: false,
    title: '',
    type: 1,
    weixin: ''
  },
  [Symbol(kCapture)]: false
}
```

#### to() => listener()

> 消息来源，如果返回null，那么说明消息来源于群聊，调用room()方法来获取Room类，目前to() 已变更为 listener()

```
bot.on('message', message => {
  const listener = message.listener()
})
```

**listener**

```
WechatifiedContactSelfImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: '1688851979087841',
  _payload: undefined,
  [Symbol(kCapture)]: false
}
```

#### room()

> 房间类，调用次方法返回房间实例或者null, 可用此方法判断消息来源于私聊还是群聊。

```
bot.on('message', message => {
  const room = message.room()
})
```

**room**

```
WechatifiedRoomImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: 'R:10696050959023730',
  _payload: {
    adminIdList: [],
    avatar: '',
    id: 'R:10696050959023730',
    memberIdList: [ '1688851979087841', '7881302689943729', '7881303132920177' ],
    ownerId: '1688851979087841',
    topic: '番茄机器人测试群'
  },
  [Symbol(kCapture)]: false
}
```

#### text()

> 可用此方法获取文本消息内容, 通常对于消息逻辑处理，可以根据消息类型判断，从而出做对应的业务逻辑和数据处理。

```
bot.on('message', message => {
  const messageType = message.payload.type
  if (messageType === 7) {
    const text = message.text() 
  } cvbnm

})
```

#### say(text Or Contact Or File)

> 调用此方法可立即回复内容给消息发送者，发送的内容可以是消息类型中的那些类型，前提是你使用的puppet服务商支持发送当前消息类型的内容。

```
bot.on('message', message => {
  message.say('要发送的消息内容，你可以发送多种类型的消息')
})
```

> 具体如何发送不同类型的消息，我会在后面一篇入门文章中完整写出来。

#### type()

> 此方法可以获取当前消息的类型。

```
bot.on('message', message => {
	const messageType = message.type()
	console.log(messageType)
})
```

**messageType**

```
/** 
 *  Unknown: 0,
    Attachment: 1,
    Audio: 2,
    Contact: 3,
    ChatHistory: 4,
    Emoticon: 5,
    Image: 6,
    Text: 7,
    Location: 8,
    MiniProgram: 9,
    GroupNote: 10,
    Transfer: 11,
    RedEnvelope: 12,
    Recalled: 13,
    Url: 14,
    Video: 15
*/
```

#### mention()

> 此方法可以获取当前群内@人员的列表。

```
bot.on('message', async message => {
	const mentionList = await message.mention()
	console.log(mentionList)
})
```

**mentionList**

```
[
  WechatifiedContactImpl {
    _events: [Object: null prototype] {},
    _eventsCount: 0,
    _maxListeners: undefined,
    id: '1688851979087841',
    _payload: {
      address: '',
      alias: '',
      avatar: 'https://wework.qpic.cn/wwhead/duc2TvpEgSQO4BpE0WZSZ09FGc9Onicj8yIF3iaVQNY6JicpK83UqlINPhgotf5FFM8S6eEYldGzbs/0',
      city: '',
      corporation: '',
      coworker: true,
      description: '',
      friend: true,
      gender: 1,
      id: '1688851979087841',
      name: '只会番茄炒蛋',
      phone: [],
      province: '',
      signature: '',
      star: false,
      title: '',
      type: 3,
      weixin: 'GaoYu'
    },
    [Symbol(kCapture)]: false
  },
  WechatifiedContactImpl {
    _events: [Object: null prototype] {},
    _eventsCount: 0,
    _maxListeners: undefined,
    id: '7881301374163481',
    _payload: {
      address: '',
      alias: '',
      avatar: 'http://wx.qlogo.cn/mmhead/PiajxSqBRaEK6YtRv3iaGbMibPvCmexhP2nGktBkeWA9oXhjB2BZBXOWg/0',
      city: '',
      corporation: '微信',
      coworker: false,
      description: '',
      friend: false,
      gender: 0,
      id: '7881301374163481',
      name: '番茄助手',
      phone: [],
      province: '',
      signature: '',
      star: false,
      title: '',
      type: 1,
      weixin: ''
    },
    [Symbol(kCapture)]: false
  }
]
```

#### message.mentionSelf()

> 此方法可检查消息是否@自己，返回Boolean类型。

```
bot.on('message', async message => {
	const isMentionSelf = await message.mentionSelf()
	console.log(isMentionSelf)
})
```

#### message.forward()

> 此方法用于转发收到的消息，可讲消息转发至某个群聊房间或者个人。

```
bot.on('message', async message => {
	const room = await bot.Room.find({topic: 'wechaty'})
	if (room) {
		await message.forward(room)	
	}
})
```

#### date()

> 该方法返回消息发送日期。

```
bot.on('message', async message => {
	const date = message.date()
	console.log(date)
})
```

**date**

```
2022-01-28T06:09:22.000Z
```

#### age()

> 此方法返回消息接收的时常，即当有人发送消息到机器人接收到消息之前的间隔，单位（seconds）

#### toFileBox()

> 此方法将从消息中提取media文件，并将其放入FileBox中

```
bot.on('message', async message => {
	const image = await messafe.toFileBox()
	// 转流媒体操作
	const imageStream = await image.toStream();
	// 将媒体流上传到存储空间。。。
})
```

#### toContact()

> 此方法用于获取个人名片信息，当获取到消息类型为3是，可用此方法获取个人名片信息。

```
bot.on('message', async message => {
	if (message.type() === 3) {
		const contact = await message.toContact()
	}
})
```

**contact**

```
WechatifiedContactImpl {
  _events: [Object: null prototype] {},
  _eventsCount: 0,
  _maxListeners: undefined,
  id: '7881301374163481',
  _payload: {
    address: '',
    alias: '',
    avatar: 'http://wx.qlogo.cn/mmhead/PiajxSqBRaEK6YtRv3iaGbMibPvCmexhP2nGktBkeWA9oXhjB2BZBXOWg/0',
    city: '',
    corporation: '微信',
    coworker: false,
    description: '',
    friend: false,
    gender: 0,
    id: '7881301374163481',
    name: '番茄助手',
    phone: [],
    province: '',
    signature: '',
    star: false,
    title: '',
    type: 1,
    weixin: ''
  },
  [Symbol(kCapture)]: false
}
```

#### toUrlLink()

> 此方法用于获取图文链接卡片

-   0.x

```
bot.on('message', async message => {
	const urlLink = await message.toUrlLink()
})
```

-   1.x

> 1.x版本目前调用此方法会报错。我会后续跟踪此问题更新内容。

**urlLink**

```
UrlLink {
  payload: {
    description: '现在前端面试Vue中都会问到响应式原理以及如何实现的，如果你还只是简单回答通过Object.defineProperty()来劫持属性可能已经不够了。 本篇文章通过学习文档及视频教程实现手写一个简易的Vue源码实现数据双向绑定，解析指令等。 目前几种主流的mvc(vm)框架都实…',
    thumbnailUrl: '',
    title: 'Vue高级指南-01 Vue源码解析之手写Vue源码',
    url: 'https://juejin.cn/post/6844904047921594382'
  }
}
```

### puppet功能实现

> 以上某些功能实现需要对应的puppet是否支持，具体请查看[puppet](https://wechaty.js.org/docs/puppet-services/compatibility/)