---
theme: cyanosis
highlight: an-old-hope
---
Compostition API集合，解决Vue2组件开发问题

更好的TypeScript支持

新的Api支持



### 什么是reactive？

- reactive是vue3中提供的实现响应式数据的方法
- 在vue2中响应式数据是通过defineProperty来实现的
  - 因为有缺陷，在处理数组方面，所以vue3中响应式数据是通过ES6的Proxy来实现的

### reactive注意点

- reactive参数必须是对象(json/arr)
- 如果给reactive传递了其他对象
  - 默认情况下修改对象，界面不会自动更新。
  - 如果想更新，可以通过重新赋值的方式。

****

### 什么是ref?

- ref和reactive一样，也用用来实现响应式数据的方法
- 由于reactive必须传递一个对象，所有导致在企业开发中如果我们只想让某个变量实现响应式的时候会非常麻烦，所有vue3就给我们提供了ref方法，实现对简单之的监听

### ref本质

- ref底层的本质还是reactive,系统会自动根据我们给ref传入的值将它转换成以下这样

  ```js
  ref(xxx) => reactive({value:xxx})
  ```

### ref注意点

- 在vue中使用ref声明的变量在模版中不需要通过.value获取，因为在template模版中，vue会自动给我们添加.value
- 在js中使用ref的值或者更改ref的值必须通过.value获取和更改

****

### 递归监听

- 默认情况下，无论是通过ref还是reactive都是递归监听

- 递归监听存在的问题，如果数据量较大，非常消耗性能

- 非递归监听

#### shallowReactive
```js
    let state = shallowReactive({
      a: "a",
      b: {
        c: "c",
        d: {
          e: "e"
        }
      }
    })
```

**注意：shallowReactive用于创建非递归监听的属性，只会监听第一层**

#### shallowRef

```js
    let state = shallowRef({
      a: "a",
      b: {
        c: "c",
        d: {
          e: "e"
        }
      }
    })
```

**注意：shallowRef监听的是.value的变化。并不是shallowReactive第一层的变化。**

#### triggerRef

```js
    let state = shallowRef({
      a: "a",
      b: {
        c: "c",
        d: {
          e: "e"
        }
      }
    })
    
    // 修改后页面并不会更新视图
    state.b.d.e = "ee"
    // 主动触发视图更新
    triggerRef(state)
```
- 应用场景

  `一半情况下我们使用ref和reactive即可，只有在需要监听的数据量比较大的时候，我们才使用shallowRef/shallowReactive`

### toRaw

了解toRaw之前我们可以先看下以下的一个小列子来理解为啥需要用到toRaw

```js
<script lang="ts">
import { reactive } from "vue";

export default {
  name: "App",
  setup() {
    let data = {
      name: "只会番茄炒蛋",
      age: 18,
    };
    let state = reactive(data);
    
    function changeAge() {
      // state.age += 1;
      data.age += 1;
      console.log(data);
      console.log(state);
    }
    return {
      state,
      changeAge,
    };
  },
};
</script>
```
我们会发现，数据改变了但是视图并没有更新。这里我们明白的一点就是data和state是引用关系。state的本质是一个Proxy对象，在这个Proxy对象中引用了data

如果直接修改data,数据是改变的，但是无法触发页面的更新

只有通过包装之后的对象来修改，才会触发页面的更新

这个方法有啥作用场景就是， 当我们只想修改数据，但是不想视图发生变化时使用。因为ref和reactive数据类型的特点就是每次修改数据都会被追踪，都会更新ui界面。非常消耗性能。如果我们有一些操作不需要追踪和更新页面，那么这时候就可以通过toRaw方法拿到他的原始数据。这样就能够节省性能

**总结：toRaw方法从响应式数据获取原始数据**

**注意：当我们通过toRaw获取ref类型的原始数据时会发现获取不到**。

ref本质：reactive

Ref(obj) => reactive({value: obj})

这也是为什么需要通过.value来获取ref创建的数据



总结：如果想要通过toRaw拿到ref类型的原始数据（创建时传入的那个数据）那么久必须明确告诉toRaw方法，要获取的时.value的值。因为经过Vue处理之后，.value中保存的才是当初的原始数据。

```js
<script lang="ts">
import { ref, toRaw } from "vue";

export default {
  name: "App",
  setup() {
    let data = {
      name: "只会番茄炒蛋",
      age: 18,
    };
    let state = ref(data);
    let data2 = toRaw(state.value);
    console.log(data2);

    function changeAge() {
      // state.age += 1;
      data.age += 1;
      console.log(data);
      console.log(state);
    }
    return {
      state,
      changeAge,
    };
  },
};
</script>
```

### Markrow

永远不会被追踪的数据，被markrow声明的变量永远不会被追踪，即使被reactive或者ref声明。修改数据也不会改变数据，更不会触发视图更新

### ref和toRef的区别

- ref 和toRef 修改响应式数据都会会影响以前的数据
- ref 数据发生改变，界面就会自动更新
- toRef数据发生改变，界面也不会自动更新

****

**toRef应用场景：如果想让响应式数据和以前的数据关联起来，并且更新响应式数据之后还不想更新UI，那么就可以使用。**

### toRefs

将响应式对象转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。



或者说将一个对象身上的所有属性变为响应式数据可以使用。
**以上这些方法都是为了提升性能。主要用的多的还是ref和reactive**

## `customRef`

创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。它需要一个工厂函数，该函数接收 `track` 和 `trigger` 函数作为参数，并且应该返回一个带有 `get` 和 `set` 的对象。

为什么要自定义ref我们下面再说。先说如何实现

```js
<script lang="ts">
import { customRef } from "vue";

function myRef(value) {
  return customRef((track, trigger) => {
    return {
      get() {
        // track()方法告诉vue这个数据是要被追踪的
        track();
        console.log("get", value);
        return value;
      },
      set(newValue) {
        value = newValue;
        console.log("set", value);
        // trigger()方法告诉vue更新视图
        trigger();
      },
    };
  });
}

export default {
  name: "App",
  setup() {
    let state = myRef(18);
    function change() {
      state.value = 20;
    }
    return {
      state,
      change,
    };
  },
};
</script>
```

为什么要自定义ref

首先我们要知道一点的是setup函数只能是一个同步的函数，不能是一个异步的函数。至于为什么不能是一个异步的函数大家在setup前面加上async让后正常声明变量在页面中显示就知道原因了。

所以当我们在业务中需要发起ajax请求获取数据的时候，且想通过async await来获取数据就行不通了。

那么无法使用async await的时候就意味着我们的代码中可能会出现大量的回调函数。

那么我们还想按照以前同步代码的方式来书写 就可以考虑使用自定义ref。

```js
<script lang="ts">
import { customRef } from "vue";

function myRef(value) {
  return customRef((track, trigger) => {
    fetch(value)
      .then(async (res) => {
        value = await res.json();
        console.log(value);
        trigger();
      })
      .catch((reason) => {
        console.log(reason);
      });

    return {
      get() {
        // track()方法告诉vue这个数据是要被追踪的
        track();
        console.log("get", value);
        return value;
      },
      set(newValue) {
        value = newValue;
        console.log("set", value);
        // trigger()方法告诉vue更新视图
        trigger();
      },
    };
  });
}

export default {
  name: "App",
  setup() {
    let state = myRef("../public/data.json");
    function change() {
      state.value = 20;
    }
    return {
      state,
      change,
    };
  },
};
</script>
```

### ref获取元素

vue3和vue2不同， vue3并没有this.$属性的那些东西了。想要获取元素也很简单

```js
<template>
  <div>
    <div ref="box">我是box</div>
  </div>
</template>

<script lang="ts">
import { onMounted, ref } from "vue";

export default {
  name: "App",
  setup() {
    let box = ref(null);
    onMounted(() => {
      console.log(box.value);
    });
    return {
      box,
    };
  },
};
</script>
```

这里注意为什么打印的是box.value 和上面ref声明变量获取的原因一致。

为什么在onMounted中打印。这点生命周期和2是一致的。因为setup和created还有beforeC reated一样，这这期间dom还没有创建

###  readonly家族

* readonly

接受一个对象 (响应式或纯对象) 或 [ref](https://v3.cn.vuejs.org/api/refs-api.html#ref) 并返回原始对象的只读代理。只读代理是深层的：任何被访问的嵌套 property 也是只读的。

用于创建一个只读属性，并且是递归只读

* shallowReadonly

用于创建一个只读的数据，但是不是递归只读。只有第一层只读

* isReadonly

用于判断属性是否是一个readonly返回布尔值

**这里大家可能会有疑问那么我直接使用const声明变量不就行了吗。 这里要注意。const通常用于声明常量，且通常是声明原始类型。但是如果声明引用类型。随便变量不可重新赋值。但是引用类型的属性的值是可以更改的。**

### 关于响应式数据本质上的理解

- 在vue2.x中是通过defineProperty来实现响应式数据的。但是有部分缺陷，例如数组的处理

- 在vue3.x中是通过Proxy来实现响应式数据的

```js
// 简单实现
let obj = { name: "番茄", age: 18 }
let state = new Proxy(obj, {
    get(obj, key) {
        console.log(obj, key); // {name: '番茄', age: 18} name
    },
    set(obj, key, value) {
        console.log(obj, key, value); // {name: '番茄', age: 18} name 炒蛋
        obj[key] = value
        console.log("更新UI视图");
      	return true
    }
})

console.log(state.name); // 番茄
state.name = "炒蛋"
console.log(state); // Proxy {name: '炒蛋', age: 18}
```

通过上述简单的列子。我们就可以在获取值和设置值的做很多事情了。

**注意点：set方法一定要给返回值告诉此次操作完成成功。因为在set操作中可能不止做一次修改例如数组**

#### proxy代理数组

```js
// 简单实现
let arr = [1, 2, 3]
let state = new Proxy(arr, {
    get(obj, key) {
        console.log(obj, key); // [1, 2, 3] 1
        return obj[key]
    },
    set(obj, key, value) {
        /**
         *  [ 1, 2, 3 ] push
            [ 1, 2, 3 ] length
            [ 1, 2, 3 ] 3 4
            更新UI视图
            [ 1, 2, 3, 4 ] length 4
            更新UI视图
            [ 1, 2, 3, 4 ]
         */
        console.log(obj, key, value);
        obj[key] = value
        console.log("更新UI视图");
        return true
    }
})

// console.log(state[1]) // 2
state.push(4)
console.log(state);
```

通过上述代码我们会发现set方法中执行了两次。一次是[ 1, 2, 3 ] 3 4， 一次是[ 1, 2, 3, 4 ] length 4

set方法一定通过返回值告诉当前的操作是否成功。如果将上面的return ture注视掉就会报错

```js
state.push(4)
      ^

TypeError: 'set' on proxy: trap returned falsish for property '3'
    at Proxy.push (<anonymous>)
    at Object.<anonymous> (/Users/cctvabu/vite-vue3-project/proxy.js:26:7)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
```
**所以注意在set方法中一定要通过返回值告诉当前操作是否成功**
****
以上呢就是我学习了解到的部分api知识。后续还在学习和补充中。当然有不对的地方请评论指出，我查阅资料修改。

#### 找工作
本人最近准备跳槽找工作。有介绍工作的可以联系我哈～