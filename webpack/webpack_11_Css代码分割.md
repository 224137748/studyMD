## webpack 11 Css代码分割

#### 背景知识

​	`webpack`打包入口文件时，配置文件名：

```js
module.exports = {
    entry: {
        main: './src/index.js'
    },
    output: {
        filename: '[name].js',	// 配置入口（entry）下文件打包后的名称
        chunkFilename: '[name].chunk.js'	// 除entry入口外，配置间接引入的插件、库打包后的文件名称
    }
}
```

#### Minimal 插件

​	`webpack`打包会将`css`文件打包到js中，使得js文件比较庞大，借助于`Minimal`插件就可以将`css`文件单独打包成独立的样式文件，**但是`Minimal`插件并不支持热更新，因此我们只在生产环境配置改插件。**

```js
// webpack.pro.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const merge = require('webpack-merge');
const commonConfig = require('./webpack.common.js');

const prodConfig = {
    mode: 'production',
    devtool: 'cheap-module-source-map',
    //重新配置style-loader
    module: {
        rules: [{
      		test: /\.scss$/,
            use: [MiniCssExtractPlugin.loader, {
                loader: 'css=loader',
                options: {
                    importloaders: 2
                }
            }, 'sass-loader', 'postcss-loader']
        }, {
            test: /\.css$/,
            use: [MiniCssExtractPlugin.loader, 'css-loader', ''postcss-loader']
        }]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: '[name].css',
            chunkFilename: '[name].chunk.css'
        })
    ]
}
```

但是配置以上参数打包后，`dist`目录下并没有找到`css`文件，那是因为`webpack`在配置`Tree shaking`（只打包引用的资源）的情况下，认为引入的`css`文件没有被使用，  因此我还需配置`package.json`让`.css`文件不适用`tree shaking`条件。

```json
{
    "name": "lesson",
    // 配置sideEffects,css文件不适用tree shaking
    "seideEffects": [".css"],
    "version": "1.0.0"
}
```

#### optimizeCssAssetsPlugin 代码压缩插件

```js
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
module.exports = {
    optimization: {
        minimizer: [new OptimizeCssAssetsPlugin({})]
    }
}
```

#### 其他（多个入口的情况）

+ 如果想把所有的`css`文件打包到一个文件下，查看官方文档，`splitChunks`
+ 如果想把每个入口的`css`文件打包到对应的目录下，也参考官方文档，同上