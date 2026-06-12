# 4-3 Express中间件

## 知识要点

- 中间件是一个函数，可以访问请求对象 (req)、响应对象 (res) 和下一个中间件函数 (next)
- 中间件按注册顺序依次执行，调用 `next()` 传递到下一个中间件
- 使用 `app.use()` 注册中间件
- 错误处理中间件接收 4 个参数 `(err, req, res, next)`
- 可以匹配特定路径前缀的中间件：`app.use("/news", middleware)`

## 中间件基本概念

Express 中间件是一个函数，签名为 `(req, res, next) => {}`，按注册顺序形成一条处理链。

```js
app.get("/news/abc", (req, res, next) => {
  console.log("handler1");
  next(new Error("abc")); // 将错误传递给错误处理中间件
});
```

## 自定义静态资源中间件

当请求路径不以 `/api` 开头时，视为请求静态资源：

```js
// staticMiddleware.js
module.exports = (req, res, next) => {
  if (req.path.startsWith("/api")) {
    // 说明你请求的是 api 接口
    next();
  } else {
    // 说明你想要的是静态资源
    res.send("静态资源");
  }
};
```

## 错误处理中间件

错误处理中间件需要 4 个参数，第一个参数是错误对象：

```js
// errorMiddleware.js
module.exports = (err, req, res, next) => {
  if (err) {
    const errObj = {
      code: 500,
      msg: err instanceof Error ? err.message : err,
    };
    res.status(500).send(errObj);
  } else {
    next();
  }
};
```

## 注册中间件

```js
const express = require("express");
const app = express();

app.use(require("./staticMiddleware"));

app.get("/news/abc", (req, res, next) => {
  next(new Error("abc"));
});

// 路径匹配：能匹配 /news、/news/abc、/news/123、/news/ab/adfs
// 不能匹配 /n、/a、/、/newsabc
app.use("/news", require("./errorMiddleware"));

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```

## 关键要点

1. `next()` -- 传递到下一个中间件
2. `next(err)` -- 将错误传递给错误处理中间件
3. `throw new Error()` -- 等同于 `next(new Error())`
4. `app.use(path, middleware)` -- 路径前缀匹配
