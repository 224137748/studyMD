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

