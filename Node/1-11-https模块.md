# 1-11 https模块

## 知识要点

`https` 模块的 API 与 `http` 模块基本一致，区别在于：
- 创建服务器需要提供 **SSL 证书**（私钥 + 证书文件）
- 默认监听端口为 **443**
- 通信过程自动加密/解密

### 创建 HTTPS 服务器所需文件

- `server-key.pem` — 服务器私钥
- `server-cert.crt` — 服务器证书

---

## 代码示例

### 1. HTTPS 服务器（server.js）

与 `http.createServer` 类似，但需要传入证书配置：

```js
const https = require("https");
const URL = require("url");
const path = require("path");
const fs = require("fs");

// 获取文件状态
async function getStat(filename) {
  try {
    return await fs.promises.stat(filename);
  } catch {
    return null;
  }
}

// 根据URL获取文件内容
async function getFileContent(url) {
  const urlObj = URL.parse(url);
  let filename = path.resolve(__dirname, "public", urlObj.pathname.substr(1));
  let stat = await getStat(filename);
  if (!stat) {
    return null;
  } else if (stat.isDirectory()) {
    filename = path.resolve(
      __dirname, "public", urlObj.pathname.substr(1), "index.html"
    );
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

// 创建 HTTPS 服务器（需要证书）
const server = https.createServer(
  {
    key: fs.readFileSync(path.resolve(__dirname, "./server-key.pem")),   // 私钥
    cert: fs.readFileSync(path.resolve(__dirname, "./server-cert.crt")) // 证书
  },
  handler
);

server.on("listening", () => {
  console.log("server listen 443");
});
server.listen(443);
```

### 2. HTTPS 客户端请求（client.js）

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

request.end();
```

### 3. 请求处理（index.js）

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
