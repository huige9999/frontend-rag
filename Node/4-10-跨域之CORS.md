# 4-10 跨域之CORS

## 知识要点

- CORS（Cross-Origin Resource Sharing）是 HTTP/1.1 的跨域解决方案
- 三种交互模式：简单请求、需要预检的请求、附带身份凭证的请求
- 服务器通过响应头告知浏览器是否允许跨域
- 预检请求使用 OPTIONS 方法
- 附带凭证时 `Access-Control-Allow-Origin` 不能为 `*`

## CORS 三种模式

### 1. 简单请求

同时满足以下条件：
- 请求方法为 GET / POST / HEAD
- 请求头仅包含安全字段
- Content-Type 仅限 `text/plain`、`multipart/form-data`、`application/x-www-form-urlencoded`

服务器响应头需包含 `Access-Control-Allow-Origin`。

### 2. 需要预检的请求

不满足简单请求条件时，浏览器先发 OPTIONS 预检请求，服务器允许后再发真实请求。

### 3. 附带身份凭证的请求

需要设置 `Access-Control-Allow-Credentials: true`。

## 本节新增 - 手写 CORS 中间件

```js
// routes/corsMiddleware.js
const allowOrigins = ["http://127.0.0.1:5500", "null"];

module.exports = function (req, res, next) {
  // 处理预检请求
  if (req.method === "OPTIONS") {
    res.header(
      `Access-Control-Allow-Methods`,
      req.headers["access-control-request-method"]
    );
    res.header(
      `Access-Control-Allow-Headers`,
      req.headers["access-control-request-headers"]
    );
  }
  res.header("Access-Control-Allow-Credentials", true);
  // 处理简单请求
  if ("origin" in req.headers && allowOrigins.includes(req.headers.origin)) {
    res.header("access-control-allow-origin", req.headers.origin);
  }
  next();
};
```

## 在入口文件中使用

```js
app.use(require("./corsMiddleware"));
```

## 关键响应头

| 响应头 | 说明 |
|--------|------|
| `Access-Control-Allow-Origin` | 允许的源 |
| `Access-Control-Allow-Methods` | 允许的请求方法 |
| `Access-Control-Allow-Headers` | 允许的请求头 |
| `Access-Control-Allow-Credentials` | 允许携带凭证 |
| `Access-Control-Max-Age` | 预检请求缓存时间 |
| `Access-Control-Expose-Headers` | 允许 JS 访问的响应头 |
