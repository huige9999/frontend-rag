# 5-2 Socket.IO

## 知识要点

### 1. Socket.IO 简介

Socket.IO 是一个基于 WebSocket 的库，提供了更高级的 API，简化了 WebSocket 的使用。它支持：
- 自动降级（在不支持 WebSocket 的浏览器中使用轮询）
- 事件驱动的消息机制
- 命名空间和房间
- 自动重连

### 2. 服务端 API

- `io.on("connection", callback)` — 监听客户端连接事件
- `socket.on(event, callback)` — 监听客户端发送的指定事件消息
- `socket.emit(event, data)` — 向当前客户端发送消息
- `socket.on("disconnect", callback)` — 监听客户端断开连接

### 3. 客户端 API

- `io.connect()` — 连接到服务器
- `socket.emit(event, data)` — 向服务器发送消息
- `socket.on(event, callback)` — 监听服务器发送的指定事件消息
- `socket.on("disconnect", callback)` — 监听断开连接

---

## 关键代码

### 服务端（index.js）

使用 Express + Socket.IO 搭建 WebSocket 服务：

```js
const express = require("express");
const socketIO = require("socket.io");
const http = require("http");
const path = require("path");

// 创建 Express 应用和 HTTP 服务器
const app = express();
const server = http.createServer(app);
app.use(express.static(path.resolve(__dirname, "public")));

// 创建 Socket.IO 实例
const io = socketIO(server);

io.on("connection", (socket) => {
  // 新客户端连接成功后触发
  console.log("新的客户端连接进来了");

  // 监听客户端的 msg 消息
  socket.on("msg", (chunk) => {
    console.log(chunk.toString("utf-8"));
  });

  // 每隔2秒向客户端发送 test 消息
  const timer = setInterval(function () {
    socket.emit("test", "test message from server");
  }, 2000);

  // 客户端断开连接时清理定时器
  socket.on("disconnect", () => {
    clearInterval(timer);
    console.log("closed");
  });
});

// 监听端口
server.listen(5008, () => {
  console.log("server listening on 5008");
});
```

### 客户端（public/index.html）

浏览器端使用 Socket.IO 客户端库连接服务器：

```html
<script src="https://cdn.bootcdn.net/ajax/libs/socket.io/2.3.0/socket.io.js"></script>
<script>
  // 连接服务器
  const socket = io.connect();

  // 点击按钮发送消息到服务器
  document.querySelector("button").onclick = function () {
    socket.emit("msg", "msg from client");
  };

  // 监听服务器的 test 消息
  socket.on("test", (chunk) => {
    console.log(chunk);
  });

  // 监听断开连接
  socket.on("disconnect", () => {
    console.log("closed");
  });
</script>
```
