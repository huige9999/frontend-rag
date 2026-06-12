# 4-11 CORS中间件

## 知识要点

- 使用第三方 CORS 中间件 `cors` 简化跨域配置
- 通过白名单（whiteList）控制允许的源
- 配置 `credentials: true` 允许携带 Cookie
- 使用 `origin` 回调函数动态判断是否允许跨域

## 安装 cors 中间件

```shell
npm install cors
```

## 使用 cors 中间件

替换上一课手写的 CORS 中间件，使用第三方 `cors` 包：

```js
const cors = require("cors");

const whiteList = ["null"];
app.use(
  cors({
    origin(origin, callback) {
      if (whiteList.includes(origin)) {
        callback(null, origin);
      } else {
        callback(new Error("not allowed"));
      }
    },
    credentials: true,
  })
);
```

## cors 配置选项

| 选项 | 说明 |
|------|------|
| `origin` | 允许的源，可以是字符串、数组或函数 |
| `methods` | 允许的 HTTP 方法 |
| `allowedHeaders` | 允许的请求头 |
| `credentials` | 是否允许携带 Cookie |
| `maxAge` | 预检请求缓存时间 |

## 对比上一课

4-10 课手写了 CORS 中间件，本课使用成熟的 `cors` 包替代，功能更完善，代码更简洁。

删除了 `routes/corsMiddleware.js`，改为使用 `const cors = require("cors")`。
