# 4-12 session

## 知识要点

- Session 是服务器端存储的会话数据
- 使用 `express-session` 中间件管理 session
- Session 通过 cookie 中的 session ID 关联客户端
- 登录后将用户信息存入 `req.session`，后续请求通过 session 判断登录状态

## express-session 中间件

```js
const session = require("express-session");

app.use(
  session({
    secret: "yuanjin",     // 签名密钥
    name: "sessionid",     // cookie 名称
  })
);
```

## 登录时写入 session

```js
// routes/api/admin.js
router.post("/login", asyncHandler(async (req, res) => {
  const result = await adminServ.login(req.body.loginId, req.body.loginPwd);
  if (result) {
    let value = result.id;
    value = cryptor.encrypt(value.toString());
    // 登录成功，将用户信息存入 session
    req.session.loginUser = result;
  }
  return result;
}));
```

## Token 中间件改为 session 验证

之前通过 cookie/header 中的 token 验证身份，现在改为通过 session 验证：

```js
// routes/tokenMiddleware.js
module.exports = (req, res, next) => {
  // ... 路由匹配逻辑
  if (req.session.loginUser) {
    // 说明已经登录过了
    next();
  } else {
    handleNonToken(req, res, next);
  }
};
```

## Session vs Cookie 认证

| 特性 | Cookie 认证 | Session 认证 |
|------|-----------|------------|
| 存储位置 | 客户端 | 服务器端 |
| 安全性 | 较低 | 较高 |
| 扩展性 | 天然支持分布式 | 需要额外处理 |
| 依赖 | 无 | 依赖 Cookie |
