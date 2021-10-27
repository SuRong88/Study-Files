## Promise.all、race和any方法都是什么意思？

这篇文章发布于 2021年05月9日，星期日，22:51，归类于 [JS API](https://www.zhangxinxu.com/wordpress/category/js/js-api/)。 阅读 3939 次, 今日 8 次 [9 条评论](https://www.zhangxinxu.com/wordpress/2021/05/promise-all-race-any/#comments)

[区别概述](javascript:)[举例说明差异](javascript:)[什么时候该使用哪个？](javascript:)[兼容性、结语没有其他](javascript:)

by [zhangxinxu](https://www.zhangxinxu.com/) from https://www.zhangxinxu.com/wordpress/?p=9941
本文欢迎分享与聚合，全文转载就不必了，尊重版权，圈子就这么大，若急用可以联系授权。



![promise封面](https://image.zhangxinxu.com/image/blog/202105/promise-cover.png)

### 一、区别概述

先一句话描述下`Promise.all()`、`Promise.race()`和`Promise.any()`的区别。

- `Promise.all()`中的Promise序列会全部执行通过才认为是成功，否则认为是失败；
- `Promise.race()`中的Promise序列中第一个执行完毕的是通过，则认为成功，如果第一个执行完毕的Promise是拒绝，则认为失败；
- `Promise.any()`中的Promise序列只要有一个执行通过，则认为成功，如果全部拒绝，则认为失败；

一例胜千言，通过实例能更好地表示上面3种方法的区别。

### 二、举例说明差异

无论是上传图片还是下载图片，都是异步操作，此时，我们可以将这两个常见交互变成了一个Promise操作，为了简化理解，整个异步过程我们就使用定时器代替，示意如下：

```
const upload = function (blob) {
    let time = Math.round(100 + 500 * Math.random());
    return new Promise((resolve, reject) => {
        // 是否执行测试
        console.log(`run ${time}ms`);
        // 成功失败概率50%
        if (Math.random() > 0.5) {
            setTimeout(resolve, time, 'promise resolved ' + time + 'ms');
        } else {
            setTimeout(reject, time, 'promise rejected ' + time + 'ms');
        }         
    });
};
const load = function (url) {
    // 同upload
};
```

#### 1. Promise.all()

`Promise.all()`里面所有可迭代的Promise都通过则认为是成功，如果有一个拒绝，则认为失败。

测试如下：

```
(async () => {
    try {
        let result = await Promise.all([upload(0), upload(1), upload(2)]);
        console.log(result);
    } catch (err) {
        console.error(err);
    }
})();
```

只有3个upload方法都resolve通过才会认为成功，否则返回第一个出错的提示结构，如下截图所示：

![Promise.all()执行结果](https://image.zhangxinxu.com/image/blog/202105/2021-05-06_172741.png)

在本例中，Promise.all()成功的概率是0.5 * 0.5 * 0.5 = 0.125，也就是成功率是12.5%。

#### 2. Promise.race()

`Promise.race()`顾名思意就是“赛跑”，哪个执行快就使用哪个。

因此，每次执行，无论成功还是失败，其输出的信息中的时间一定是延时时间最短的那个。测试代码如下所示：

```
(async () => {
    try {
        let result = await Promise.race([load(0), load(1), load(2)]);
        console.log(result);
    } catch (err) {
        console.error(err);
    }
})();
```

执行结果如下截图：

![Promise.race执行结果示意](https://image.zhangxinxu.com/image/blog/202105/2021-05-06_175949.png)

可以看到，无论是result还是err的信息中的ms时间，对应的就是3个run时间中最短的那个。

并且，成功的返回值不再是一个数组，而是单个提示。

在本例中，Promise.race()成功的概率是0.5，也就是成功率是50%。

#### 3. Promise.any()

`Promise.any()`更关心成功，只要有一个成功就可以了，除非所有的Promise都拒绝，否则就认为成功。

类似的测试代码：

```
(async () => {
    try {
        let result = await Promise.any([load(0), load(1), load(2)]);
        console.log(result);
    } catch (err) {
        console.error(err);
    }
})();
```

执行结果如下截图：

![Promise.any执行结果示意](https://image.zhangxinxu.com/image/blog/202105/2021-05-06_180624.png)

可以看到出错的类型和上面两个方法都不一样，是AggregateError类型的错误。

> AggregateError: All promises were rejected

成功的返回值是第一个执行成功的Promise的返回值。

在本例中，Promise.any()成功的概率是(1 – 0.5 * 0.5 * 0.5) = 87.5%，也就是成功率是87.5%。

### 三、什么时候该使用哪个？

这一小节讲下三种Promise方法合适的使用场景。

单纯举例，让大家更好地了解这3种Promise方法。

#### 1. Promise.all()

`Promise.all()`在图片批量上传的时候很有用，可以知道什么时候这批图片全部上传完毕，保证了并行，同时知道最终的上传结果。

又例如，页面进行请求的时候，如果请求时间太短，loading图标就会一闪而过，体验并不好。

`Promise.all()`可以保证最低loading时间，例如下面的代码可以保证loading至少出现200ms：

```javascript
let getUserInfo = function (user) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve('姓名：张鑫旭'), Math.floor(400 * Math.random()));
    });
}

let showUserInfo = function (user) {
    return getUserInfo().then(info => {
        console.log('用户信息', info);
        return true;
    });
}

let timeout = function (delay, result) {
    return new Promise(resolve => {
        setTimeout(() => resolve(result), delay);
    });
}
// loading时间显示需要
const time = +new Date();
let showToast = function () {
    console.log('show loading...');
}
let hideToast = function () {;
    console.log('hide loading' + (+new Date() - time));
}
// 执行代码示意
showToast();
Promise.all([showUserInfo(), timeout(200)]).then(() => {
   hideToast();
});
```

执行效果如下所示，可以看到loading从显示到隐藏，一定不会小于200ms。

![loading时间执行示意](https://image.zhangxinxu.com/image/blog/202105/2021-05-06_185724.png)



#### 2. Promise.race()

上面的loading策略仔细一想，有些怪怪的，请求本来很快，还非要显示一个loading，这不是舍本逐末了吗？

所以需求应该是这样，如果请求可以在200ms内完成，则不显示loading，如果要超过200ms，则至少显示200ms的loading。

这个需求可以考虑使用`Promise.race()`方法，执行代码示意如下（getUserInfo、showUserInfo等方法都不变）。

```
// loading时间显示需要
let time = 0;
let showToast = function () {
    time = +new Date()
    console.log('show loading...');
}
let hideToast = function () {;
    console.log('hide loading' + (+new Date() - time));
}
// 执行代码示意
let promiseUserInfo = showUserInfo();
Promise.race([promiseUserInfo, timeout(200)]).then((display) => {
    if (!display) {
        showToast();
        
        Promise.all([promiseUserInfo, timeout(200)]).then(() => {
            hideToast();
        });
    }
});
```

于是，要么用户信息无loading瞬间显示，要么显示至少200ms的loading，这样的体验就会更细致了。

执行结果如下截图示意：

![loading效果](https://image.zhangxinxu.com/image/blog/202105/2021-05-06_191643.png)

**可取消的Promise**

找到一个 `Promise.race()` 方法的案例，出自Michael Clark，代码如下：

```
function timeout(delay) {
  let cancel;
  const wait = new Promise(resolve => {
    const timer = setTimeout(() => resolve(false), delay);
    cancel = () => {
      clearTimeout(timer);
      resolve(true);
    };
  });
  wait.cancel = cancel;
  return wait;
}

function doWork() {
  const workFactor = Math.floor(600*Math.random());
  const work = timeout(workFactor);
  
  const result = work.then(canceled => {
    if (canceled)
      console.log('Work canceled');
    else
      console.log('Work done in', workFactor, 'ms');
      return !canceled;
  });
  result.cancel = work.cancel;
  return result;
}

function attemptWork() {
  const work = doWork();
  return Promise.race([work, timeout(300)])
    .then(done => {
      if (!done)
        work.cancel();
      return (done ? 'Work complete!' : 'I gave up');
  });
}

attemptWork().then(console.log);
```

代码也可以点击这里访问：[promise-race-cancellable-promises-michael.js](https://gist.github.com/Mahdhir/1f6609ea75f45c07801168c18cdb3812#file-promise-race-cancellable-promises-michael-js)

**长时间执行的批处理**

下面代码出自Chris Jensen，可以保持并行请求的数量固定。

代码如下：

```
const _ = require('lodash')

async function batchRequests(options) {
    let query = { offset: 0, limit: options.limit };

    do {
        batch = await model.findAll(query);
        query.offset += options.limit;

        if (batch.length) {
            const promise = doLongRequestForBatch(batch).then(() => {
                // Once complete, pop this promise from our array
                // so that we know we can add another batch in its place
                _.remove(promises, p => p === promise);
            });
            promises.push(promise);

            // Once we hit our concurrency limit, wait for at least one promise to
            // resolve before continuing to batch off requests
            if (promises.length >= options.concurrentBatches) {
                await Promise.race(promises);
            }
        }
    } while (batch.length);

    // Wait for remaining batches to finish
    return Promise.all(promises);
}

batchRequests({ limit: 100, concurrentBatches: 5 });
```

也可以点击这里访问：[promise-race-batch-requests-chris-js](https://gist.github.com/Mahdhir/94cc9a62dc096086b0de44630921e3d4#file-promise-race-batch-requests-chris-js)

#### 3. Promise.any()

`Promise.any()`适合用在通过不同路径请求同一个资源的需求上。

例如，Vue3.0在unpkg和jsdelivr都有在线的CDN资源，都是国外的CDN，国内直接调用不确定哪个站点会抽风，加载慢，这时候可以两个资源都请求，哪个请求先成功就使用哪一个。

比方说unpkg的地址是：https://unpkg.com/vue@3.0.11/dist/vue.global.js
jsdelivr的地址是：https://cdn.jsdelivr.net/npm/vue@3.0.11/dist/vue.global.js

我们就可以使用下面代码进行请求（使用动态 import 示意）：

```
let startTime = +new Date();
let importUnpkg = import('https://unpkg.com/vue@3.0.11/dist/vue.runtime.esm-browser.js');
let importJsdelivr = import('https://cdn.jsdelivr.net/npm/vue@3.0.11/dist/vue.runtime.esm-browser.js');
Promise.any([importUnpkg, importJsdelivr]).then(vue => {
  console.log('加载完毕，时间是：' + (+new Date() - startTime) + 'ms');
  console.log(vue.version);
});
```

然后就会有下图所示的结果：

![执行结果](https://image.zhangxinxu.com/image/blog/202105/2021-05-07_112535.png)

83毫秒完成，但是实际上，两个JS的请求时间差异是挺大的，结果如下：

![真实请求时间](https://image.zhangxinxu.com/image/blog/202105/2021-05-07_112824.png)

可以看到unpkg的请求时间长了很多 715ms，我测试的时候偶尔还会出现1S以上的请求时长。

不过没关系，有了 `Promise.any()` ，就可以使用最快的那一个。

`Promise.any()`还有一个好处，那就是如果 unpkg 这个网站挂了，也不会影响 Vue 资源的加载，因为一个请求失败了，会继续请求其他的资源，也就是会去请求 jsdelivr 的资源。

这样保证了资源尽可能可用，但是尽可能使用加载最快的资源。

在这种场景下就很实用。

### 四、兼容性、结语没有其他

`Promise.any()`是后出来的规范，因此，兼容性相对滞后一些，如下图所示：

![Promise.any兼容性](https://image.zhangxinxu.com/image/blog/202105/2021-05-07_113527.png)

Safari 14才支持，因此，目前生产环境还不能使用，毕竟我们厂子还有些产品需要兼容iOS 9呢。