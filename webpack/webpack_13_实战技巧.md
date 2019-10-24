## webapck 13 实战技巧

#### 1、库 & 组件的打包

​	我们希望编写的组件、库能适应各种生产环境（`cmd`,`<script>`），借助于`webpack`打包设置来实现：

```js
// webpack.config.js
const path = require('path');
module.exports = {
    output: {
        path: path.resolve(__dirname: 'dist'),
        filename: 'library.js',
        library: 'root',	// 将该库挂在到全局变量root上
        libraryTarget: 'umd'	// 将该库封装成适用AMD CMD 各个平台
    }
}

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

#### 7、单页应用 & 非history模式下的路由问题

​	在`SPA`页面中中，常常使用到路由，有的框架默认使用路由采用的时`history`模式，这种模式通常叫做`hash`路由，以`#`号加上路由路径表示，例如：`/app/v3/lite/#/vrbt`、`/app/v3/lite/#/crbt`等；

​	这种路由模式以`#`号加上路由路径这种表现形式不太美观，我们想取消掉中间的`#`号，在地址栏输入`/app/v3/lite/vrbt `，就跳转到我们希望看到的页面。但是实际上浏览器会报错，提示网页未找到，那是因为浏览器会到服务器中的路径下寻找资源，服务器中根本没有这个文件，对此我们还需要在`webpack.config.js`中做以下配置：

```js
// webpack.config.js
module.exports = {
    devServer: {
        historyApiFallback: true	
        //等同于
        // historyApiFallback: {
        //    rewrites: [{
        //    	form: /\.*/,   将所有路径映射到当前根目录下的index.html
        //		to: '/index.html'
        //	}]
    	// }
    }
}
```

**以上配置仅仅只作为本地开发有效，线上还需要后端配置`apach`设置映射关系，所以还是推荐使用默认的history模式**

#### 8、webpack 配置Eslint

​	有同学会有疑问，为什么要在`webpack`中配置`eslint`呢？经常使用的代码编辑器中就有`eslint`插件(sublime无法安装此插件)，使用起来也很方便，但如果是团队协同开发的项目，想统一`eslint`标准，以及代码风格，岂不是每个开发人员的`eslint`配置项都要设置成一致？所以`webpack`中配置`eslint`就能有效的解决这个问题。

+ 安装`eslint`：

  ```bash
  npm install eslint --save-dev
  ```

+ 在项目文件夹下本地检测：

  ```bash
  // 检测src目录下的文件，此方法开发效率较慢
  npx eslint src
  ```

+ 在开发中使用，配置`devServer`和`js-loader`等参数。

  ```js
  // 配置webpack.config.js
  module.exports = {
      devServer: {
          overlay: true,	// 开发时，在浏览器实时展示eslint错误信息
          contentBase: './dist',
          open: true,
          port: 8080,
          hot: true,
          hotOnly: true
      }
      module: {
          rules: [{
              test: /\.js$/,
              exclude: /node_modules/,
              use: ['babel-loader', 'eslint-loader']	// js文件先经过eslint-loader检查，在经过babel转译
          }]
      }
  };
  ```

  

