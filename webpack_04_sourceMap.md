## webpack_03 sourceMap

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

