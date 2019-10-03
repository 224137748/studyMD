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

