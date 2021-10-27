## Hash模式
hash模式是以url的hash值来作为路由，这也是vue的默认模式；

hash模式的原理是动态的更改location.hash的值，同时对它进行监听来实时渲染相应的页面，这个hash值也就是url中 “#”后面的那个就hash值；

vue路由hash模式主要依赖几个特性

首先第一点，**hash模式的这个hash值它只是路由的一个状态，在向服务端发送请求的时候hash值的部分不会被发送**
其次**hash值的更改不会导致页面刷新，但是它可以在浏览器的历史记录中新增一条记录，并且我们可以通过浏览器前进后退按钮来动态改变历史记录
同时用onhashchange事件来监听hash值的变化，从而实现根据hash值的变化来渲染相应的页面内容**
我们可以在js中简单实现一下

```html
<html>

	<head>
		<meta charset="utf-8">
		<title></title>
	</head>

	<body>
		<span class="cur"></span>
		<button type="button" id="btn">下一页</button>

		<script type="text/javascript">
			let btn = document.querySelector("#btn");
			let cur = document.querySelector(".cur");
			let page = 0;
			btn.addEventListener("click",()=>{
				page++;
				cur.innerHTML = page;
				location.hash = page
			})
		</script>

	</body>
</html>
```


可以看到，hash值在动态改变，但是页面没有刷新，并且此时我们可以在浏览器的历史记录中找到这些记录

现在我们点击浏览器的前进后退按钮，url会改变，但页面不会刷新，因为我们还没有绑定onhashchange事件。

绑定hashchange事件：

```javascript
	<script type="text/javascript">
		let btn = document.querySelector("#btn");
		let cur = document.querySelector(".cur");
		let page = 0;
		btn.addEventListener("click",()=>{
			page++;
			cur.innerHTML = page;
			location.hash = page
		})
		
		// 该事件可动态监听到hash值的变化
		window.onhashchange = (e)=>{
			cur.innerHTML = e.newURL.substring(e.newURL.indexOf("#")+1);
		}
	</script>
```
此时我们页面的hash值的更改就可以被监听到，并且我们可以动态改变页面内容，而不需要重新刷新页面。

##  History模式
history模式主要是依赖与h5的historyApi，这其中主要包括两个api，pushState和replaceState；

history.pushState("","","")


pushState接收三个参数，第一个参数是当前url的状态，这个稍后用的上

第二个参数不用管，这个参数还没有被实现，目前还没什么用，

第三个参数就是用来更新新的url

首先这两个api都能在不刷新页面的情况下去动态更改url，并且它的更改可以作用的浏览器的历史记录中
其次第二点，我们可以通过浏览器前进后退按钮来动态切换这个url，并且此时可以用popstate事件来监听url的切换
另外这种模式它的整个url都会被发送到服务器端
下面简单实现一下

pushState
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<span class="cur"></span>
		<button type="button" id="btn">下一页</button>
		<script type="text/javascript">
			let btn = document.querySelector("#btn");
			let cur = document.querySelector(".cur");
			let page = 0;
			btn.addEventListener("click",()=>{
				page++;
				cur.innerHTML = page;
				history.pushState({page},"",`?page=${page}`)
			})
		</script>
	</body>
</html>


url地址发生改变了，但页面并没有刷新，此时我们可以在浏览器的历史记录中查看这些记录



现在我们点击前进后退按钮还没什么用，因为我们还没有绑定popstate事件；

绑定popstate事件：

```javascript
	<script type="text/javascript">
		let btn = document.querySelector("#btn");
		let cur = document.querySelector(".cur");
		let page = 0;
		btn.addEventListener("click",()=>{
			page++;
			cur.innerHTML = page;
			history.pushState({page},"",`?page=${page}`)
		})
		
		// 当点击前进后退按钮时触发此事件
		window.onpopstate = function(e){
			if(e.state){
				// e.state 就是之前设置的url的状态信息
				page = e.state.page;
				cur.innerHTML = page;
			}
		}
	</script>
```


此时可以跟随url来动态更改页面的内容，但是页面却不用刷新

 

replaceState
replaceState的参数和pushState的参数相同，只不过它是直接更改当前页面的url；

我们将上面的代码改动一下：


<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<span class="cur"></span>
		<button type="button" id="btn">下一页</button>
		<button type="button" id="replace">更新</button>
		<script type="text/javascript">
			let btn = document.querySelector("#btn");
			let replace = document.querySelector("#replace");
			let cur = document.querySelector(".cur");

```javascript
		let page = 0;
		let str = "AAA"
		btn.addEventListener("click",()=>{
			page = parseInt(page)
			page++;
			cur.innerHTML = page;
			history.pushState({page},"",`?page=${page}`)
		})
```


​			
```javascript
		replace.addEventListener("click",()=>{
			page = page+str;
			cur.innerHTML = page;
			history.replaceState({page},"",`?page=${page}`)
		})
```


​			
```javascript
		// 当点击前进后退按钮时触发此事件
		window.onpopstate = function(e){
			if(e.state){
				// e.state 就是之前设置的url的状态信息
				page = e.state.page;
				cur.innerHTML = page;
			}
		}
	</script>
</body>
```
</html>




可以看到，url进行了更新，并且它可以作用在历史记录当中。

 

History模式所引起的问题
我们之前说过，history模式的整个url都会被发送到服务器端，这就会导致一个问题

http://192.168.1.101:8080/Search

http://192.168.1.101:8080/Expand
就是我们不能对它们进行刷新操作，因为我们一刷新就会将这个地址向服务端再发送一次，

若我们直接将这种地址发送到服务器端，服务器就会去查找这个地址所指向的模块，但因为我们的项目是一个单页项目，它只有index.html这一个文件，因此它肯定是不找不到这些模块的，这就会导致它会返回404；



因此我们要对服务器进行一些相关配置，这里以Apache服务器为例,具体配置方式查看下面链接：

https://www.cnblogs.com/litings/p/10802972.html

hash模式不会导致此问题

http://192.168.1.101:8080/#/Search
而hash模式不同，hash模式它#号后面的内容不会被发送，它只是当前客户端路由的状态，因此hash模式可以直接兼容这些服务器
————————————————
原文链接：https://blog.csdn.net/tdl081071tdy/article/details/113410501



##  总结

1. hash模式的这个hash值它只是路由的一个状态，在向服务端发送请求的时候**hash值的部分不会被发送**，其次**hash值的更改不会导致页面刷新**，但是它可以在浏览器的历史记录中新增一条记录，并且我们可以通过浏览器前进后退按钮来动态改变历史记录，同时用onhashchange事件来监听hash值的变化，从而实现根据hash值的变化来渲染相应的页面内容。
2. history模式的整个url都会被发送到服务器端