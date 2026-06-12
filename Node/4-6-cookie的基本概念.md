# 4-6 Cookie的基本概念

## 知识要点

- HTTP 协议是无状态的，服务器无法确定两次请求是否来自同一客户端
- Cookie 是浏览器端的"卡包"，用于存放各种身份凭证
- Cookie 由 key、value、domain、path、secure、expire 等属性组成
- 服务器通过 `set-cookie` 响应头设置 Cookie
- 客户端通过 `cookie` 请求头自动附带 Cookie

## HTTP 无状态问题

HTTP 协议是无状态的，即服务器不知道这一次请求的人跟之前登录请求成功的人是不是同一个人。

解决方案：
1. 客户端登录成功后，服务器给客户端一个令牌（token）
2. 后续客户端的每次请求，都附带这个令牌

## Cookie 的组成

每个 cookie 包含以下信息：

- **key**：键
- **value**：值
- **domain**：所属网站域
- **path**：所属基路径
- **secure**：是否使用安全传输（HTTPS）
- **expire**：过期时间

## Cookie 自动附带条件

浏览器在发送请求时，自动附带的 cookie 须同时满足：
1. cookie 没有过期
2. cookie 的域与请求的域匹配
3. cookie 的 path 与请求的 path 匹配
4. 验证安全传输（如果 secure=true 则必须 HTTPS）

## 服务器端设置 Cookie

通过 `set-cookie` 响应头：

```
set-cookie: token=123456; path=/; max-age=3600; httponly
```

各属性说明：
- `path`：cookie 的路径，不设置默认为请求路径
- `domain`：cookie 的域，不设置默认为请求域
- `expire`：过期时间（GMT 格式）
- `max-age`：相对有效期（秒）
- `secure`：仅 HTTPS 传输
- `httponly`：禁止 JS 获取，防止 XSS 攻击

## 本节代码 - 通过 set-cookie 设置登录凭证

```js
// routes/api/admin.js
router.post("/login", asyncHandler(async (req, res) => {
  const result = await adminServ.login(req.body.loginId, req.body.loginPwd);
  if (result) {
    // 登录成功，通过响应头设置 cookie
    res.header("set-cookie", `token=${result.id}; path=/; domain=localhost; max-age=3600; httponly`);
  }
  return result;
}));
```

## 删除 Cookie

删除 cookie 其实是修改 cookie，让同名同域同路径的 cookie 过期：

```
set-cookie: token=; domain=localhost; path=/; max-age=-1
```
