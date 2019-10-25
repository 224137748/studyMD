## webpack 15 多页面应用的的打包

#### 介绍

​	多页也应用即项目多个入口，打包后输出多个独立的页面，每个页面中只引用自己相关的资源。本文章接下来就要讲述如何配置多页面应用的打包。

#### Src 目录结构

​	项目`src`目录下有三个文件，一个是`index.html`模板文件，另外两个分别是多页应用的入口文件：

```
src
|---index.html
|---index.js
|---list.js
```

#### webpack 配置

​	配置`webpack.config.js`

```js
module.exports = {
    entry: {
        main: './src/index.js',
        list: './src/list.js'
    }
}
```

我们可以看到，项目入口参数中配置两个入口，分别是`main.js`和`list.js`。我们希望的结果是打包成两个页面，然后的每个页面只引用对应的`js`文件。

那么接下来我们就来配置一下`HtmlWebpackPlugin`这个生成`html`的插件。

```js
// 先定义一个config对象 存储webpack的配置参数
let config = {
    // ....
    entry: {
        main: './src/index.js',
        list: './src/list.js'
    }
    // ....
}
// 定义生成plugins的函数
const makePlugins = function(config) {
	const plugins = [];
    Object.keys(config.entry).forEach((item) => {
       plugins.push(new HtmlWebpackPlugin({
           template: ''src/index.html’,
           filename: item + '.html',
           // 配置index.html需要引用的模块
           chunks: ['runtime', 'vendors', item]
       }));
	});
	return plugins;
}
// 定义config的plugins
config.plugins = makePlugins(confg);
module.exports = config'

```

