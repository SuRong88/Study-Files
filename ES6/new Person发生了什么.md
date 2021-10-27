如下：

```javascript
function Person(name) {
    this.name = name
    return name;
}
let p = new Person('Tom');
复制代码
```

实例化Person过程中，Person返回什么（或者p等于什么）？

**答案：**

```javascript
{name: 'Tom'}
复制代码
```

若将题干改为

```javascript
function Person(name) {
    this.name = name
    return {}
}
let p = new Person('Tom');
复制代码
```

实例化Person过程中，Person返回什么（或者p等于什么）？

**答案：**

```
{}
复制代码
```

**解析**

> 构造函数不需要显示的返回值。使用new来创建对象(调用构造函数)时，如果return的是非对象(数字、字符串、布尔类型等)会忽而略返回值;如果return的是对象，则返回该对象(注：若return null也会忽略返回值）。

**手动实现new关键字**

```javascript
function createObj(...args) {
	const obj = {};
	const Constructor = args.shift();
	obj.__proto__ = Constructor.prototype;
	const ret = Constructor.apply(obj, args);
	return typeof ret === "object" ? ret : obj;
}
```

