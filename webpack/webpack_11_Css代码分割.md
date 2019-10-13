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

