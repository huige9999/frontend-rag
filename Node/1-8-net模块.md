# 1-8 net模块

## 知识要点

`net` 模块是 Node.js 的底层网络通信模块，提供 TCP 协议的 socket 编程能力。

### 核心概念

- **Server** — `net.createServer()` 创建 TCP 服务器
- **Socket** — 通信套接字，用于读写数据
- **TCP** — 传输控制协议，面向连接、可靠的传输层协议

### 服务器端事件

- `listening` — 服务器开始监听
- `connection` — 有客户端连接，回调参数为 `socket`

### Socket 事件

- `data` — 接收到数据
- `end` — 连接关闭
- `close` — socket 完全关闭

---

## 代码示例

### 1. TCP 服务器（index.js）

创建 TCP 服务器，当收到请求时返回一张图片（模拟 HTTP 响应）：

```js
const net = require("net");
const server = net.createServer();
const fs = require("fs");
const path = require("path");

server.listen(9527); // 监听9527端口

server.on("listening", () => {
  console.log("server listen 9527");
});

server.on("connection", socket => {
  console.log("有客户端连接到服务器");

  socket.on("data", async chunk => {
    const filename = path.resolve(__dirname, "./hsq.jpg");
    const bodyBuffer = await fs.promises.readFile(filename);
    // 构造 HTTP 响应头
    const headBuffer = Buffer.from(
      `HTTP/1.1 200 OK\r\nContent-Type: image/jpeg\r\n\r\n`,
      "utf-8"
    );
    const result = Buffer.concat([headBuffer, bodyBuffer]);
    socket.write(result);
    socket.end();
  });

  socket.on("end", () => {
    console.log("连接关闭了");
  });
});
```

> 本例使用 `net` 模块手动构造 HTTP 响应报文，展示了 HTTP 协议的底层原理。

### 2. TCP 客户端（client.js）

使用 `net.createConnection` 连接远程服务器，发送 HTTP 请求并解析响应：

```js
const net = require("net");
const socket = net.createConnection(
  {
    host: "duyi.ke.qq.com",
    port: 80
  },
  () => {
    console.log("连接成功");
  }
);

// 解析响应的头部和消息体
function parseResponse(response) {
  const index = response.indexOf("\r\n\r\n");
  const head = response.substring(0, index);
  const body = response.substring(index + 2);
  const headParts = head.split("\r\n");
  const headerArray = headParts.slice(1).map(str => {
    return str.split(":").map(s => s.trim());
  });
  const header = headerArray.reduce((a, b) => {
    a[b[0]] = b[1];
    return a;
  }, {});
  return {
    header,
    body: body.trimStart()
  };
}

var receive = null;

function isOver() {
  const contentLength = +receive.header["Content-Length"];
  const curReceivedLength = Buffer.from(receive.body, "utf-8").byteLength;
  return curReceivedLength > contentLength;
}

socket.on("data", chunk => {
  const response = chunk.toString("utf-8");
  if (!receive) {
    receive = parseResponse(response);
    if (isOver()) socket.end();
    return;
  }
  receive.body += response;
  if (isOver()) socket.end();
});

// 发送 HTTP 请求
socket.write(
  `GET / HTTP/1.1\r\nHost: duyi.ke.qq.com\r\nConnection: keep-alive\r\n\r\n`
);

socket.on("close", () => {
  console.log(receive.body);
  console.log("结束了！");
});
```

> 本例使用 `net` 模块手动发送 HTTP 请求报文并解析响应，帮助理解 HTTP 协议的工作原理。
