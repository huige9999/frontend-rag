# 4-1 Express的基本使用

## 知识要点

- Express 是一个基于 Node.js 的 Web 应用框架
- 通过 `express()` 创建应用实例
- 使用 `app.请求方法("路径", 处理函数)` 配置路由映射
- `req` 和 `res` 是被 express 封装后的对象
- express 应用本质上是一个处理请求的函数，可传给 `http.createServer`

## 基本使用

### 创建 Express 应用

```js
const express = require("express");
const app = express(); // 创建一个express应用
```

### 配置请求映射

`app.请求方法("请求路径", 处理函数)`，如果请求方法和请求路径均满足匹配，交给处理函数进行处理。

```js
app.get("/news/:id", (req, res) => {
  // req 和 res 是被express封装过后的对象
  // 获取请求信息
  console.log("请求头", req.headers); // 获取请求头对象
  console.log("请求路径", req.path);
  console.log("query", req.query);
  console.log("params", req.params);

  // 响应方式
  res.redirect(302, "https://duyi.ke.qq.com");
});

// 匹配任何get请求
app.get("*", (req, res) => {
  console.log("abc");
});
```

### 启动监听

```js
const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```

### Express 与 http 模块的关系

express 应用实际上是一个函数，可以直接传给 `http.createServer`：

```js
const express = require("express");
const http = require("http");
const app = express();
const server = http.createServer(app);
const port = 5008;
server.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```

## 常用 res 方法

| 方法 | 说明 |
|------|------|
| `res.send(data)` | 发送响应数据（自动判断类型） |
| `res.status(code)` | 设置响应状态码 |
| `res.header(key, value)` | 设置响应头 |
| `res.location(url)` | 设置 Location 头 |
| `res.redirect(code, url)` | 重定向 |
| `res.end()` | 结束响应 |
