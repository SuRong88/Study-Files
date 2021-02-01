## 当传递的内容为空时,context指向window

##  1.call 逐个传递参数

```javascript
Function.prototype.myCall = function(context, ...args) {
	context = context || window
	context.fn = this
	const result = context.fn(args)
	delete context.fn
	return result
}
```



## 2.apply 所有参数为一个数组
```javascript
Function.prototype.myApply = function(context, args) {
	context = context || window
	context.fn = this
	const result = context.fn(...args)
	delete context.fn
	return result
}
```



## 3.call返回一个指定this指向的函数
```javascript
Function.prototype.myBind = function(context, ...args) {
	context = context || window
	context.fn = this
	const fn = context.fn
	delete context.fn
	return function() {
		fn.apply(context, ...args)
	}
}
```



```javascript
const jr = {
	name: 'sjr',
	age: 23
}

const doudou = {
	name: 'doudou',
	age: 24
}

function fn(args) {
	console.log(args);
	console.log(this.name)
}

fn.myCall(jr, 1, 2, 3)
fn.myCall(doudou, [2, 3, 4])
console.log(jr, doudou);

const obj = {
	key: 'objKey',
	fn: function() {
		console.log(this.key);
	}
}

const objFn = obj.fn
const objFnBind = objFn.myBind(obj)
objFnBind()
```

