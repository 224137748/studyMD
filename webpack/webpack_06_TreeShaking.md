## webpack_06 Tree shaking

#### 介绍

​	当一个`js`文件用`export`方法导出多个函数或者变量时，我们希望`webpack`只打包业务中引入的函数和变量，而不会将整个`js`文件打包。

**注意： `Tree Shaking`只适用于`es6 Module`模块（静态引入），并不适用于`require.js`（动态引入）**

#### 配置

`webpack`在`development`模式下是不会开启`tree shaking`的，生产环境默认开启，因此我们 需要添加以下代码：

```js
modules.export = {
    optimization: {
 		usedExports: true       
    }
}
```

有时我们引用的`js`代码、`css`代码中没有导出任何东西，`import '@babel/poly-fill'`， `tree shaking`有可能将此文件忽略掉因此我们需要同时设置`package.json`文件，来消除上述情况。  

```json 
// package.json
{
    “sideEffects”: ["@babel/poll-fill"] // false
}
```

