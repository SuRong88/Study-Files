##  If-Modified-Since和If-None-Match

简单的说，Last-Modified 与If-Modified-Since 都是用于记录页面最后修改时间的 HTTP 头信息，只是 Last-Modified 是由服务器往客户端发送的 HTTP 头，而 If-Modified-Since 则是由客户端往服务器发送的头，可 以看到，再次请求本地存在的 cache 页面时，客户端会通过 If-Modified-Since 头将先前服务器端发过来的 Last-Modified 最后修改时间戳发送回去，这是为了让服务器端进行验证，通过这个时间戳判断客户端的页面是否是最新的，如果不是最新的，则返回新的内容，如果是最新的，则 返回 304 告诉客户端其本地 cache 的页面是最新的，于是客户端就可以直接从本地加载页面了，这样在网络上传输的数据就会大大减少，同时也减轻了服务器的负担。

ETags和If-None-Match是一种常用的判断资源是否改变的方法。类似于Last-Modified和HTTP-IF-MODIFIED-SINCE。但是有所不同的是Last-Modified和HTTP-IF-MODIFIED-SINCE只判断资源的最后修改时间，而ETags和If-None-Match可以是资源任何的任何属性，不如资源的MD5等。

ETags和If-None-Match的工作原理是在HTTP Response中添加ETags信息。当客户端再次请求该资源时，将在HTTP Request中加入If-None-Match信息（ETags的值）。如果服务器验证资源的ETags没有改变（该资源没有改变），将返回一个304状态；否则，服务器将返回200状态，并返回该资源和新的ETags。

##  If-Modified-Since为什么还要If-None-Match

ETags和If-None-Match是一种常用的判断资源是否改变的方法。

ETags和If-None-Match的工作原理是在HTTP Response中添加ETags信息。类似于Last-Modified和HTTP-IF-MODIFIED-SINCE。
但是有所不同的是Last-Modified和HTTP-IF-MODIFIED-SINCE只判断资源的最后修改时间，而ETags和If-None-Match可以是资源任何的任何属性，比如资源的MD5等。