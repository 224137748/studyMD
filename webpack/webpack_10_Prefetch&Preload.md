---
typora-root-url: ..\image
---

## webpack 10 Prefetch 和Preloading

#### 介绍

​	在前面学习`code Splitting`代码分割的时候，有提到`splitChunks`的配置项：

```js
module.export = {
    optimization: {
        splitChunks: {
            chunks: 'async' // 默认值： async
        }
    }
}
```

​	试想一下，为什么`webpack`默认异步加载的代码才进行代码分割？同步加载的代码比如`jquery、lodash`等库为什么不进行代码分割。

​	实际上`webpack`认为，只有增大网页的代码利用率，减少不必要的代码，才能更快的显示 web 首页。下面展示测试web首页加载的性能情况，我们在浏览器中输入网址`http://y.migu.cn/app/v3/lite/#/vrbt` , 在`chrome`浏览器中按快捷键`ctrl + shift + p`，在命令行输入`cover`，选择`Show Coverage`这一选项，然后点击小圆圈，出现下图：![coverage](/F:/code/studyMD/image/coverage.png)

从上图可见，首页`index.js`的利用率只有`52%`，左下角显示**`592kb of 1.0 MB bytes are not used。（57%）`**。可见这个项目利用率是偏低的。

#### 如何优化代码

​	`webpack`希望我们将一些交互的逻辑改成异步加载的形式，我们以下面代码为例：

```js
// 优化前
// index.js
document.addEventListener('click', () => {
    let ele = document.createElement('div');
    ele.innerHTML = 'Hello world~!';
    document.body.appendChild(ele);
});
```

上述代码在首页加载后，只有第一行和最后一行被使用，中间`click`事件相关逻辑在未点击之前都是没有被利用的，因此我们将代码优化一下，

```js
// click.js
function createElement() {
    let ele = document.createElement('div');
    ele.innerHTML = 'Hello world~!';
    document.body.appendChild(ele);
}
export default createElement;
```

在`index.js`中引入点击事件的逻辑：

```js
// 优化后
// index.js
document.addEventListener('click', () => {
    import ('./click.js').then(({default: func}) => {
       func(); 
    });
});
```

这样就增加了`index.js`代码的利用率。在实际开发中，我们可以将用户的登陆逻辑做成这种异步加载的形式，当用户点击登录按钮，就加载对应的模块代码，执行相关逻辑；

#### 有一个疑问？

​	当用户点击了某个按钮，触发事件，再去加载模块执行`js`逻辑是否会产卡顿，响应慢的问题？怎么解决？

#### **`Preload    `**与**` Prefetch`**

​	当web首页加载完必须的模块代码后，这时带宽已经释放出来了，我们希望涉及到用户的交互的代码会在这个时候自动下载，既满足了首页加载迅速，又满足了提升代码利用率的前提下提高异步逻辑响应效率。这就要依赖`webpack`中`Preload`和`Prefetch`的配置。

与`Prefetch`相比，`Preload`指令有很多区别：

- 预加载的块开始并行于父块加载。父块完成加载后，预取的块开始。
- 预加载的块具有中等优先级，可以立即下载。浏览器空闲时，将下载预提取的块。
- 父块应立即请求预加载的块。预取的块可以在将来的任何时候使用。
- 浏览器支持不同。

配置`Prefetch`如下：

```js
// index.js
document.addEventListener('click', () => {
    import (/* webpackPrefetch: true */'./click.js').then(({default: func}) => {
       func(); 
    });
});
```

配置`Preloading`如下：

```js
// index.js
document.addEventListener('click', () => {
    import (/* webpackPreload: true */'./click.js').then(({default: func}) => {
       func(); 
    });
});
```

