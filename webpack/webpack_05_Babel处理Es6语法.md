## webpack 05 Babel

#### 介绍

`Babel`就是 将`Es6+`语法转译成`Es5`的工具。

#### 安装

```js
npm install --save-dev babel-loader @babel/core
// 详细babel配置,es6 -> es5转换
npm install --save-dev @babel/preset-env



```

#### 使用

在`webpack`中配置

```
module: {
    rules: [
        {
        	test: /\.js$/,
        	exclude: /node_modules/,
        	loader: 'babel-loader',
        	option: {
        		presets: ['@babel/preset-env']
        	}
        }
    ]
}
```

#### 低版本兼容，语法polyfill写法

1、安装`@babel/polyfill`插件，并且在所需兼容的`JavaScript`代码中引入`polyfill`。但是这样做会让编译后的代码变得很大，下面接着讲到按需编译。

```js
npm install --save-dev @babel/polyfill
// polyfill的使用
// 在JavaScript脚本之前引用

// Javascript
import "@babel/polyfill";
const arr = [new Promise(() => {}), new Promise(() => {})];

arr.map(item => {
    console.log(item);
});
// ....

```

2、配置`webpack.config.js`

```js
modulesL {
    rules: [{
        test: /\.js$/,
        exclude: /node_modules/,
        loader: ['babel-loader'],
        // 配置转译参数，只将代码按需转译，不用将全部es6语法转译成es5
        options: [['@babel/preset-env', {
            useBuiltIns: 'usage'
        }]]
    }]
}
```

#### 按条件编译

如果我们所开发的是在`chrome`浏览器（67版本）上运行的web项目。那么可以这样配置：

```js
modulesL {
    rules: [{
        test: /\.js$/,
        exclude: /node_modules/,
        loader: ['babel-loader'],
        // 配置转译参数，只将代码按需转译，不用将全部es6语法转译成es5
        options: [['@babel/preset-env', {
            targets: {
              // 浏览器版本
                chrome: '67'
            },
            useBuiltIns: 'usage'
        }]]
    }]
}
```

`webpack`会检测`chrome`浏览器版本是否需要将`es6+`语法转译，如果当前版本支持`es6`语法，则不用转译，以减少代码大小。

#### 类库形式编译

有的时候我们开发一些开源的库，避免全局污染，从而需要编译成像闭包形式的代码。

1、安装

```js
npm install --save-dev @babel/plugin-transform-runtime

npm install --save-dev @babel/runtime
```

2、配置`webpack`

```js
module: {
    rules: [{
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
            plugins: [['@babel/plugin-transoform-runtime', {
                corejs: 2,
                helpers: true,
                regenerrator: true,
                useEsModules: false
            }]]
        }
    }]
}
```

#### .babelrc

如果`webpack`中`babel`配置项太多，可以新建一个`.babelrc`文件，单独配置。

```json
// .babelrc
{
    "plugins": [["@babel/plugin-transform-runtime", {
        "corejs": 2,
        "helpers": true,
        "regenerator": true,
        "useESModules": false
    }]]   
}
```

