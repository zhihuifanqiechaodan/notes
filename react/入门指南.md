## React简介
首先不能否认React.js是全球最火的前端框架(Facebook推出的前端框架)，国内的一二线互联网公司大部分都在使用React进行开发，比如阿里、美团、百度、去哪儿、网易 、知乎这样的一线互联网公司都把React作为前端主要技术栈。

React的社区也是非常强大的,随着React的普及也衍生出了更多有用的框架，比如ReactNative和React VR。React从13年开始推广，现在已经推出16RC（React Fiber）这个版本，性能和易用度上，都有很大的提升。
### React优点总结
* **生态强大**：现在没有哪个框架比React的生态体系好的，几乎所有开发需求都有成熟的解决方案。
* **上手简单**: 你甚至可以在几个小时内就可以上手React技术，但是他的知识很广，你可能需要更多的时间来完全驾驭它。
* **社区强大**：你可以很容易的找到志同道合的人一起学习，因为使用它的人真的是太多了。
### 需要的前置知识
* **JavaScript基础**：如果能会ES6就更好了

* **npm基础**：你需要会使用npm的包管理
****
## React开发环境搭建
* 安装Node只需要进入Node网站，进行响应版本的下载，然后进行双击安装就可以了。
```
// 安装完成打开命令窗口输入
node -v   //出现版本号则安装成功
```
* 脚手架的安装

```
// 安装react的脚手架工具
//create-react-app是React官方出的脚手架工具
npm install -g create-react-app  
```
* 创建一个React项目

```
// 桌面创建一个react-demo的文件夹
// 进入当前文件夹,打开命令行
create-react-app demo   //用脚手架创建React项目
npm start   // 安装完成进入demo文件夹启动项目
```
****
## 项目根目录中的文件介绍
* **README.md** :这个文件主要作用就是对项目的说明，已经默认写好了一些东西，你可以简单看看。如果是工作中，你可以把文件中的内容删除，自己来写这个文件，编写这个文件可以使用Markdown的语法来编写。


* **package.json**: 这个文件是webpack配置和项目包管理文件，项目中依赖的第三方包（包的版本）和一些常用命令配置都在这个里边进行配置，当然脚手架已经为我们配置了一些了，目前位置，我们不需要改动。如果你对webpack了解，对这个一定也很熟悉。


* **package-lock.json**：这个文件用一句话来解释，就是锁定安装时的版本号，并且需要上传到git，以保证其他人再npm install 时大家的依赖能保证一致。


* **gitignore**: 这个是git的选择性上传的配置文件，比如一会要介绍的node_modules文件夹，就需要配置不上传。


* **node_modules**:这个文件夹就是我们项目的依赖包，到目前位置，脚手架已经都给我们下载好了，你不需要单独安装什么。


* **public**：公共文件，里边有公用模板和图标等一些东西。


* **src**： 主要代码编写文件，这个文件夹里的文件对我们来说最重要，都需要我们掌握。
### public文件夹介绍
这个文件都是一些项目使用的公共文件，也就是说都是共用的
* **favicon.ico**: 这个是网站或者说项目的图标，一般在浏览器标签页的左上角显示。

* **index.html**: 首页的模板文件

* **mainifest.json**：移动端配置文件
### src文件夹介绍
这个目录里边放的是我们开放的源代码，我们平时操作做最多的目录。

* **index.js**: 这个就是项目的入口文件


* **index.css**：这个是index.js里的CSS文件。


* **app.js**: 这个文件相当于一个方法模块，也是一个简单的模块化编程。


* **serviceWorker.js**:这个是用于写移动端开发的，PWA必须用到这个文件，有了这个文件，就相当于有了离线浏览的功能。
****
## 组件的介绍
### 入口文件的编写
写一个项目的时候一般要从入口文件进行编写的，在src目录下，index.js文件就是入口文件

```
import React from 'react' // 引入react
import ReactDOM from 'react-dom' // 引入react-dom
import App from './App' // 引入App模块
ReactDOM.render(<App />,document.getElementById('root')) // 将App模块渲染到了 root ID上面
```
### App组件的编写

```
import React from 'react'
// JSX语法
class App extends React.Component{
    render(){
        return (
            <div>
                我是APP组件
            </div>
        )
    }
}
export default App;
```
### React中JSX语法简介

```
JSX就是Javascript和XML结合的一种格式。React发明了JSX，
可以方便的利用HTML语法来创建虚拟DOM，当遇到<，JSX就当作HTML解析，
遇到{就当JavaScript解析.
```
### 组件和普通JSX语法区别
这个说起来也只有简单的一句话，就是你自定义的组件必须首写字母要进行大写，而JSX是小写字母开头的。
### JSX中使用三元运算符
在JSX中也是可以使用js语法的
```
import React from 'react'
// JSX语法
class App extends React.Component{
    render(){
        return (
            <div>
                <div>{false ? '不显示我' : '显示的我'}</div>
                我是APP组件
            </div>
        )
    }
}
export default App;
```
****
## 组件外层包裹原则
react和vue组件模板的原则一样,最外层必须有且只有一个元素,多了会报错

下面这个列子就是一个错误的,因为最外层有了两个元素
```
import React from 'react'
// JSX语法
class App extends React.Component{
    render(){
        return (
            <div></div>
            <div>
                <div>{false ? '不显示我' : '显示的我'}</div>
                我是APP组件
            </div>
        )
    }
}
export default App;
```
### Fragment标签
加上最外层的DIV，组件就是完全正常的，但是你的布局就偏不需要这个最外层的标签怎么办?比如我们在作Flex布局的时候,外层还真的不能有包裹元素。这种矛盾其实React16已经有所考虑了，为我们准备了<Fragment>标签。

```
import React from 'react'
// JSX语法
class App extends React.Component{
    render(){
        return (
            <React.Fragment>
                <div>{false ? '不显示我' : '显示的我'}</div>
            </React.Fragment>
        )
    }
}
export default App;
```
这时候再去浏览器的Elements中查看，就会发现已经没有外层的包裹了。
****
## 响应式设计和数据的绑定
React不建议你直接操作DOM元素,而是要通过数据进行驱动，改变界面中的效果。React会根据数据的变化，自动的帮助你完成界面的改变。所以在写React代码时，你无需关注DOM相关的操作，只需要关注数据的操作就可以了（这也是React如此受欢迎的主要原因，大大加快了我们的开发速度）。

```
import React from 'react'
// JSX语法
class App extends React.Component{
    constructor(props){
    super(props) //调用父类的构造函数，固定写法
        this.state={
            myName: '只会番茄炒蛋',
            list: ['番茄', '炒蛋']
        }
    }
    render(){
        return (
            <React.Fragment>
                <div>{this.state.myName}</div>
                <ul>
                    {
                        this.state.list.map((item,index) => {
                            return (
                                <li key={item + index}>{item}</li>
                            )
                        })
                    }
                </ul>
            </React.Fragment>
        )
    }
}
export default App;
```
### 绑定事件

```
import React from 'react'
// JSX语法
class App extends React.Component{
    constructor(props){
    super(props) //调用父类的构造函数，固定写法
        this.state={
            myName: '只会番茄炒蛋',
            list: ['番茄', '炒蛋']
        }
        this.changeBtn = this.changeBtn.bind(this)
    }
    render(){
        return (
            <React.Fragment>
                <div>{this.state.myName}</div>
                <button onClick={this.changeBtn}>点击事件</button>
            </React.Fragment>
        )
    }
    changeBtn () {
        // 这里要注意,如果没有绑定this这里的this是undefined
        // 通过setState方法去改变数据,保证页面会渲染改变后的内容
        this.setState({
            myName: '我的点击按钮后被改变的值'
        })
    }
}
export default App;
```
****
## JSX防踩坑的几个地方
### JSX代码注释
以下两种方式书写代码注释
```
 {/* 正确注释的写法 */}
 { // 正确注释的写法 }
```
### JSX中的class陷阱
它是防止和js中的class类名冲突, 所以JSX中都是用className
```
<input class="input"/>  // 错误的写法
<input className="input"/>  // 正确的写法
```
### JSX中的html解析问题
如果想在文本框里输入一个h1标签，并进行渲染。默认是不会生效的，只会把h1标签打印到页面上，这并不是我想要的。如果工作中有这种需求，可以使用dangerouslySetInnerHTML属性解决。具体代码如下：

```
<li dangerouslySetInnerHTML={{__html:item}}> {<h1>我会变成h1号字体</h1>}</li>
```
### JSX中<label>标签的坑
JSX中label的坑，也算是比较大的一个坑，label是html中的一个辅助标签，也是非常有用的一个标签。

```
<div>
    <label>加入服务：</label>
    <input className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
    <button onClick={this.addList.bind(this)}> 增加服务 </button>
</div>
```
这时候想点击“加入服务”直接可以激活文本框，方便输入。按照html的原思想，是直接加ID就可以了。代码如下：

```
<div>
    <label for="jspang">加入服务：</label>
    <input id="jspang" className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
    <button onClick={this.addList.bind(this)}> 增加服务 </button>
</div>
```
这时候你浏览效果虽然可以正常，但console里还是有红色警告提示的。大概意思是不能使用for.它容易和javascript里的for循环混淆，会提示你使用htmlfor。

```
<div>
    <label htmlFor="jspang">加入服务：</label>
    <input id="jspang" className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
    <button onClick={this.addList.bind(this)}> 增加服务 </button>
</div>
```
****
## 组件的拆分
### 父组件如何使用子组件
```
// 子组件
import React from 'react'
// JSX语法
class childItem extends React.Component{
    return (
        <React.Fragment>
            <div>我是子组件</div>
        </React.Fragment>
    )
}
export default childItem;
```

```
// 父组件
import React from 'react'
// 引入子组件
import childItem from './childItem'
// JSX语法
class parentItem extends React.Component{
    return (
        <React.Fragment>
            {/* 使用了子组件 */}
            <childItem/>
        </React.Fragment>
    )
}
export default parentItem;
```
### 父子组件的传值
父组件通过自定义属性将要传递的值传递给子组件
```
// 父组件
import React from 'react'
// 引入子组件
import childItem from './childItem'
// JSX语法
class parentItem extends React.Component{
    constractor (props) {
        super(props)
        this.state = {
            myName: '只会番茄炒蛋'
        }
    }
    return (
        <React.Fragment>
            {/* 使用了子组件 */}
            <childItem myName={this.state.myName}/>
        </React.Fragment>
    )
}
export default parentItem;
```
子组件通过this.props.XXX接受父组件传递归来的值

```
// 子组件
import React from 'react'
// JSX语法
class childItem extends React.Component{
    return (
        <React.Fragment>
            <div>{this.props.myName}</div>
        </React.Fragment>
    )
}
export default childItem;
```
**父组件向子组件传递内容，靠属性的形式传递。**
### 子组件向父组件传递数据
切记单项数据流!!!

React有明确规定，子组件时不能操作父组件里的数据的，所以需要借助一个父组件的方法，来修改父组件的内容。
```
// 子组件
import React from 'react'
// JSX语法
class childItem extends React.Component{
    constructor (props) {
        super(props)
        this.changeParent = this.changeParent.bind(this)
    }
    return (
        <React.Fragment>
            <div onClick={this.changeParent}>{this.props.myName}</div>
        </React.Fragment>
    )
    changeParent () {
        // 调用父组件传递过来的方法
        this.props.pranentChange()
    }
}
export default childItem;
```
```
// 父组件
import React from 'react'
// 引入子组件
import childItem from './childItem'
// JSX语法
class parentItem extends React.Component{
    constractor (props) {
        super(props)
        this.state = {
            myName: '只会番茄炒蛋'
        }
        this.pranentChange = this.pranentChange.bind(this)
    }
    return (
        <React.Fragment>
            {/* 使用了子组件 */}
            <childItem 
                pranentChange={this.pranentChange}
                myName={this.state.myName}
            />
        </React.Fragment>
    )
    pranentChange () {
        this.setState({
            myName: '番茄炒蛋少放糖'
        })
    }
}
export default parentItem;
```
****
## PropTypes校验传递值
在父组件向子组件传递数据时，使用了属性的方式，也就是props，但是没有任何的限制。这在工作中时完全不允许的，因为大型项目，如果你不校验，后期会变的异常混乱，业务逻辑也没办法保证。
### PropTypes的应用
#### 普通效验
```
// 子组件
import React from 'react'
// 引入效验
import PropTypes from 'prop-types'
// JSX语法
class childItem extends React.Component{
    constructor (props) {
        super(props)
        this.changeParent = this.changeParent.bind(this)
    }
    return (
        <React.Fragment>
            <div onClick={this.changeParent}>{this.props.myName}</div>
        </React.Fragment>
    )
    changeParent () {
        // 调用父组件传递过来的方法
        this.props.pranentChange()
    }
}
childItem.prorTypes = {
    myName: PropTypes.string, // 效验传入的内容必须是一个字符串
    pranentChange: PropTypes.func // 效验传入的内容必须是一个function
}
export default childItem;
```
注意: 这里添加效验以后,如果不按照效验规则传入内容会有warning警告
#### 必传值的效验---isRequired
例如这里加了一个newName的属性,并且放入JSX中,就算父组件不传递这个值也不会报错的
```
// 子组件
render() { 
    return ( 
        <div >{this.props.newName}</div>
    );
}
```
但是怎样避免必须传递newName这个属性值?如果不传递就报错,这就需要使用isRequired关键字了,它表示必须进行传递，如果不传递就报错。

```
childItem.prorTypes = {
    // 效验传入的内容必须是一个字符串并且必须传入值
    newName:PropTypes.string.isRequired, 
}
```
#### 使用默认值---defaultProps
有时候子组件是需要有一个默认的值,如果父组件不传就用默认的,如果传了就用父组件传递过来的值,defalutProps就可以实现默认值的功能，比如现在把newName的默认值设置成"只会番茄炒蛋" 。

```
childItem.defaultProps = {
    // 子组件使用父组件传递的值,但是他自己有个默认的值
    newName:'只会番茄炒蛋'
}
```
****
## ref的使用方法
在编写组件中的方法时，经常会遇到语义化很模糊的代码，这对于团队开发是一个很大的问题。因为review代码或者合作时都会影响开发效率。或者到这核心成员离开，项目倒闭的严重影响。所以我们必须重视react代码当中的语义化。ref是个不错的工具

```
import React from 'react'
// JSX语法
class parentItem extends React.Component{
    constractor (props) {
        super(props)
        this.state = {
            myName: '只会番茄炒蛋'
        }
        this.inputChange = this.inputChange.bind(this)
    }
    return (
        <React.Fragment>
            <input 
                value={this.state.myName}
                onChange={this.inputChange}
            ></input>
        </React.Fragment>
    )
    inputChange (e) {
        console.log(e.target.value)
    }
}
export default parentItem;
```
想要获取上面input输入的值,必须通过e.target.value,这样并不直观,也不好看,这中情况可以使用ref来进行解决

```
import React from 'react'
// JSX语法
class parentItem extends React.Component{
    constractor (props) {
        super(props)
        this.state = {
            myName: '只会番茄炒蛋'
        }
        this.inputChange = this.inputChange.bind(this)
    }
    return (
        <React.Fragment>
            <input 
                value={this.state.myName}
                onChange={this.inputChange}
                ref={input => {this.input = input}}
            ></input>
        </React.Fragment>
    )
    inputChange (e) {
        console.log(this.input.value)
    }
}
export default parentItem;
```
这就使代码变得语义化和优雅的多。但是是不建议用ref这样操作的，因为React的是数据驱动的，所以用ref会出现各种问题。
### ref使用中的坑
比如现在我们要用ref绑定获取子元素的数量，可以先用ref进行绑定。

```
<ul ref={(ul)=>{this.ul=ul}}>
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>  
```
这时候如果我们动态添加或者减少li的数量,并在方法中打印li的数量,发现返回的数量并不准确,这个坑是因为React中更新setState是一个异步函数所造成的。也就是这个setState在代码执行是有一个时间的，如果你真的想了解清楚，你需要对什么是虚拟DOM有一个了解。简单的说，就是因为是异步，还没等虚拟Dom渲染，我们的console.log就已经执行了。

那这个代码怎么编写才会完全正常那，其实setState方法提供了一个回调函数，也就是它的第二个函数。下面这样写就可以实现我们想要的方法了。

```
增加/删除(){
    this.setState({
        list:改变后的数据,
    },()=>{
        console.log(this.ul.querySelectorAll('li').length)
    })
}
```
****
## 生命周期

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/5/16d004e82c3c92bb~tplv-t2oaga2asx-image.image)
### 什么是生命周期函数

```
生命周期函数指在某一个时刻组件会自动调用执行的函数
```
### 挂载
当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：
* **constructor()**

```
如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。
在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，
应在其他语句之前前调用 super(props)。否则，this.props 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数仅用于以下两种情况：
* 通过给 this.state 赋值对象来初始化内部 state。
* 为事件处理函数绑定实例

在 constructor() 函数中不要调用 setState() 方法。
如果你的组件需要使用内部 state，请直接在构造函数中为 this.state 赋值初始 state：
```
* static getDerivedStateFromProps()

```
getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。
```
* **render()**

```
页面state或props发生变化时执行。
```
* **componentDidMount()**

```
组件挂载完成时被执行。一般也在这里发起网路请求
```
### 更新
当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
* static getDerivedStateFromProps()

```
// 不常用的生命周期方法
getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。
```
* shouldComponentUpdate()

```
// 不常用的生命周期方法
根据 shouldComponentUpdate() 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。
默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。
```
* **render()**

```
render() 方法是 class 组件中唯一必须实现的方法。
当 render 被调用时，它会检查 this.props 和 this.state 的变化
```
* getSnapshotBeforeUpdate()

```
// 不常用的生命周期方法
getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。
```
* **componentDidUpdate()**
```
componentDidUpdate() 会在更新后会被立即调用。首次渲染不会执行此方法。
```
### 卸载
当组件从 DOM 中移除时会调用如下方法：
* **componentWillUnmount()**
```
componentWillUnmount() 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，
例如，清除 timer，取消网络请求或清除在 componentDidMount() 中创建的订阅等。
```
### 错误处理
当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
* static getDerivedStateFromError()
```
// 不常用的生命周期方法
此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state
```
* componentDidCatch()
```
// 不常用的生命周期方法
componentDidCatch() 会在“提交”阶段被调用，因此允许执行副作用。 它应该用于记录错误之类的情况：
```
## React中的axios数据请求
### 安装axios

```
npm install  axios -S
```
### 使用

```
// 那个组件使用在那个页面引入
import axios from 'axios'
```
引入后，可以在componentDidMount生命周期函数里请求ajax，我也建议在componentDidMount函数里执行，因为在render里执行，会出现很多问题，比如一直循环渲染；在componentWillMount里执行，在使用RN时，又会有冲突。所以强烈建议在componentDidMount函数里作ajax请求。

```
componentDidMount(){
    axios.post('https://www.easy-mock.com/mock/5d50245e710c4d0d04d78d19/echart')
        .then((res)=>{console.log('axios 获取数据成功:'+JSON.stringify(res))  })
        .catch((error)=>{console.log('axios 获取数据失败'+error)})
}
```
## 动画react-transition-group
React有着极好的开发生态，开发需要的任何基本需求都可以找到官方或大神造的轮子，动画这种必不可少的东西当然也不例外，React生态中有很多第三方的动画组件，学习一下react-transition-group动画组件。可以满足日常动画开发需求。
推荐的最重要理由是：这个也是react官方提供的动画过渡库，有着完善的API文档）。
### 安装react-transition-group

```
npm install react-transition-group -S
```
安装好后，你可以先去github上来看一下文档，他是有着三个核心库（或者叫组件）。
* Transition
* CSSTransition
* TransitionGroup
#### 使用CSSTransition
先用import进行引入，代码如下：
```
import { CSSTransition } from 'react-transition-group'
```
引入后便可以使用了，使用的方法就和使用自定义组件一样,直接写<CSSTransition>，而且不再需要管理className了，这部分由CSSTransition进行管理。

```
render() { 
    return ( 
        <div>
            <CSSTransition 
                in={this.state.isShow}   //用于判断是否出现的状态
                timeout={2000}           //动画持续时间
                classNames="boss-text"   //className值，防止重复
            >
                <div>BOSS级人物-孙悟空</div>
            </CSSTransition>
            <div><button onClick={this.toToggole}>召唤Boss</button></div>
        </div>
        );
}
```
需要注意的是classNames这个属性是由s的，如果你忘记写，会和原来的ClassName混淆出错，这个一定要注意。
* xxx-enter: 进入（入场）前的CSS样式；
* xxx-enter-active:进入动画直到完成时之前的CSS样式;
* xxx-enter-done:进入完成时的CSS样式;
* xxx-exit:退出（出场）前的CSS样式;
* xxx-exit-active:退出动画知道完成时之前的的CSS样式。
* xxx-exit-done:退出完成时的CSS样式。
```
.input {border:3px solid #ae7000}

.boss-text-enter{
    opacity: 0;
}
.boss-text-enter-active{
    opacity: 1;
    transition: opacity 2000ms;

}
.boss-text-enter-done{
    opacity: 1;
}
.boss-text-exit{
    opacity: 1;
}
.boss-text-exit-active{
    opacity: 0;
    transition: opacity 2000ms;

}
.boss-text-exit-done{
    opacity: 0;
}
```
##### unmountOnExit 属性
CSSTransition加上unmountOnExit属性,加上这个的意思是在元素退场时，自动把DOM也删除，这是以前用CSS动画没办法做到的。

```
render() { 
    return ( 
        <div>
            <CSSTransition 
                in={this.state.isShow}   //用于判断是否出现的状态
                timeout={2000}           //动画持续时间
                classNames="boss-text"   //className值，防止重复
                unmountOnExit
            >
                <div>BOSS级人物-孙悟空</div>
            </CSSTransition>
            <div><button onClick={this.toToggole}>召唤Boss</button></div>
        </div>
        );
}
```
#### 使用TransitionGroup
它就是负责多个DOM元素的动画的，我们还是拿小姐姐这个案例作例子，现在可以添加任何的服务项目，但是都是直接出现的，没有任何动画，现在就给它添加上动画。添加动画，先引入transitionGrop。

```
import {CSSTransition , TransitionGroup} from 'react-transition-group'

```
引入之后，就可以使用这个组件了，方法是在外层增加<TransitionGroup>标签。

```
<ul ref={(ul)=>{this.ul=ul}}>
    <TransitionGroup>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </TransitionGroup>
</ul> 

```
但是只有这个<TransitonGroup>是不够的，你还是需要加入<CSSTransition>,来定义动画。

```
<ul ref={(ul)=>{this.ul=ul}}>
    <TransitionGroup>
        <CSSTransition
            timeout={1000}
            classNames='boss-text'
            unmountOnExit
            appear={true}
            key={index+item}
        >
            <li>1</li>
        </CSSTransition>
        <CSSTransition
            timeout={1000}
            classNames='boss-text'
            unmountOnExit
            appear={true}
            key={index+item}
        >
            <li>2</li>
        </CSSTransition>
    </TransitionGroup>
</ul> 
```
React动画还有很多知识，能做出很多酷炫的效果，完全可以单独分出来一个岗位，我在工作中用的都是比较简单的动画，用react-transition-group动画已经完全可以满足我的日常开发需求了
****
以上大部分内容和讲解都来来自[**技术胖**](https://juejin.im/post/6844903870213292045)的博客,结合了自己对react的理解。