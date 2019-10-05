## webpack_03 entry & output

#### 1、默认配置

+ `entry`值为字符串时：

  ```js
  module.exports = {
      entry: './src/index.js'
      // 以上写法与下面打包等同
      // {
      //    main: './src/index.html'
      // }
  }
  ```

+ `output`中的属性`filename`不配置时，默认为`main.js`。

  ```js
  module.exports = {
      output: {
          // 不配置filename与下面打包效果等同
          // filename: 'main.js'
      }
  }
  ```

#### 2、多入口打包

+ `extry`配置多入口

  ```js
  module.exports = {
      extry: {
          main: './src/index.js',
          sub: './src/sub.js'
      }
  }
  ```

+ `output`配置打包后的文件

  ```js
  module.exports = {
      output: {
      	// 使用占位符
          filename: '[name].js',
          path: path.resolve(__dirname, 'dist')
      }
  }
  ```

#### 3、配置静态资源发布地址

```
module.exports = {
    output: {
        publicPath: 'http://cdn.com.cn',
        filename: 'bundle.js'
    }
}
```

## webpack_04  sourceMap

#### 介绍

​	`sourceMap`是映射代码编译前后的关系，比如：`dist`目录下`main.js`文件第96行代码出错了，实际上是`src`目录下`index.js`文件的第 ** 行出错了，方便开发者找到错误；

#### 如何配置？

​	当前环境如果为开发者模式时，通常将`devtool`属性设置为`cheap-module-eval-source-map`，因为此时打包速度最快且查找错误最方便；当前环境如果为生产环境时，`devtool`属性值设置为`cheap-module-source-map`为宜。

```
module.exports = {
	// 开发者环境
    mode: 'development',
    devtool: 'source-map'
}
```

