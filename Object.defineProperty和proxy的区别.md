### 观察者模式

应用场景:

1. 场景一: 当观察的数据对象发生变化时, 自动调用相应函数。比如 vue 的双向绑定;
2. 场景二: 每当调用对象里的某个方法时, 就会调用相应'访问'逻辑。比如给测试框架赋能的 spy 函数;

### 场景一: 双向绑定

#### Object.defineProperty

使用 `Object.defineProperty(obj, props, descriptor)` 实现观察者模式, 其也是 [vue 双向绑定](https://github.com/MuYunyun/blog/issues/11) 的核心, 示例如下(当改变 obj 中的 value 的时候, 自动调用相应相关函数):

```js
var obj = {
  data: { list: [] },
}

Object.defineProperty(obj, 'list', {
  get() {
    return this.data['list']
  },
  set(val) {
    console.log('值被更改了')
    this.data['list'] = val
  }
})
```

#### Proxy

Proxy/Reflect 是 ES6 引入的新特性, 也可以使用其完成观察者模式, 示例如下(效果同上):

```js
var obj = {
  value: 0
}

var proxy = new Proxy(obj, {
  set: function(target, key, value, receiver) { // {value: 0}  "value"  1  Proxy {value: 0}
    console.log('调用相应函数')
    Reflect.set(target, key, value, receiver)
  }
})

proxy.value = 1 // 调用相应函数

```

### `vue` 在 3.0 版本上使用 `Proxy` 重构的原因

首先罗列 `Object.defineProperty()` 的缺点:

1. `Object.defineProperty()` 不会监测到数组引用不变的操作(比如 `push/pop` 等);
2. `Object.defineProperty()` 只能监测到对象的属性的改变, 即如果有深度嵌套的对象则需要再次给之绑定 `Object.defineProperty()`;

关于 `Proxy` 的优点

1. 可以劫持数组的改变;
2. `defineProperty` 是对属性的劫持, `Proxy` 是对对象的劫持;
3. `Proxy`有多达13种拦截方法,不限于`apply`、`ownKeys`、`deleteProperty`、`has`等等是`Object.defineProperty`不具备的。



### 缺点： 内置对象：内部插槽（Internal slots）

许多内置对象，例如 `Map`, `Set`, `Date`, `Promise` 等等都使用了所谓的 “内部插槽”。

它们类似于属性，但仅限于内部使用，仅用于规范目的。例如， `Map` 将项目存储在 `[[MapData]]`中。内置方法直接访问它们，而不通过 `[[Get]]/[[Set]]` 内部方法。所以 `Proxy` 不能拦截。

为什么要在意呢？他们是内部的！

好吧，这就是问题。在像这样的内置对象被代理后，代理对象没有这些内部插槽，因此内置方法将失败。

