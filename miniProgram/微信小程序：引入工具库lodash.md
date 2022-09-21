

![](https://www.lodashjs.com/img/lodash.png)

### 简介

Lodash 是一个一致性、模块化、高性能的 JavaScript 实用工具库。

> Lodash 遵循 MIT 开源协议发布，并且支持最新的运行环境。 查看各个构件版本的区别并选择一个适合你的版本。



### 小程序引入Lodash

方式一：安装npm包

```js
npm install lodash
```

方式二：下载压缩文件

**utils/lodash.mini.js**

```js
https://raw.githubusercontent.com/lodash/lodash/4.17.15-npm/lodash.min.js
```

**推荐采用下载压缩文件到本地使用，小程序包大小有限制**

### 问题修复

小程序中直接引入lodash使用时会报错,需要在引入一个修复报错的包

**utils/lodash-fix.js**

```js
/**
 * 修复 微信小程序中lodash 的运行环境
 */
 
global.Object = Object;
global.Array = Array;
// global.Buffer = Buffer
global.DataView = DataView;
global.Date = Date;
global.Error = Error;
global.Float32Array = Float32Array;
global.Float64Array = Float64Array;
global.Function = Function;
global.Int8Array = Int8Array;
global.Int16Array = Int16Array;
global.Int32Array = Int32Array;
global.Map = Map;
global.Math = Math;
global.Promise = Promise;
global.RegExp = RegExp;
global.Set = Set;
global.String = String;
global.Symbol = Symbol;
global.TypeError = TypeError;
global.Uint8Array = Uint8Array;
global.Uint8ClampedArray = Uint8ClampedArray;
global.Uint16Array = Uint16Array;
global.Uint32Array = Uint32Array;
global.WeakMap = WeakMap;
global.clearTimeout = clearTimeout;
global.isFinite = isFinite;
global.parseInt = parseInt;
global.setTimeout = setTimeout;
```

### 使用

utils/lodash.js

```js
import '~/utils/lodash.fix';
import { throttle, debounce, flattenDeep, uniqBy, cloneDeep, isEqual, differenceWith, chunk } from '~/utils/lodash.mini';

export default {
  throttle,
  debounce,
  flattenDeep,
  uniqBy,
  cloneDeep,
  isEqual,
  differenceWith,
  chunk,
};
```

页面使用时直接引入该文件即可。