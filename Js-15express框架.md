## express

- 基于 Node.js 平台，快速、开放极简的 web 开发框架。

### 使用步骤

- 1、安装 `$ npm install express --save`
- 2、引入 express 第三方框架
- 3、开启服务监听端口
- 4、处理相应
- 5、在 express 中，保留了原生 http 的相关属性和函数

### app.use('虚拟目录', fn)

- 用户/abc/def 的请求
  - 选择性调用 `app.sue('/abc', fn)` 的中间件
  - 但是内部`req.url`则去除了`/abc`这个暗号
- `app.use('',fn)`是任何请求都会出发执行的 **相当于请求拦截**

```javascript
// 引入模块
const express = require("express");
// 实例化一个服务
let server = express();
// 定义后端路由
let router = express.Router();

// 全局
server.use("", (req, res, next) => {
  console.log("接受请求了");
  next();
});

// 定义接口,请求方式，虚拟路径
router.get("/login", (req, res) => {
  res.end("login");
});

router.post("/register", (req, res, next) => {
  res.end("register");
});

// 监听端口
server.linsten(8080, () => {
  console.log("请用浏览器打开：http://loclahost:8080");
});
```

### 中间件类别

- 应用级中间件 `app.use(fn)`
- 路由级中间件
  - 1、获取路径中间件
  - 2、配置路由
  - 3、加入到应用程序控制中 `app.use(router)`
- 内置中间件
  - 处理一些静态资源文件的返回（设置将某个目录下的资源文件向外暴露）
    - 当 url 匹配上我设置的目录下的文件后，自动返回该文件 `/assets/imgs/love.png`
    - 加入到应用程序控制中 `app.use(内置中间件)`；
- 第三方中间件
  - 更方便的处理`cookie/session`，简易的解析 post 请求数据
  - 在 npm 上下载并使用
  - 加入到应用程序控制中`app.use(第三方中间件)`
- 错误处理中间件
  - 在 express 中统一处理错误

### 路由中间件

- 一个请求进来（总网线），分发到各自不同的处理（分多跟网线给其他人）
  - **分流**
- 后端路由
  - （请求方式 + URL = 判断依据）（分流的判断依据）-> 做不同的处理（分流后的行为）
- 使用步骤

  - 1、获取路由中间件对象`let router = express.Router();`
  - 2、配置路由规则,请求方式，相应

  ```javascript
  router.get("/login", (req, res) => {
    res.json({
      code: 200,
      data: {
        name: "zhangsan",
        age: 18
      },
      info: "success"
    });
  });

  // post 请求
  router.post("/register", (req, res) => {
    res.end("register");
  });
  ```

  - 将 router 加入到应用中 `app.use(router);`

### res 扩展函数

```javascript
// 下载文件
res.download("./XXX.txt");

// 响应json对象
res.json({});

// 配合jsonp 要求客户端请求的时候也要是jsonp的方式并且  callback=xxx
res, jsonp("数据");

// 重定向 301是永久重定向，302 临时重定向
res.redirect("http://www.baidu.com");

// 发送字符串数据自动加 content-type
res.send("");

// 显示一个文件
res.sendFile();

// 响应状态码
res.sendStatus();
```

## 模板渲染

---

### 使用 art-template 模板引擎

- 安装 `express-art-template art-template`依赖
- app.js 中配置
  - 注册一个模板引擎
    - `app.engine(.html'后缀名', express-art-template);`
    - 设置默认渲染引擎`app.set('view engine', '.html');`
  - 使用 `res.render(文件名， 数据对象)`
  - express 这套使用，默认在当前 app.js 同级的 views 目录查找
