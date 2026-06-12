# 5-1 WebSocket原理

## 知识要点

### 1. Socket 通信模型

1. 客户端连接服务器（TCP/IP），三次握手，建立连接通道
2. 客户端和服务器通过 Socket 接口发送消息和接收消息，任何一端在任何时候，都可以向另一端发送任何消息
3. 有一端断开了，通道销毁

### 2. HTTP 通信模型

1. 客户端连接服务器（TCP/IP），三次握手，建立连接通道
2. 客户端发送一个 HTTP 格式的消息（消息头 + 消息体），服务器响应 HTTP 格式的消息（消息头 + 消息体）
3. 客户端或服务器断开，通道销毁

**实时性的问题解决方案：**
1. 轮询
2. 长连接

### 3. WebSocket 通信模型

WebSocket 是专门用于解决实时传输问题的协议：

1. 客户端连接服务器（TCP/IP），三次握手，建立连接通道
2. 客户端发送一个 HTTP 格式的消息（特殊格式），服务器也响应一个 HTTP 格式的消息（特殊格式），称之为 **HTTP 握手**
3. 双方自由通信，通信格式按照 WebSocket 的要求进行
4. 客户端或服务器断开，通道销毁

### 4. 服务端握手响应

在 WebSocket 的 HTTP 握手阶段，服务器响应头中需要包含如下内容：

```
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: [key]
```

其中，`Sec-WebSocket-Accept` 的值计算算法为：

```js
base64(sha1(Sec-WebSocket-Key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"))
```

在 Node.js 中可以使用以下代码获得：

```js
const crypto = require("crypto");
const hash = crypto.createHash("sha1");
hash.update(requestKey + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11");
const key = hash.digest("base64");
```

其中，`requestKey` 来自于请求头中的 `Sec-WebSocket-Key`。

---

## 关键代码

### 服务端 - 手动实现 WebSocket 握手（index.js）

使用 Node.js 原生 `net` 模块手动实现 WebSocket 握手过程：

```js
const net = require("net");

const server = net.createServer((socket) => {
  console.log("收到客户端的连接");
  socket.once("data", (chunk) => {
    // 解析 HTTP 请求头，提取 Sec-WebSocket-Key
    const httpContent = chunk.toString("utf-8");
    let parts = httpContent.split("\r\n");
    parts.shift();
    parts = parts
      .filter((s) => s)
      .map((s) => {
        const i = s.indexOf(":");
        return [s.substr(0, i), s.substr(i + 1).trim()];
      });
    const headers = Object.fromEntries(parts);

    // 按照 WebSocket 协议计算 Sec-WebSocket-Accept
    const crypto = require("crypto");
    const hash = crypto.createHash("sha1");
    hash.update(
      headers["Sec-WebSocket-Key"] + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"
    );
    const key = hash.digest("base64");

    // 响应 HTTP 握手
    socket.write(`HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: ${key}

`);

    // 握手完成后，监听后续数据
    socket.on("data", (chunk) => {
      console.log(chunk);
    });
  });
});

server.listen(5008);
```

### 客户端 - 浏览器 WebSocket 使用（public/index.html）

浏览器端使用原生 `WebSocket` API 连接服务器：

```js
// 创建 WebSocket 连接，同时发送连接请求到服务器
const ws = new WebSocket("ws://localhost:5008");

ws.onopen = function () {
  // HTTP 握手完成
  console.log("连接已建立");
};

ws.onmessage = function (e) {
  console.log("来自服务器的数据", e.data);
};

ws.onclose = function () {
  console.log("通道关闭");
};

// 发送数据到服务器
document.querySelector("button").onclick = function () {
  ws.send("123");
};

// ws.close(); // 客户端主动断开连接
```
