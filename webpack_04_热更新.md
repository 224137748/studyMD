## webpack_04 热更新

#### 介绍

​	模块热替换(`HMR - Hot Module Replacement`)功能会在应用程序运行过程中替换、添加或删除[模块](https://www.webpackjs.com/concepts/modules/)，而无需重新加载整个页面。主要是通过以下几种方式，来显著加快开发速度：

- **保留在完全重新加载页面时丢失的应用程序状态**。
- **只更新变更内容，以节省宝贵的开发时间。**
- **调整样式更加快速 - 几乎相当于在浏览器调试器中更改样式。**

#### 启用HMR

启用此功能实际上相当简单。而我们要做的，就是更新 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的配置，和使用 `webpack` 内置的 `HMR `插件。

```js
const webpack = require('webpack');

module.exports = {
    extry: {
        main: './src/index.js'
    },
    devtool: 'inline-source-map',
    devServer: {
        contentBase: './dist',
        // 启动服务后打开浏览器
        open: true,
        // 设置端口
        port: 8080,
        // 开启热更新，代码变更后不刷新网页
        hot: true,
        hotOnly: true
    },
    // 配置热更新插件
    plugins: [new webpack.HotModuleReplacementPlugin()]
}
```

**注意： 以上配置重启之后，`css样式`发生变更会直接在浏览器中生效，因为`css-loader`在背后重新做了处理，但是`js`就不行，我们还需做以下操作：**

```js
if (module.hot) {
    module.hot.accept('./number', () => {
        // 热更新后的回调操作...
        removePrevInit();
        init();
        number();
    })
}
```

`vue`中之所以没有看见这样的代码，是因为`vue-loader`在背后做了处理。

