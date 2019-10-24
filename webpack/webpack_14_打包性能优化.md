## webpack 14 性能优化

#### 1、跟上技术的迭代（Node、Npm、Yarn）

#### 2、尽可能少的模块上应用`loader`

#### 3、Plugin 尽可能精简并确保可靠

#### 4、resolve 参数合理配置

​	当我们先引入某个文件夹下的文件时，想省略其文件后缀名，如：

```js
// 正常
import Child from './child/child.jsx'

// 报错
import Child from './child/child';
```

此时我们就要配置`resolve`参数：

```js
modle.exports = {
	resolve: {
		extensions: ['.js', 'jsx'],	// 从左往右依次查找
        mainFiles: ['index'],	// 默认查找文件目录下名称为index的文件
        // 配置文件位置映射 将import Child from 'child'映射到下面地址
        alias: {
            child: path.resolve(__dirname, '../src/a/b/c/child')
        }
	}
}
```

通过以上配置最终能查到我们需要的`child/child.jsx`。

#### 5、使用DllPlugin 提高打包速度

​	当我们使用`webpack`打包项目的时，引用的第三方库（如：`react、react-dom、Vue`）在每次都会被`webpack`打包，我们目的是：

1. **第三方库只打包一次，后面几次编译不再打包；**
2. **如果项目需要的第三方库被打包过，则直接引用打包过的资源，否则去`node_modules`中查找库，再进行打包**

##### 配置步骤：

+ 编写`webpack.dll.js`文件，将第三方模块单独打包成一个文件：

  ```js
  const path = require('path');
  const webpack = require('webpack');
  
  module.exports = {
      mode: 'production',
      extry: {
          vendors: ['react', 'react-dom', 'lodash']	// 这里配置要引入的第三方库，打包到vendors名称下
      },
      output: {
          filename: '[name].dll.js',	// 定义第三方库的名称
          path: path.resolve(__dirname, '../dll'),
          library: '[name]'	// 将第三方库挂载到全局变量vendors下
      },
      plugins: {
          // 配置第三方库与项目的映射关系，生成一个manifest.json
          new webpack.DllPlugin({
          	name: '[name]',
          	path: path.resolve(__dirname, '../dll/[name].manifest.json')
      	})
      }
  }
  ```

+ 设置`webpack`打包配置文件，执行`npm run build:dll`进行第三方库的首次打包，

  ```js
  // package.json
  {
      "scripts": {
          "build:dll": "webpack --config ./build/webpack.dll.js"
      }
  }
  
  // 再cmd中打包
  npm run build:dll
  ```

  打包后，会在根目录下生成一个`dll`文件夹，文件夹包含`vendors.dll.js`和 `vebdors.manimest.json`映射文件。

+ **打包后的第三方库并没有被使用，因此我们要在index.html中引用打包后的库**

  + 安装一个可以在`index.html`文件中引入资源的插件：`npm install add-asset-html-webpack-plugin --save`。

  + 配置`webpack.config.js`：

    （1）向index.html中插入引用资源；

    （2）查找文件的映射关系，如果dll文件中有需要的第三方库，则直接引用，否则去node_modules中查找，走打包流程

    ```js
    const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin');
    const path = require('path');
    const webpack = require('webpack');
    module.exports = {
    	// ....
    	plugin: [
            // （1）向index.html中插入引用资源；
            new AddAssetHtmlWebpackPlugin({
                filePath: path.resolve(__dirname, '../dll/vendors.dll.js')
            }),
            // (2)查找文件的映射关系，如果dll文件中有需要的第三方库，则直接引用，否则去node_modules中查找，走打包流程
            new webpack.DllReferencePlugin({
                manifest: path.resolve(__dirname, '../dll/vendors.manifest.json')
            })
        ]	
    	// ...
    }
    ```

     

+ **========优化=========优化===========优化===========优化======**

  上面的示例`entry`中只有一个入口，

  ```js
  module.exports = {
      entry: {
          vendors: ['react', 'react-dom', 'lodash']
      }
  }
  ```

  如果有多个入口怎么操作？想看看下面这种情况：

  ```js
  
  // webpack.dll.js 中配置多个入口
  module.exports = {
      entry: {
          vendors: ['lodash'],
          react: ['react', 'react-dom'],
          jquery: ['jquery']
      }，
      output: {
          filename: '[name].dll.js',	// 定义第三方库的名称
          path: path.resolve(__dirname, '../dll'),
          library: '[name]'	// 将第三方库挂载到全局变量vendors下
      },
  }
  ```

  执行以下命令：

  ```bash
  npm run build:dll
  ```

  会在`dll`文件夹中生成`vendors.dll.js`、`react.dll.js`、`jquery.dll.js`、`vendors.manifest.json`、`react.manifest.json`、`jquery.manifest.json`等文件，然后我们还要到`webpack.config.js`中将生成的文件注入到`index.html`中，并且配置依赖关系：

  ```js
  module.epxorts = {
      plugins: [
          new AddAssetHtmlWebpackPlugin({
              // ...vendors.dill.js
          })，
          new webpack.DllReferencePlugin({
          	// ...vendors.manifest.json
          }),
          new AddAssetHtmlWebpackPlugin({
              // ...react.dill.js
          })，
          new webpack.DllReferencePlugin({
          	// ...react.manifest.json
          }),
          new AddAssetHtmlWebpackPlugin({
              // ...jquery.dill.js
          })，
          new webpack.DllReferencePlugin({
          	// ...jquery.manifest.json
          })
      ]
  }
  ```

  ok，看到这里我想大家应该都知道了这样显然很不优雅，每新增一个入口文件，就要手动到`webpack.config.js`中配置两个插件，所以我们希望`node`帮助我们自动的添加插件。

  **解决办法：**

  ```js
  const fs = require('fs');
  const path = require('path');
  // files 为dll文件夹下面的所有文件的名称组成的数组
  const plugins = [];
  const files = fs.readdirSync(path.resolve(__dirname, '../dll'));
  files.forEach((file) => {
      if (/.*\.dll.js/.test(file)) {
          plugins.push(new AddAssetHtmlWebpackPlugin({
              filepath: path.resolve(__dirname, '../dll', file)
          }))
      }
      if (/.*\.manifest.json/.test(file)) {
          plugins.push(new webpack.DllReferencePlugin({
              manifest: path.resolve(__dirname, '../dll', file)
          }))
      }
  })
  ```

#### 6、控制包文件大小

#### 7、thread-loader,parallel-webpack,happaypack进行多进程打包

#### 8、合理使用 sourceMap

#### 9、结合 stats 分析打包结果

#### 10、开发环境无用插件剔除