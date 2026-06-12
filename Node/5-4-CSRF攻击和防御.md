# 5-4 CSRF攻击和防御

## 知识要点

### 1. CSRF 特点和原理

CSRF：**Cross Site Request Forgery**，跨站请求伪造

本质是：恶意网站把**正常用户**作为**媒介**，通过模拟正常用户的操作，攻击其**登录过**的站点。

**原理流程：**
1. 用户访问正常站点，登录后获取到了正常站点的令牌（以 Cookie 的形式保存）
2. 用户访问恶意站点，恶意站点通过某种形式去请求了正常站点（请求伪造），迫使正常用户把令牌传递到正常站点，完成攻击

### 2. CSRF 防御方法

#### (1) Cookie 的 SameSite 属性

将 Cookie 的 `SameSite` 设置为 `Strict`，禁止跨域附带 Cookie：

- **Strict**：严格模式，所有跨站请求都不附带 Cookie，有时会导致用户体验不好
- **Lax**（推荐）：宽松模式，超链接、GET 表单、预加载连接时发送 Cookie，其他不发送
- **None**：无限制

#### (2) 验证 Referer 和 Origin

页面中的二次请求都会附带 Referer 或 Origin 请求头，服务器可以进行验证。但某些浏览器可以被用户禁止发送 Referer。

#### (3) 使用非 Cookie 令牌

要求每次请求在请求体或请求头中附带 Token（如 `authorization: token`），而非依赖 Cookie。

#### (4) 验证码

每个敏感操作都需要附带验证码，安全性高但用户体验差。

#### (5) 表单随机数

服务端渲染时生成一次性随机数，提交时验证：
1. 客户端请求页面，传递 Cookie
2. 服务器生成随机数放入 Session，页面表单加入隐藏域
3. 提交表单时自动提交隐藏的随机数
4. 服务器对比随机数并清除

#### (6) 二次验证

当做出敏感操作时，进行二次验证（如输入密码、短信验证码等）。

---

## 关键代码

### CSRF 攻击示例 — 恶意网站（danger/index.html）

恶意网站通过隐藏的 iframe 加载攻击页面：

```html
<body>
  <h1>这是一个看上去极其正常的网页</h1>
  <iframe style="display: none;" src="./danger.html" frameborder="0"></iframe>
</body>
```

### CSRF 攻击示例 — 攻击页面（danger/danger.html）

攻击页面自动提交一个表单，模拟用户操作：

```html
<form
  id="dangerform"
  action="http://localhost:5008/api/student"
  method="POST"
>
  <input type="text" name="name" value="黑客" />
  <input type="text" name="sex" value="0" />
  <input type="text" name="mobile" value="10000000000" />
  <input type="text" name="ClassId" value="9" />
  <input type="text" name="birthday" value="0" />
</form>

<script>
  // 页面加载后自动提交表单，模拟用户向正常站点发送请求
  document.getElementById("dangerform").submit();
</script>
```

### 服务端入口与中间件配置（routes/init.js）

配置 Express 应用，加入 Cookie 解析和 Token 验证中间件：

```js
const express = require("express");
const app = express();
const path = require("path");

// 静态资源
const staticRoot = path.resolve(__dirname, "../public");
app.use(express.static(staticRoot));

// Cookie 解析中间件
const cookieParser = require("cookie-parser");
app.use(cookieParser());

// Token 验证中间件
app.use(require("./tokenMiddleware"));

// 请求体解析
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

// 路由
app.use("/api/student", require("./api/student"));
app.use("/api/admin", require("./api/admin"));

// 错误处理中间件
app.use(require("./errorMiddleware"));

app.listen(5008, () => {
  console.log("server listen on 5008");
});
```

### Token 验证中间件（routes/tokenMiddleware.js）

验证请求中的 Token，支持 Cookie 和 Authorization 两种方式：

```js
const { getErr } = require("./getSendResult");
const { pathToRegexp } = require("path-to-regexp");
const cryptor = require("../util/crypt");

// 需要 Token 验证的 API 列表
const needTokenApi = [
  { method: "POST", path: "/api/student" },
  { method: "PUT", path: "/api/student/:id" },
  { method: "GET", path: "/api/student" },
];

module.exports = (req, res, next) => {
  // 匹配当前请求是否需要 Token
  const apis = needTokenApi.filter((api) => {
    const reg = pathToRegexp(api.path);
    return api.method === req.method && reg.test(req.path);
  });
  if (apis.length === 0) {
    next();
    return;
  }

  // 从 Cookie 或 Authorization 头获取 Token
  let token = req.cookies.token;
  if (!token) {
    token = req.headers.authorization;
  }
  if (!token) {
    res.status(403).send(getErr("you dont have any token to access the api", 403));
    return;
  }
  // 解密 Token 获取用户 ID
  const userId = cryptor.decrypt(token);
  req.userId = userId;
  next();
};
```

### 登录接口 — 设置 Cookie SameSite 防御（routes/api/admin.js）

登录成功后设置 Cookie，使用 `sameSite: "Lax"` 防御 CSRF：

```js
router.post("/login", asyncHandler(async (req, res) => {
  const result = await adminServ.login(req.body.loginId, req.body.loginPwd);
  if (result) {
    let value = result.id;
    value = cryptor.encrypt(value.toString());
    // 设置 Cookie，SameSite 为 Lax 防 CSRF
    res.cookie("token", value, {
      path: "/",
      domain: "localhost",
      maxAge: 7 * 24 * 3600 * 1000,
      sameSite: "Lax",
    });
    res.header("authorization", value);
  }
  return result;
}));
```

### 加密工具（util/crypt.js）

使用 AES-128-CBC 对称加密算法加密 Token：

```js
const secret = Buffer.from("mm7h3ck87ugk9l4a"); // 128位密钥
const crypto = require("crypto");
const iv = Buffer.from("jxkvxz97409u3m8c"); // 随机向量

exports.encrypt = function (str) {
  const cry = crypto.createCipheriv("aes-128-cbc", secret, iv);
  let result = cry.update(str, "utf-8", "hex");
  result += cry.final("hex");
  return result;
};

exports.decrypt = function (str) {
  const decry = crypto.createDecipheriv("aes-128-cbc", secret, iv);
  let result = decry.update(str, "hex", "utf-8");
  result += decry.final("utf-8");
  return result;
};
```

### 统一响应格式（routes/getSendResult.js）

封装统一的 API 响应格式和异步错误处理：

```js
exports.getErr = function (err = "server internal error", errCode = 500) {
  return { code: errCode, msg: err };
};

exports.getResult = function (result) {
  return { code: 0, msg: "", data: result };
};

exports.asyncHandler = (handler) => {
  return async (req, res, next) => {
    try {
      const result = await handler(req, res, next);
      res.send(exports.getResult(result));
    } catch (err) {
      next(err);
    }
  };
};
```
