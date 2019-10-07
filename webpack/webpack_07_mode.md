## webpack 07 模式

####  介绍

​	根据项目需求通常分为`development`模式 和 `production`模式，他们主要有一下区别：

 +	`sourceMap`在开发者模式下，能检索出具体错误，在生产模式下没有开发者模式下全面。
 +	编译，在开发者模式中代码不进行压缩，生产模式下代码会压缩减小体积。

#### 开发 & 生产模式切换

​	通常情况下将开发者模式和生产者模式分开配置，例如：`webpack.dev.js`和`webpack.pro.js`;在`package.json`文件中配置启动项：

`package.json`配置

```json
{
	"name": "demo",
    "version": "1.0.1",
    "main": "index.js",
    "script": {
        "dev": "webpack-dev-server --config webpack.dev.js",
        "build": "webpack --config webpack.pro.js"
    }
}
```

​	开发者模式 和 生产者模式通常都会有相同的配置参数，比如`entry`、`output`、`module`等，我们可以将两个模式共同的部分提取出来，单独创建一个文件进行管理`webpack.common.js`。再使用`webpack-merge`插件，将公用的配置和当前环境的配置进行合并。

```js
// webpack.dev.js
const webpack = require('webpack');
const merge = require('webpack-merge');
const commonConfig = require('./webpack.common.js');
// 开发者环境的配置项
const devConfig = {
    mode: 'development',
    devtool: 'cheap-module-eval-source-map',
    derServer: {
        contentBase: './dist',
        open: true,
        port: 3000,
        hot: true
    },
    plugins: [new webpack.HotModuleReplacementPlugin()],
    // 只打包 使用的 函数
    optimization: {
        usedExports: true
    }
}

// 进行配置合并
module.exports = merge(commonConifg, devConfig);
```

