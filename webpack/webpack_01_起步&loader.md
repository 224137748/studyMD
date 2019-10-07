## webpack_01 - 起步&loader

#### 1、`webpack`搭建环境

+ 安装`nodes.js`
+ `npm init `初始化项目配置文件，生成一个`package.json`文件。

#### 2、安装`webpack`

+ 全局安装，（不推荐，依据不同项目来配置版本号）

  `npm install webpack webpack-cli -g`

+ 项目安装：

  `npm install webpack webpack-cli -D`

+ 安装指定版本的`webpack`版本

  `npm install webpack@4.20.1 webpack-cli -D`

+ `webpack-cli`是开发者能在项目中使用`webpack`指令

#### 3、`webpack`配置文件

+ `webpack`命令行常用指令

  + 在没有配置文件的情况下，指定打包入口，`npx webpack index.js `

  + `webpack`打包默认读取当前项目根目录下`webpack.config.js`作为打包的配置文件，当指定其他文件为打包配置项时，`npx webpack --config webpackSpecial.js`

  + 在`package.json`里面配置`script`，映射`webpack`指令

    ```js
    // packahe.json
    {
        // ...
        "script": {
            "build": "webpack"
        }
    }
    
    // node、cmd 
    npm run build   --->>> npx webpack
    
    
    ```

    

+ `webpack.config.js`依据项目工程配置

  ```js
  const path = require('path');
  module.exports = {
      entry: './index.js',
      output: {
          filename: 'bundle.js',
          path: path.resolve(__dirname, 'dist')
      }
  }
  ```

#### 4、loader

+ **插件执行顺序： 从下到上，从右到左**

+ **图片资源**`：webpack`默认只会打包`js`文件，对其他类型的文件（图片，css样式文件），需要第三方的插件进行加载；

  - `file-loader`

    ```js
    module.exports = {
        // 模块
        module: {
            rules: [{
                test: /\.(png|jpg|gif)$/,
                use: {
                    loader: 'file-loader',
                    option: {
                        // placeholders占位符，修改打包后的文件名
                        name: '[name].[ext]',
                        // 打包图片到指定文件夹
                        outputPath: 'images/'
                    }
                }
            }]
        }
    }
    ```

  + `url-loader`,功能与`file-loader`类似	 

    ```js
    module: {
        rules: [{
            test: /\.(png|jpg|gif)$/,
            use: {
                loader: 'url-loader',
                option: {
                    // placeholders占位符，修改打包后的文件名
                    name: '[name].[ext]',
                    // 打包图片到指定文件夹
                    outputPath: 'images/',
                    // 小于200k的图片将会被压缩
                    limit: 204800
                }
            }
        }]
    }
    ```

+ **`Css样式`**：

  `css-loader`：处理`css`样式文件

  `style-loader`：在`js`文件中引用`css`资源时，将`css`资源挂在到页面中。

  `scss-loader`：对于`css`预处理器的使用。

  `postcss-loader`：对新增的`css3`样式代码，添加浏览器前缀；注意：`postcss-loader`需要单独新增一个配置文件`postcss.config.js`配置所需的插件；

  **示例：**

  ```
  module.exports = {
      module: {
          rules: [{
              test: /\.（css|scss）$/,
              // 插件执行顺序： 从下到上，从右到左
              use: [
              	'style-loader', 
              	'css-loader', 
              	'scss-loader', 
              	'postcss-loader'
              ];
          }]
      }
  }
  ```

+ **`Css-loader`样式补充**

  + 在`.scss`中引用其他`scss`文件，不经过`postcss-loader`、`scss-loader`处理的情况

    ```js
    module: {
        rules: [{
            test: /\.（css|scss）$/,
            use: [
                'style-loader', 
                {
                    loader: 'css-loader',
                    options: {
                    	importLoaders: 2	
                    }
                }, 
                'scss-loader', 
                'postcss-loader'
            ];
        }]
    }
    ```

  + `css`样式模块化，类似于`scoped`区间样式，不会和其他模块的样式重叠和干扰。

    **1、配置：**

    ```js
    module: {
        rules: [{
            test: /\.（css|scss）$/,
            use: [
                'style-loader', 
                {
                    loader: 'css-loader',
                    options: {
                    	importLoaders: 2,
                        modules: true
                    }
                }, 
                'scss-loader', 
                'postcss-loader'
            ];
        }]
    }
    ```

    **2、使用：**

    ```js
    import avatar from './avatar.jpg';
    import style from './index.scss';
    
    var img = new Image();
    img.src = avatar;
    
    // 增加avatar的类名，此时，这个style中的.avatar样式只作用于当前的图片
    img.className.add(style.avatar);
    
    var root = document.getElementById('root');
    root.append(img);	
    ```

  + **`css`字体打包**

    ```
    rules: [{
        test: /\.(ttf|svg|eot)$/,
        use: 'file-loader'
    }]
    ```

    
