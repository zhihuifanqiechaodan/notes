## Redux介绍
Redux是一个用来管理管理数据状态和UI状态的JavaScript应用工具。随着JavaScript单页应用（SPA）开发日趋复杂，JavaScript需要管理比任何时候都要多的state（状态），Redux就是降低管理难度的。（Redux支持React，Angular、jQuery甚至纯JavaScript）

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/13/16d292fc153325b8~tplv-t2oaga2asx-image.image)
### 创建store仓库
在使用Redux之前，需要先用npm install来进行安装

```
npm install redux --save 
```
安装好redux之后，在src目录下创建一个store文件夹,然后在文件夹下创建一个index.js文件。

index.js就是整个项目的store文件，打开文件，编写下面的代码。

```
import { createStore } from 'redux'  // 引入createStore方法
const store = createStore()          // 创建数据存储仓库
export default store                 //暴露出去
```
这样虽然已经建立好了仓库，但是这个仓库很混乱，这时候就需要一个有管理能力的模块出现，这就是Reducers。这两个一定要一起创建出来，这样仓库才不会出现互怼现象。在store文件夹下，新建一个文件reducer.js,然后写入下面的代码。

```
const defaultState = {}  //默认数据
export default (state = defaultState,action)=>{  //就是一个方法函数
    return state
}
```
**state: 是整个项目中需要管理的数据信息,这里没有什么数据，所以用空对象来表示。**

这样reducer就建立好了，把reducer引入到store中,再创建store时，以参数的形式传递给store。

```
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    // 引入Reducers模块
const store = createStore(reducer) // 创建数据存储仓库
export default store   //暴露出去

```
### 组件中使用redux中的数据
* 初始化reducer.js中defaultState的数据

仓库store和reducer都创建好了，可以初始化一下reducer.js中的的数据了，在reducer.js文件的defaultState对象中，加入两个属性:myName和list。代码如下

```
const defaultState = {
    myName : '只会番茄炒蛋',
    list:['番茄炒蛋', '蛋炒米饭', '西红柿鸡蛋汤']
}
export default (state = defaultState,action)=>{
    return state
}
```
**这就相当于你给Store里增加了两个新的数据。**
* 组件获得store中的数据
有了store仓库，也有了数据，在组件中引入store即可

```
// 标准引入
import store from './store/index'
// 简写引入
import store from './store'
```
引入store后可以试着在构造方法里打印到控制台一下，看看是否真正获得了数据，如果一切正常，是完全可以获取数据的。

```
constructor(props){
    super(props)
    // 获取store中state数据要通过store.getState()方法
    console.log(store.getState()) 
}
```
这时候数据还不能在组件中直接使用,需要在构造器中将其复制给组件的state

```
constructor(props){
    super(props)
    // 获取store中state数据要通过store.getState()方法
    this.state = store.getState()
    console.log(this.state)
```
### Redux Dev Tools的安装
Redux Dev Tools和Vue DevTools的功能相似,都是方便我们查看store中数据

```
// 打开谷歌浏览器商店搜索Redux DevTools安装即可,当然需要你先科学上网
```
#### 配置Redux Dev Tools

```
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'
// 创建数据存储仓库
const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
)
export default store   //暴露出去
```
配置完以上内容即可在浏览器打开调试工具查看store中的数据了

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/13/16d296f2d1217d08~tplv-t2oaga2asx-image.image)
### 组件中去更改redux中的数据
#### 创建Action
想改变Redux里边State的值就要创建Action了。Action就是一个对象，这个对象一般有两个属性，第一个是对Action的描述，第二个是要改变的值。

```
const action ={
    type:'change_value', // action的描述
    value: '传递给state的值'   // 想要改变/保存的值
}
```
action就创建好了，但是要通过dispatch()方法传递给store。我们在action下面再加入一句代码。

```
const action ={
    type:'change_value', // action的描述
    value: '传递给state的值'   // 想要改变/保存的值
}
store.dispatch(action) // 通过store.dispatch推送
```
这是Action就已经完全创建完成了，也和store有了联系。
#### store的自动推送策略
store只是一个仓库，它并没有管理能力，它会把接收到的action自动转发给Reducer。直接在Reducer中打印出结果看一下。打开store文件夹下面的reducer.js文件，修改代码。

```
export default (state = defaultState,action) => {
    console.log(state, action)
    return state
}
```
* state: 指的是原始仓库里的状态。
* action: 指的是action新传递的状态。
通过打印可以知道，Reducer已经拿到了原来的数据和新传递过来的数据，现在要作的就是改变store里的值。先判断type是不是正确的，如果正确，我们需要从新声明一个变量newState去深拷贝state。（记住：Reducer里只能接收state，不能改变state。）,所以我们声明了一个新变量，然后再次用return返回回去。

```
export default (state = defaultState,action) => {
    if(action.type === 'change_value'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.myName = action.value
        return newState
    }
    return state
}
```
#### 让组件发生更新
现在store里的数据已经更新了，但是组件还没有进行更新，还需要在组件中的constructor中增加订阅redux状态函数

```
constructor(props){
    super(props)
    this.state=store.getState();
    this.storeChange = this.storeChange.bind(this)  // 改变this指向
    store.subscribe(this.storeChange) // 订阅Redux的状态
}
 storeChange(){
     this.setState(store.getState()) // 重新获取最新的state
 }
```
以上需要增加订阅redux状态函数的业务场景是在当前组件中既使用了state,又因为自身的改变去改变state, 例如:

```
render() { 
        return ( 
            <div style={{margin:'10px'}}>
               <input 
                    value={this.state.myName}
                    @onClick={this.changeMyName.bind(this)}> 
               </input>
            </div>
         );
}
changeMyName (e) {
    const action ={
        type:'change_value', // action的描述
        value: e.target.value   // 想要改变/保存的值
    }
    store.dispatch(action) // 通过store.dispatch推送
}
```
### Redux的小技巧
写Redux Action的时候，写了很多Action的派发，产生了很多Action Types，如果需要Action的地方我们就自己命名一个Type,会出现两个基本问题：
* 这些Types如果不统一管理，不利于大型项目的服用，设置会长生冗余代码。
* 因为Action里的Type，一定要和Reducer里的type一一对应在，所以这部分代码或字母写错后，浏览器里并没有明确的报错，这给调试带来了极大的困难。
#### 模块化
把Action Type单独拆分出一个文件。在src/store文件夹下面，新建立一个actionTypes.js文件，然后把Type集中放到文件中进行管理。

```
export const  CHANGE_VALUE = 'change_value'
export const  ADD_ITEM = 'addItem'
export const  DELETE_ITEM = 'deleteItem'
```
#### 组件中引入actionTypes.js并使用
在需要使用的组件中按需引入
```
import { CHANGE_VALUE } from './store/actionTypes'
```

```
changeInputValue(e){
    // 以前的写法
    const action ={
        type:'change_value',    // action的描述
        value: e.target.value   // 想要改变/保存的值
    }
    store.dispatch(action)
    
    // 现在的写法
    const action ={
        type:CHANGE_VALUE,    // action的描述
        value:e.target.value  // 想要改变/保存的值
    }
    store.dispatch(action)
}
```
#### 在reducer.js中引入并使用
也是先引入actionType.js文件，然后把对应的字符串换成常量，整个代码如下：

```
import { CHANGE_VALUE } from './actionTypes'

const defaultState = {
    myName : '只会番茄炒蛋',
    list:['番茄炒蛋', '蛋炒米饭', '西红柿鸡蛋汤']
}

export default (state = defaultState,action)=>{
    if(action.type === CHANGE_VALUE){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.myName = action.value
        return newState
    }
    // 这里可以写多个判断去改变赋值
    return state
}
```
#### 组件中的action也抽离
* 编写actionCreators.js文件

在/src/store文件夹下面，建立一个心的文件actionCreators.js，先在文件中引入编写的actionTypes.js文件。

```
import {CHANGE_VALUE}  from './actionTypes'

// 这个导出的匿名函数最后执行返回的是一个对象
export const changeInputAction = (value)=>({
    type:CHANGE_VALUE,
    value
})
```
* 修改组件中使用的方式

```
import {changeInputAction} from './store/actionCreatores' // 引入抽离出去的actionCreators.js

changeInputValue(e){
    // 以前的写法
    const action ={
        type:'change_value',    // action的描述
        value: e.target.value   // 想要改变/保存的值
    }
    store.dispatch(action)
    
    // 现在的写法
    const action ={
        type:CHANGE_VALUE,    // action的描述
        value:e.target.value  // 想要改变/保存的值
    }
    
    // 都抽离组件的写法
    const action = changeInputAction(e.target.value)
    store.dispatch(action)
}
```
这样就完成了分离，那你可能要问，多了一个文件，代码看起来更加晦涩难懂了，这有什么意义那？实际上这样就实现了复用，比如说增删改查不可能用一次，可能很多地方都会用到，这样我们直接引入文件使用就可以了，可以避免冗余代码。还有就是这样如果写错了常量名称，程序会直接在浏览器和控制台报错，可以加快开发效率，减少找错时间。
### Redux中非常重要的几个点
* store必须是唯一的，多个store是坚决不允许，只能有一个store空间
* 只有store能改变自己的内容，Reducer不能改变
* Reducer必须是纯函数

```
Reudcer只是返回了更改的数据，但是并没有更改store中的数据，store拿到了Reducer的数据，自己对自己进行了更新。
```

```
Reducer必须是纯函数,如果函数的调用参数相同，则永远返回相同的结果。
它不依赖于程序执行期间函数外部任何状态或数据的变化，必须只依赖于其输入参数。
```
### Axios异步获取数据并和Redux结合

```
// actionTypes.js文件中新增一个常量字段

export const GAT_DATA = 'getData'
```

```
// actionCreators.js新增一个保存文件的方法
import { GAT_DATA } from './actionTypes' // 引入常量

export const getDataAction = (data) => ({
    tyle: 'GAT_DATA',
    value: data
})
```

```
// 组件中使用
import { getDataAction } from './actionCreators' // 引入模块的actionCreators
import axios from 'axios'

componentDidMount(){ // 生命周期中调用
    axios.get('地址').then(res => {
        if (res.data) {
            const data = res.data
            const action = getDataAction(data)
            store.dispatch(action)
        }
    }).catch(err => {
        alert(err)
    })
}
```
现在数据已经通过dispatch传递给store了，接下来就需要reducer处理业务逻辑了。打开reducer.js代码如下

```
import { GAT_DATA, CHANGE_VALUE } from './actionTypes'

const defaultState = {
    myName : '只会番茄炒蛋',
    list:['番茄炒蛋', '蛋炒米饭', '西红柿鸡蛋汤']
}

export default (state = defaultState,action)=>{
    if(action.type === CHANGE_VALUE){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.myName = action.value
        return newState
    }
    if(action.type === GAT_DATA){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.list = action.value // 保存通过axios获取的数据列表
        return newState
    }
    // 这里可以写多个判断去改变赋值
    return state
}
```
### Redux-thunk中间件的安装和配置
Redux-thunk这个Redux最常用的插件。什么时候会用到这个插件那？比如在Dispatch一个Action之后，到达reducer之前，进行一些额外的操作，就需要用到middleware（中间件）。在实际工作中你可以使用中间件来进行日志记录、创建崩溃报告，调用异步接口或者路由。 这个中间件可以使Redux-thunk进行增强
#### 安装Redux-thunk组件

```
npm install --save redux-thunk
```
配置Redux-thunk组件

```
// 引入applyMiddleware,如果你要使用中间件，就必须在redux中引入applyMiddleware
import { createStore , applyMiddleware } from 'redux' 
// 引入redux-thunk库
import thunk from 'redux-thunk'
```
如果你按照官方文档来写，你直接把thunk放到createStore里的第二个参数就可以了，但以前我们配置了Redux Dev Tools，已经占用了第二个参数。

```
const store = createStore(
    reducer,
    applyMiddleware(thunk)
) // 创建数据存储仓库
```
这样写是完全没有问题的，但是Redux Dev Tools插件就不能使用了，如果想两个同时使用，需要使用增强函数。使用增加函数前需要先引入compose。

```
import { createStore , applyMiddleware ,compose } from 'redux' 
```
然后利用compose创造一个增强函数，就相当于建立了一个链式函数，代码如下:

```
const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose
```
有了增强函数后，就可以把thunk加入进来了，这样两个函数就都会执行了。

```
const enhancer = composeEnhancers(applyMiddleware(thunk))
```
这时候直接在createStore函数中的第二个参数，使用这个enhancer变量就可以了，相当于两个函数都执行了。

```
const store = createStore( reducer, enhancer) // 创建数据存储仓库
```
详细代码如下:

```
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
import thunk from 'redux-thunk'

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose

const enhancer = composeEnhancers(applyMiddleware(thunk))

const store = createStore( reducer, enhancer) // 创建数据存储仓库
export default store   //暴露出去
```
### Redux-thunk的使用方法
把向后台请求数据的程序放到中间件中，这样就形成了一套完整的Redux流程，所有逻辑都是在Redux的内部完成的，这样看起来更完美，而且这样作自动化测试也会变动简单很多
#### 在actionCreators.js里编写业务逻辑
以前actionCreators.js都是定义好的action，根本没办法写业务逻辑，有了Redux-thunk之后，可以把TodoList.js中的componentDidMount业务逻辑放到这里来编写。也就是把向后台请求数据的代码放到actionCreators.js文件里。那我们需要引入axios,并写一个新的函数方法。（以前的action是对象，现在的action可以是函数了，这就是redux-thunk带来的好处）

```
import axios from 'axios'

export const getTodoList = () =>{
    return ()=>{
        axios.get('地址').then((res)=>{
            const action = getDataAction(data)
            dispatch(action)
        })
    }
}
```
组件中使用

```
import { getTodoList } from './store/actionCreatores'
componentDidMount(){
    const action = getTodoList()
    store.dispatch(action)
}
```
这时候我们通过Redux Dev Tools就可以看到异步拿到的数据已经存储到state中了

也许你会觉的这么写程序很绕，刚开始写Redux的时候确实感觉很绕，但是随着项目的越来越大，你会发现把共享state的业务逻辑放到你Redux提示当中是非常正确的，它会使你的程序更加有条理
****
以上就是我在学习redux中的理解和笔记了, 学习视频是通过技术胖的B站视频和掘金文章,链接如下

链接：https://juejin.im/post/6844903901603299335