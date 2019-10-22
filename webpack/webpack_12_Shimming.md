## webpack 12 Shimming垫片

#### 介绍

​	有时我们引用一些第三方的`js`库，需要依赖类似`jquery、lodash`这种基础库，

```js
export function ui() {
    $('body').css('background', _join('res'));
}
```

上面代码依赖`$`和`_`两个基础库，要使得代码运行正常，必须得在这个第三方库的最前面添加：

```js
import $ from 'jquery';
import _ from 'lodash';

export function ui() {
    $('body').css('background', _join('res'));
}
```

#### 使用webpack插件优化

```js
// 引入webpack插件
const webpack = require('webpack');

module.exports = {
    // ...
    plugins: [
        new webpackProvidePlugin({
            // 当webpack发现模块中使用$时，会自动引入jquery
            $: 'jquery',
            _: 'lodash'
        })
    ]
    // ...
}
```

#### 模块中的this

​	我们来看下面的代码，在`webpack`中入口`index,js`文件`this`指向问题：

```js
// index.js
console.log(this);
console.log(this === window);

// 打印
// Object
// false
```

实际上`this`在这里指向模块自身。

#### 如何将模块中的this实现window?

1. 安装`imports-loader`;

2. 配置`webpack`：

   ```js
   module.exports = {
       entry: './src/index.js',
       module: {
           rule: [{
               test: /\.js$/,
               exclude: /node_module/,
               // 配置imports-loader, 执行顺序从下往上
               use: [{
                   loader: 'babek-loader'
               }, {
                   loader: 'imports-loader?this=window'
               }]
           }]
       }
   }
   ```

   