## `webpack`学习之路 - 

#### 1、`webpack`搭建环境

+ 安装`nodes.js`
+ `npm init `初始化项目配置文件，生成一个`package.json`文件。

#### 2、安装`webpack`

+ 全局安装，（不推荐，依据不同项目来配置版本号）

  `npm install webpack webpack-cli -g`

+ 项目安装：

  `npm install webpack webpack-cli -D`

+ 安装指定版本的`webpack`版本

  `npm install webpack@4.20.1 webpack-cli -D`

+ `webpack-cli`是开发者能在项目中使用`webpack`指令

#### 3、`webpack`配置文件

+ `webpack`命令行常用指令

  + 在没有配置文件的情况下，指定打包入口，`npx webpack index.js `

  + `webpack`打包默认读取当前项目根目录下`webpack.config.js`作为打包的配置文件，当指定其他文件为打包配置项时，`npx webpack --config webpackSpecial.js`

  + 在`package.json`里面配置`script`，映射`webpack`指令

    ```js
    // packahe.json
    {
        // ...
        "script": {
            "build": "webpack"
        }
    }
    
    // node、cmd 
    npm run build   --->>> npx webpack
    
    
    ```

    

+ `webpack.config.js`依据项目工程配置

  ```js
  const path = require('path');
  module.exports = {
      entry: './index.js',
      output: {
          filename: 'bundle.js',
          path: path.resolve(__dirname, 'dist')
      }
  }
  ```

#### 4、loader

​	`webpack`默认只会打包`js`文件，对其他类型的文件（图片，css样式文件），需要第三方的插件进行加载；

```js
module.exports = {
    // 模块
    module: {
        rules: [{
            test: /\.(png|jpg|gif)$/,
            use: {
                loader: 'file-loader',
                option: {}
            }
        }]
    }
}
```

