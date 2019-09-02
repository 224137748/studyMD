## 多页应用

+ 核心思想：其实就是两个Vue项目，一次webpack打包，关联用url联系

  - webpack操作:
    1. 多个入口 `{main1:'./usermain.js',main2:'./goodsmain.js'  }`
    2. 多个html插件

+ 注意事项

  - ![](E:\code\studyMD\image\1530612674237.png)

+ `getHtmls`的思路

  - 更为灵活的读取各项目下的`js`文件（入口）`entry:{‘js文件名’:'js文件路径' } `

  ```js
  plugins:[].concat([
  	new HtmlWebpackPlugin(),
  	new HtmlWebpackPlugin()
  ])
  ```

  + `filename`属性是生成的相对dist的文件名  `xxx.html`
  + `template` 模板生成的参照物  需要绝对路径||相对路径 `'./xxx.html'`
  + `chunks:[filename] `  指定第三引入的`js`文件名称
  + ![1530629409502](E:\code\studyMD\image\1530629409502.png)
  + 这个`[name]`其实就是`entry`的`key`

+ 部署：解决首页`开发环境scripts的dev配置--open-page xxx.html 生产环境配置 server节点`

+ ```js
  // 将dist文件夹放到 ngx里面 配置ngx代理
  server {
      listen       9527;
      server_name  localhost;
  
      location / {
          root   html;
          index  user.index.html user.index.htm; // user为打包后的子页面名称
      }
  }
  ```

#### 处理前后端分离带来的跨域问题

+ 开发环境 `webpack.config.js(**dev**)`

+ ```js
  proxy: {
        '/api': {
          target: 'https://list.jd.com/list.html?cat=1318,1462,1480&callback=jQuery942326&md=9&l=jdlist&go=0',
          pathRewrite: {'^/api' : ''}, // 将‘api’替换为空
          changeOrigin:true, //必须要加，否则是访问自己的环境
        	}
     }
  ```

+ 生产环境`nginx`配置后端请求代理

+ ```
  server {
      listen 8090;
      location / {
          root html;
          index index.html index.htm;
      }
      location /api {
          # 这里也可以录用原有参数，进行服用，并重写Url
           proxy_pass https://list.jd.com/list.html?cat=1318,1462,1480&callback=jQuery942326&md=9&l=jdlist&go=0;
      }
  }
  ```

#### 预渲染

+ 单页应用.. 首先加载 `index.html`
+ ![](E:\code\studyMD\image\1537023673375.png)
+ 再单页中预先渲染部分，静态数据，不变化的页面，比如： “关于我”

