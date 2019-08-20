#### session store

* 客户端cookie长度有限

* 客户端保存cookie信息不安全

* __客户端保存钥匙，服务器存储数据__

* ```js
  let store = {
    storage: {},
    get (key) {
      return this.storage[key]
    },
    set (key, sess) {
      this.storage[key] = sess
    },
    destroy (key) {
      delete this.storage[key]
    }
  } 
  
  app.use(session({store},app));
  ```

#### 聊天室练习

* 异步ajax聊天

* 功能扩展
  * 1. 即时聊天
    2. 统计在线人数
    3. 显示在线列表
    4. 私聊
    5. 群组聊天

#### 核心思想socket.io

- 轮询ajax(setInterval(发ajax))   __缺点:不停询问服务器，浪费性能__
- 服务器不关闭连接，一次响应，一直保持连接 __缺点:只有服务器向客户端不断输出__
```js
    // HTML5 中的eventSource来单向推数据, 一直保持链接
    var es = new EventSource('/getTimeByLongConn');
    es.onmessage = function(e) {
        document.querySelector('#time').value = e.data;
    }

```
- html5中出来了一个websocket   他是在原来http协议的基础上，去升级当前协议为websocket升级
  - 将原本  先有请求才有响应的机制，更改成了，服务端也可以主动发请求给客户端
  - HTTP 一问一答，  TCP协议，客户端与服务器建立连接以后，就可以自由的通信了
  - 缺点:__有兼容性问题__（IE11）



* 总结: 
  * 长轮询 : 客户端不停问，服务器不停回
  * 长连接: 客户端一次，服务器多次（服务器向客户端单向输出）
  * ws（握手） WebSocket
    * 全双工（双向工作（客户端和服务器））通信



- socket.io交互方式可能通过websocket/轮询ajax/服务器响应流(保持连接)
  - 1. 服务器可主动发数据到客户端
    2. 客户端向客户端发数据通过服务器

#### 下载包

- 客户端：socket.io-client
- 服务器：koa-socket

#### 操作步骤

- 服务器广播   ```io.broadcast('事件名',{ 数据} );```
- 服务器向客户端||客户端向服务器  ``` socket.emit('事件名',{数据});```
- 客户端接收  ```socket.on('事件名',data=>{} )```
- 服务器接收  ```io.on('事件名',data=>{});```

#### 进阶学习

- 私聊
  - 1. 客户端告诉服务器to谁,及内容
    2. 服务器通过io.to(socket.id)找到目标客户端,再通过emit通信
- 群组聊天
  - 加入群组 ```ctx.socket.join(groupid);```
  - 向群里通信```ctx.socket.broadcast.to(groupid).emit```
  - .to(groupId).emit('xxxx',data)
  - 后续接收还是对应on('xxxx')

#### excel操作

- node-xlsx 

#### 总结

- 第三方包的用途  koa-static 处理静态资源
- koa-router  query获取查询字符串  params 获取 /xxx/:id 中的id
- koa-session app.keys必须要写上作为其内部运算的标识
  - 两次setcookie其中一个是cookie标识
  - 一个是数据签名， 保证数据不被修改
- koa-art-template 按文档配置引擎
- koa-bodyparser 解析请求体   ctx.request.body获取数据
- socket.io
  - 客户端需要引入  socket.io-client
  - 服务器： koa-socket
  - 对象之间的关系和结构，都是写多就熟，也并不用很熟，看着文档，会来就行
  - 客户端端与服务器之间都是  on('xxx')  emit('xxx')去对应
    - join加入组
    - to 私聊
    - 事件名,数据    数据可以是对象，如果仅仅是字符串，  ctx.data拿就可以了，是对象ctx.data.xxxx







