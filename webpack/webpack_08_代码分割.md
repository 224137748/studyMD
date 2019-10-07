## webpack 08 code splitting

#### 介绍

​	在项目中通常需要将业务代码和引用资源库代码进行分开打包，这样的好处是：

+ 可以减少单个资源文件体积，缩短网页请求`js`文件的响应时间；
+ 当业务发生变更时，只需维护业务代码，项目闪现后用户只加载最新的业务代码即可，资源代码库会缓存在用户的客户端内 。

#### 配置webpack实现代码分割

在`webpack.config.js`中新增以下配置：

```js
module.export = {
    optimization: {
        splitChunks: {
            chunks: 'all'
        }
    }
}
```

+ 同步代码，引用类库后，配置以上参数即可生效。
+ 异步代码，无需做任何配置，`webpack`会自动进行代码分割。 

#### 修改异步组件打包后的名字

1、 在异步组件中使用魔法注释：

```js
// index.js
function getComponent() {
    // 引入时定义打包后代码块名字
    return import(/* webpackChunkName: 'lodash' */ 'lodash').then({default: _} => {
        var element = document.createElement('div');
        element.innerHTML = _.join('lee', 'Sen', '-');
        return element;
    })
}

getComponent().then( ele => {
    document.body.appendChild(ele);
});
```

2、在`webpack.config.js`中配置

```js
module.exports = {
    optimization: {
        splitChunks: {
            chunks: 'all',
            cacheGroups: {
                vendors: false,
                default: false
            }
        }
    }
}
```



#### SplitChunksPlugin配置参数详解

1、`splitChunks`默认配置如下

```js
module.exports = {
    optimization: {
        splitChunks: {
            chunks: 'async', // async 只针对异步加载做代码分割  all 对异和同步都可以执行代码分割
            minSize: 30000, // 设置要执行代码分割的最小体积——大于30kb的模块则进行代码分割
            maxSize: 0,	// 设置执行代码分割后的js模块最大体积——代码分割后的体积最大不超过50kb
            minChunks: 1,	// 一个模块被复用多少次后才被进行代码分割
            maxAsyncRequsest: 5,	// 同时异步加载的模块数
            maxInitialRequests: 3, //  首页代码分割请求数  
            automaticNameDelimiter: '~', // 文件名连接符
            name: true,
            // 缓存组： 要执行代码分割的部分都将执行到这个配置
            cacheGroups: {
                // Vendors: false,
                // defauly: false
                // 如果引用的库与下面的匹配规则相符，则打包到vendors.js文件中
                vendors: {
                    test: /[\\/]/node_modules[\\/]/,
                    // 权重，值越大优先打包到此组
                    priority: -10,
                    filename: 'vendors.js'
                },
                // 代码分割的库如果不匹配上面的vendor规则，则默认下面的规则,则打包到common.js文件中
                default: {
                 	priority: -20,
                 	reuseExistingChunk: true,	// 如果一个模块已经被打包过了，再次引用的时候不会被打包
                    filename: 'common.js'
                }
            }
        }
    }
}
```

