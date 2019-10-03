## `webpack`_02  plugins

#### 插件

​	webpack 有着丰富的插件接口(rich plugin interface)。webpack 自身的多数功能都使用这个插件接口。这个插件接口使 webpack 变得**极其灵活**。

#### 1、`HtmlWebpackPlugin`

+ 介绍：使用`webpack`打包的资源文件名中包含随着编译而发生变化的哈希，每次需要在模板中修改引用的文件名，有了这个插件，就能生成一个HTML文件，自动引入资源。

+ 安装

  ```js
  npm install --save-dev html-webpack-plugin
  ```

+ 使用

  ```js
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  module.exports = {
      pligins: [new HtmlWebpackPlugin({
         // 配置模板
          template: 'src/index.html'
      })]
  }
  ```

#### 2、`clean-webpack-plugin`

+ 介绍：每次编译都会清空上一次编译 `dist`(output.path)目录中的内容。

+ 安装

  ```js
  npm install clean-webpack-plugin -D
  ```

+ 使用

  ```
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const path = require('path');
  const HtmlWebpackPlugin = require(html-webpack-plugin);
  // webpack.config.js
  module.exports: {
  	plugins: [new HtmlWbepackPlugin({
          // ...
  	}), new CleanWebpackPlugin(['dist'])],
  	output: {
          filename: '[name]_[hash].js',
          path: path.resolve(__dirname, 'dist')
  	}
  }
  
  ```

  