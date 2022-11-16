# 禁止直接使用 Object.prototypes 的内置属性 (no-prototype-builtins)

配置文件中的 `"extends": "eslint:recommended"` 属性启用了此规则。

在ECMAScript 5.1中，新增了 `Object.create`，它支持使用指定的 `[[Prototype]]` 创建对象。`Object.create(null)` 是一种常见的模式，用于创建将用作映射的对象。当假定对象将包含来自`Object.prototype` 的属性时，这可能会导致错误。该规则防止直接从一个对象调用某些 `Object.prototype` 的方法。

此外，对象可以具有属性，这些属性可以将 `Object.prototype` 的内建函数隐藏，可能导致意外行为或拒绝服务安全漏洞。例如，web 服务器解析来自客户机的 JSON 输入并直接在结果对象上调用 `hasOwnProperty` 是不安全的，因为恶意客户机可能发送一个JSON值，如 `{"hasOwnProperty": 1}`，并导致服务器崩溃。

为了避免这种细微的 bug，最好总是从 `Object.prototype` 调用这些方法。例如，`foo.hasOwnProperty("bar")` 应该替换为 `Object.prototype.hasOwnProperty.call(foo, "bar")`。

## Rule Details

该规则禁止直接在对象实例上调用 `Object.prototype` 的一些方法。

**错误** 代码示例：

```
/*eslint no-prototype-builtins: "error"*/

var hasBarProperty = foo.hasOwnProperty("bar");

var isPrototypeOfBar = foo.isPrototypeOf(bar);

var barIsEnumerable = foo.propertyIsEnumerable("bar");
```

**正确** 代码示例：

```
/*eslint no-prototype-builtins: "error"*/

var hasBarProperty = Object.prototype.hasOwnProperty.call(foo, "bar");

var isPrototypeOfBar = Object.prototype.isPrototypeOf.call(foo, bar);

var barIsEnumerable = {}.propertyIsEnumerable.call(foo, "bar");
```

# 背后的原理

今天在想到原型链的时候突然意识到为何 ESLint 不允许从目标对象调用 Object 原型方法。

在 JS 中，往往通过改变原型链实现继承。一旦原型链发生改变，原先可以访问到的原型属性、方法便可能无法访问。考虑最极端的情况，若 obj 原先原型链的最顶端是 Object，此时可以通过原型链访问 Object.hasOwnProperty 方法；而若改变后，顶端不再是 Object，那么访问 obj.hasOwnProperty 访问就会得到 undefined。因此，直接从对象访问原型方法，很可能会带来隐藏的 BUG。