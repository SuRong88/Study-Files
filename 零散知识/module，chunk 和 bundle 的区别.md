# [webpack 中，module，chunk 和 bundle 的区别是什么？](https://www.cnblogs.com/skychx/p/webpack-module-chunk-bundle.html)

Webpack 有非常多的概念，很多名词长得都差不多。我把这些分散在文档和教程里的内容总结起来，写了一份 **webpack 中的易混淆知识点**，目前看是**全网独一份**，大家可以**加个收藏**，方便以后检索和学习。

全集链接 ➡️ [webpack 易混淆知识点](https://www.cnblogs.com/skychx/tag/Webpack/)

前两天为了优化公司的代码打包项目，恶补了很多 webpack4 的知识。要是放在几年前让我学习 webpack 我肯定是拒绝的，之前看过 webpack 的旧文档，比我们内部项目的文档还要简陋。

但是最近看了一下 webpack4 的文档，发现 webpack官网的 [指南](https://webpack.docschina.org/guides/) 写的还不错，跟着这份指南学会 webpack4 基础配置完全不是问题，想系统学习 webpack 的朋友可以看一下。



```
友情提示：本文章不是入门教程，不会费大量笔墨去描写 webpack 的基础配置，请读者配合教程[源代码](https://github.com/skychx/webpack_learn/tree/master/confuse)食用。
```



说实话我刚开始看 webpack 文档的时候，对这 3 个名词云里雾里的，感觉他们都在说打包文件，但是一会儿 chunk 一会儿 bundle 的，逐渐就迷失在细节里了，所以我们要跳出来，从宏观的角度来看这几个名词。

webpack 官网对 chunk 和 bundle 做出了[解释](https://webpack.docschina.org/glossary)，说实话太抽象了，我这里举个例子，给大家**形象化**的解释一下。

首先我们在 src 目录下写我们的业务代码，引入 index.js、utils.js、common.js 和 index.css 这 4 个文件，目录结构如下：

```bash
src/
├── index.css
├── index.html # 这个是 HTML 模板代码
├── index.js
├── common.js
└── utils.js
```

index.css 写一点儿简单的样式：

```css
body {
    background-color: red;
}
```

utils.js 文件写个求平方的工具函数：

```javascript
export function square(x) {
    return x * x;
}
```

common.js 文件写个 log 工具函数：

```javascript
module.exports = {
  log: (msg) => {
    console.log('hello ', msg)
  }
}
```

index.js 文件做一些简单的修改，引入 css 文件和 common.js：

```javascript
import './index.css';
const { log } = require('./common');

log('webpack');
```

webpack 的配置如下：

```javascript
{
    entry: {
        index: "../src/index.js",
        utils: '../src/utils.js',
    },
    output: {
        filename: "[name].bundle.js", // 输出 index.js 和 utils.js
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    MiniCssExtractPlugin.loader, // 创建一个 link 标签
                    'css-loader', // css-loader 负责解析 CSS 代码, 处理 CSS 中的依赖
                ],
            },
        ]
    }
    plugins: [
        // 用 MiniCssExtractPlugin 抽离出 css 文件，以 link 标签的形式引入样式文件
        new MiniCssExtractPlugin({
            filename: 'index.bundle.css' // 输出的 css 文件名为 index.css
        }),
    ]
}
```

我们运行一下 webpack，看一下打包的结果：

![img](http://ww3.sinaimg.cn/large/006tNc79ly1g3g9kn79ntj30o6060ab8.jpg)

我们可以看出，index.css 和 common.js 在 index.js 中被引入，打包生成的 index.bundle.css 和 index.bundle.js 都属于 chunk 0，utils.js 因为是独立打包的，它生成的 utils.bundle.js 属于 chunk 1。

感觉还有些绕？我做了一张图，你肯定一看就懂：

![image-20200518210532171](https://image-1255652541.cos.ap-shanghai.myqcloud.com/uPic/image-20200518210532171.png)

看这个图就很明白了：

1. 对于一份同逻辑的代码，当我们手写下一个一个的文件，它们无论是 ESM 还是 commonJS 或是 AMD，他们都是 **module** ；
2. 当我们写的 module 源文件传到 webpack 进行打包时，webpack 会根据文件引用关系生成 **chunk** 文件，webpack 会对这个 chunk 文件进行一些操作；
3. webpack 处理好 chunk 文件后，最后会输出 **bundle** 文件，这个 bundle 文件包含了经过加载和编译的最终源文件，所以它可以直接在浏览器中运行。

一般来说一个 chunk 对应一个 bundle，比如上图中的 `utils.js -> chunks 1 -> utils.bundle.js`；但也有例外，比如说上图中，我就用 `MiniCssExtractPlugin` 从 chunks 0 中抽离出了 `index.bundle.css` 文件。



### 1.1 一句话总结：

`module`，`chunk` 和 `bundle` 其实就是同一份逻辑代码在不同转换场景下的取了三个名字：

我们直接写出来的是 module，webpack 处理时是 chunk，最后生成浏览器可以直接运行的 bundle。

##  



最后推荐一下我的个人公众号：「卤蛋实验室」，平时会分享一些前端技术和数据分析的内容，大家感兴趣的话可以关注一波：
![img](https://image-1255652541.cos.ap-shanghai.myqcloud.com/uPic/20190709220018.png)https://news.cnblogs.com/)