## Node.js  01  初识

#### 简介

​	`Node.js`是一个让`JavaScript`运行在服务器端的开发平台，它让JavaScript的触角伸到了服务器端，可以与`PHP、JSP、Python、Ruby`平起平坐。

但Node似乎有点不同：

+ **`Node.js`**不是一种独立的语言**，与`PHP、JSP、Python、Perl、Ruby`的“既是语言，也是平台”不同，`Node.js`**使用`JavaScript`进行编程，运行在`JavaScript`引擎上（`V8`）。

+ 与`PHP、JSP`等相比（`PHP、JSP、.net`都需要运行在服务器程序上，`Apache、Naginx、Tomcat、IIS`。

），**`Node.js`跳过了`Apache、Naginx、IIS`等`HTTP`服务器，它自己不用建设在任何服务器软件之上**。`Node.js`的许多设计理念与经典架构（`LAMP = Linux + Apache + MySQL + PHP`）有着很大的不同，可以提供强大的伸缩能力。一会儿我们就将看到，`Node.js`没有web容器。

#### `Node.js` 特点

+ **单线程**

  ​		`node.js`是基于谷歌`v8 `引擎孕育而生的一门“语言”，本质上仍然是`Javascript`，所以在执行机制上是单线程的。

+ **非阻塞 I/O**

  ​		所谓**I/O**（`input`/`output`）即数据库的读写操作，在常规`Apach、Java、PHP`搭建的服务器中，每新增一个"用户"，则会分出2Mb的内存去处理**I/O**交互，这样会导致高并发的场景非常棘手。而在`node.js`中，凡是遇到**I/O**操作，`node.js`可以将其列入到异步任务队列中，这样不影响主线程的任务，待异步任务完成时，再执行相应的逻辑，即**事件驱动**。所以，`node.js`并不适用与复杂的数据计算等业务。

+ **事件驱动**

  ​		`node.js`的优点就是因为所有的交互都是可以异步来实现。在Node中，客户端请求建立连接，提交数据等行为，会触发相应的事件。在一个时刻，只能执行一个事件回调函数，但是在执行一个事件回调函数的中途，可以转而处理其他事件（比如，又有新用户连接了），然后返回继续执行原事件的回调函数，这种处理机制，称为“事件环”机制。

+ **`Node.js`没有web容器**

  ​		我们在`web`浏览器中访问`/static/imgs/01.png`或者`/static/css/style.css`路径查找资源时，后端通常是在`public`文件夹下建了类似`/static/imgs/...`或者`/statoc/css/...`这样的目录结构（web容器），并配置路径与文件的映射关系，所以我们能在页面访问到。

  ​		而很多人在学习`node.js`的时候也认为`node`服务器根目录中有一个`static`这样的文件夹，里面有我们放着我们需要的静态资源。实际上**`Node.js`没有web容器!!!**

  ​		实际上我们输入以上路径时，`node`会根据`url`匹配不同的路径，做出相应：

  ```js
  const http = require('http');
  const fs = require('fs');
  const psth = require('path');
  
  const server = http.createServer((req, res) => {
     if (req.url === '/') {
         fs.readfile(path.resolve(__dirname, 'index.html'), (err, data) => {
             res.end(data)
         })
     } else if (req.url === '/static/imgs/01.png') {
          fs.readfile(path.resolve(__dirname, 'imgs'), (err, data) => {
             res.end(data)
         })
     }
  });
  server.listen(3000, 'localhost', () => {
      console.log('app has started at http://localhost:3000');
  })
  ```

  当我们访问`node.js`服务器中静态资源时，都要对路由（路径）做配置，否则就不能响应对应的资源。这也是`node.js`强大的地方，他的路由匹配是相当灵活的。

