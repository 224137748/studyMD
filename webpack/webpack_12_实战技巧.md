## webapck 12 实战技巧

#### 1、库 & 组件的打包

​	我们希望编写的组件、库能适应各种生产环境（`cmd`,`<script>`），借助于`webpack`打包设置来实现：

```js
// webpack.config.js
  

```

####  2、引用的第三方库，不打包

​	**防止**将某些 `import` 的包(package)**打包**到 bundle 中，而是在运行时(runtime)再去从外部获取这些*扩展依赖(external dependencies)*。

```js
// webpack.config.js
module.exports = {
    externals: {
      jquery: 'jQuery'
    }
}
```

查看上面的例子。属性名称是 `jquery`，表示应该排除 `import $ from 'jquery'` 中的 `jquery` 模块。

详细配置参考[externals][1]

[1]: https://www.webpackjs.com/configuration/externals/	"externals"

#### 3、PWA 的打包配置

+ 安装插件`npm install workbox-webpack-plugin --save-dev`

+ 配置`webpack-config.js`:

  ```js
  const WorkboxPlugin = require('workbox-webpack-plugin');
  
  module.exports = {
      plugins: [new WorkboxPlugin.GenerateSw({
        	clientsClaim: true,
          skipWaitting: true
      })]
  }
  ```

  

#### 4、TypeScript 的配置 

+ 安装`loader`插件：`npm install ts-loader typescript --save`

+ 配置`webpack.config.js`:

  ```js
  const path = require('path');
  module.exports = {
      mode: 'prodction',
      entry: './src/index.tsx',
      module: {
          rule: [{
              test: /\.tsx?$/,
              use: 'ts-loader',
              exclude: '/node_modules/'
          }]
      },
      output: {
          filename: 'bundle.js',
          path: path.resolve(__dirname, 'dist')
      }
  }
  ```

+ 在根目录下创建`tsconfig.json`：

  ```json
  {
      "compilerOption": {
          "outDir": "./dist",
          "module": "es6",
          "target": "es5",
          "allowJ s": true
      }
  }
  ```

#### 5、Ts里面引入第三方库

​	在`typescript`里面引入第三方库的时，我们希望在调用库里面的方法时也提供语法校验，此时我们需要在安装第三方库的时候同时要安装对应`typescript`语法校验插件：

```js
npm install lodash @types/lodash --save
```

#### 6、webpackDevServer 实现请求转发

+ 代理请求，下面展示发送请求的例子：

  `index.js`文件中我们通过`get`方法向`www.dell-lee.com`域名下发送一个请求，但是我们当前的域名、端口为`localhost:8080`，此时发送请求会被跨域。

  ```js
  import axios from 'axios';
  import React, { Component } from 'react';
  
  class App extend Component {
      componentDidMount() {
          axios.get('/react/api/header.json').then(res => {
              console.log(res);
          })
      }
  }
  ```

  在` webpack.config.js`文件中配置代理，对于`https`的请求要增加`secure`属性：

  ```js
  module.exports = {
      devServer: {
          contentBase: './dist',
          open: true,
          port: 8080,
          hotOnly: true,
          hot: true,
          proxy: {
              '/react/api': {
                  target: 'http://www.dell-lee.com',
                  // secure: false 对于https请求要配置该项
              },
              changeOrigin: true	
          }
      }
  }
  ```

+ 代理接口：将`header.json` （生产接口）代理到`demo.json`的（测试接口）上。

  ```js
  module.exports = {
      devServer: {
          contentBase: './dist',
          open: true,
          port: 8080,
          hotOnly: true,
          hot: true,
          proxy: {
              '/react/api': {
                  target: 'http://www.dell-lee.com',
                  pathRewrte: {
                      'header.json': 'demo.json'
                  }
              }
          }
      }
  }
  ```

+ 更多详情配置查看[`devServer.proxy`][2]

  [2]: https://www.webpackjs.com/configuration/dev-server/#devserver-proxy	"devServer.proxy"

  

