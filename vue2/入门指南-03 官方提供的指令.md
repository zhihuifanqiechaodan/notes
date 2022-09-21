## 第三章 学习vue中的指令
vue官方为我们提供了很多它自己原生的指令, 工作项目中会经常用到,这里我先为大家讲解几个基础的指令

### v-cloak指令
使用v-cloak指令能够解决差值表达式闪烁的问题

```
// 需要在样式中设置以下样式
<style>
    [v-cloak] {
        display: none;
    }
</style>

// 使用方式: 在我们使用差值表达式的标签添加该指令
<p v-cloak>{{ msg }}</p>
```
### v-text指令
v-text 是没有闪烁的问题
但是 v-text 会覆盖元素中原本的内容，但是差值表达式只会替换自己的这个占位符，不会把整个元素的内容清空

```
使用方式:
<p v-text>{{ msg }}</p>
```
### v-html指令
v-html 会解析标签，以上两种不会，它也会更改元素中原本的内容

```
使用方式：
new app({
    el: "#app",
    data: {
        msg: "<h1>我是一个标签元素</h1>"
    }
})
<p v-html="msg"></p>
```
### v-bind指令
* v-bind： 是Vue中，提供的用来绑定属性的指令,只能实现数据的单向绑定,从M自动绑定到V,无法实现数据的双向绑定。
* 注意：v-bind： 指令可以被简写为 ：要绑定的属性
* v-bind 中可以写合法的js表达式

```
// 使用方式:
<p v-bind:title="msg"></p>

简写方式:
<p :title="msg"></p>

// 属性中写js表达式
<p v-bind:title="msg+'合法表达式'"></p>

```
#### 绑定class属性的用法
* 数组的写法
```
// 直接传递一个数组, 数组里面的类名要写字符串, 注意:这里的class需要使用v-bind做数据绑定
使用方式：
<p :class="['thin', 'italic']"></p>
```
* 数组中嵌套对象

```
// 数组中推荐使用这种方式
使用方式：
<p :class="['thin', 'italic',{'active':flag}]"></p> // 这里的flag在data中定义, 是一个布尔值
```
* 数组中使用三元表达式

```
// data中定义一个布尔值类型的flag,在数组中用三元表示来显示这个flag
使用方式：
<p :class="['thin', 'italic', flag ? 'active':'noactive']"></p>
```
* 直接使用对象

```
// 在为class使用v-bind绑定对象的时候,对象的属性是类型,
// 由于对象的属性可带可不带引号,写法自己决定, 属性的值是一个标识符
使用方式: 
<p :class="{thin: true, italic: true, active: false}"></p>
```
#### 绑定style属性的用法
* 直接在标签上通过 :style 的形式书写

```
// 对象就是无序键值对的集合
使用方式：
<p :style="{color:'red', 'font-weight':200}"></p>
```
* 将样式定义在data中, 在:style绑定的标签中引用

```
// 先将样式定义在data中的一个变量身上
new app({
    el: "#app",
    data: {
        styleObject: {color:'red', 'font-weight':200}
    }
})
// 在标签上,通过属性绑定的形式,将样式对象应用到元素中
<p :style="styleObject"></p>
```
* 在 :style 中通过数组, 引用多个 data 上的样式对象

```
// 先将样式定义在data中的一个变量身上
new app({
    el: "#app",
    data: {
        styleObject1: {color:'red', 'font-weight':200},
        styleObject2: {color:'red', 'font-weight':200}
    }
})
// 在标签上,通过属性绑定的形式,将样式对象应用到元素中
使用方式：
<p :style="[styleObject1, styleObject2]"></p>
```
### v-model指令

Vue中唯一一个实现双向数据绑定的指令, **注意 : 只能运用到表单元素中**

```
使用方式:
<input v-model="msg"></input> // 修改imput输入框的值, 下面p标签绑定的内容会随之改变
<p>{{ msg }}</p>
```
### v-for指令
* 在使用 v-for 指令的时候**要注意加上 key 属性**

* Vue2.2以后的版本规定,当前组件使用v-for循环的时候, 或者在一些特殊情况中, 如果v-for有问题, 必须在使用v-for的同时, 指定唯一的字符串/数组类型 :key值。

* v-for="(val, key) in list" // **in 后面可以放普通数组, 对象数组, 对象, 还可以放数字**
#### 迭代数组

```
// 先在data上定义一个数组
new app({
    el: "#app",
    data: {
        list: [1, 2, 3, 4]
    }
})
使用方式：item是循环的每一项,list是循环的数组,index是索引值
<li v-for="(item, index)in list" :key="index">{{index}}~~~{{item}}</li>
```
#### 迭代对象数组

```
new app({
    el: "#app",
    data: {
        list:[{id:1,name:'tank1'}, {id:2,name:'tank2'}, {id:3,name:'tank3'}]
    }
})
使用方式：
<li v-for="(item, index)in list" :key="index">
  id:{{item.id}} --- 名字:{{item.name}} --- 索引{{index}}
</li>
```
#### 迭代对象

```
new app({
    el: "#app",
    data: {
        list: {
            name1: "abc1",
            name2: "abc2",
            name3: "abc3"
        }
    }
})
使用方式：在遍历对象身上的键值对的时候.除了有val、key, 在第三个位置还有一个索引index, 索引基本用不到
<li v-for="(val, key, index) in list" :key="index">
    值是:{{ val }} --- 键是: {{key}} --- 索引{{index}}
</li>
```
#### 迭代数字
如果使用v-for迭代数字,前面的 count 从 1 开始

```
使用方式：
<li v-for="(count, index) in 10" :key="index">这是第 {{count}} 次循环</li>
```
### v-if 和 v-else 和 v-else-if 还有 v-show指令
如果元素涉及到频繁的切换,最好不要使用 v-if 而推荐使用 v-show

如果元素可能不太频繁被显示出来被用户看,推荐用 v-if 和 v-else
* v-if 和 v-else

```
// v-if 的特点: 每次都会重新删除或创建元素
// v-if 有较高的切换性能消耗
// v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。
使用方式：
new app({
    el: "#app",
    data: {
        flag: true // 定义一个变量为布尔值类型
    }
})
<p v-if="flag">我在flag为true的时候显示</p>  
<p v-else>我在flag不为true的时候显示</p>
```
* v-show

```
// v-show的特点: 每次不会重新进行DOM的删除和创建,只是切换了元素的display:none样式
// v-show有较高的初始渲染消耗
使用方式：
<p v-show="flag">我在flag为true的时候显示</p>
```