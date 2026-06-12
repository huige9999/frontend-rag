# 1-9 http模块

## 知识要点

`http` 模块基于 `net` 模块封装，提供了更高层的 HTTP 协议支持。

### 核心方法

| 方法 | 说明 |
|------|------|
| `http.createServer(callback)` | 创建 HTTP 服务器 |
| `http.request(url, options, callback)` | 发送 HTTP 请求 |

### 服务器端（Server）

- 请求对象 `req`：包含请求方法、路径、头部、请求体
- 响应对象 `res`：用于设置状态码、响应头、发送响应体

### 客户端请求

- 使用 `http.request()` 创建请求
- 必须调用 `request.end()` 表示请求体结束

---

## 代码示例

### 1. HTTP 服务器（server.js）

创建 HTTP 服务器，监听 9527 端口，解析请求信息并返回响应：

```js
const http = require("http");
const url = require("url");

function handleReq(req) {
  console.log("有请求来了！");
  const urlobj = url.parse(req.url);
  console.log("请求路径", urlobj);
  console.log("请求方法", req.method);
  console.log("请求头", req.headers);

  let body = "";
  req.on("data", chunk => {
    body += chunk.toString("utf-8");
  });
  req.on("end", () => {
    console.log("请求体", body);
  });
}

const server = http.createServer((req, res) => {
  handleReq(req);
  res.setHeader("a", "1");
  res.setHeader("b", "2");
  res.statusCode = 404;
  res.write("你好！");
  res.end();
});

server.listen(9527);
server.on("listening", () => {
  console.log("server listen 9527");
});
```

### 2. HTTP 客户端（client.js）

使用 `http.request` 发送 GET 请求：

```js
const http = require("http");

const request = http.request(
  "http://yuanjin.tech:5005/api/movie",
  {
    method: "GET"
  },
  resp => {
    console.log("服务器响应的状态码", resp.statusCode);
    console.log("服务器响应头", resp.headers);
    let result = "";
    resp.on("data", chunk => {
      result += chunk.toString("utf-8");
    });
    resp.on("end", chunk => {
      console.log(JSON.parse(result));
    });
  }
);

request.end(); // 表示消息体结束
```

### 3. 静态资源服务器（index.js）

根据请求 URL 返回对应的静态文件，支持目录自动查找 `index.html`：

```js
const http = require("http");
const URL = require("url");
const path = require("path");
const fs = require("fs");

async function getStat(filename) {
  try {
    return await fs.promises.stat(filename);
  } catch {
    return null;
  }
}

async function getFileContent(url) {
  const urlObj = URL.parse(url);
  let filename = path.resolve(__dirname, "public", urlObj.pathname.substr(1));
  let stat = await getStat(filename);
  if (!stat) {
    return null;
  } else if (stat.isDirectory()) {
    filename = path.resolve(__dirname, "public", urlObj.pathname.substr(1), "index.html");
    stat = await getStat(filename);
    if (!stat) return null;
    return await fs.promises.readFile(filename);
  } else {
    return await fs.promises.readFile(filename);
  }
}

async function handler(req, res) {
  const info = await getFileContent(req.url);
  if (info) {
    res.write(info);
  } else {
    res.statusCode = 404;
    res.write("Resource is not exist");
  }
  res.end();
}

const server = http.createServer(handler);
server.on("listening", () => {
  console.log("server listen 6000");
});
server.listen(6100);
```
