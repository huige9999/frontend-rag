# 5-5 XSS攻击和防御

## 知识要点

### 1. XSS 简介

XSS：**Cross Site Scripting**，跨站脚本攻击

### 2. XSS 攻击类型

#### (1) 存储型 XSS

1. 恶意用户提交了恶意内容到服务器
2. 服务器没有识别，保存了恶意内容到数据库
3. 正常用户访问服务器
4. 服务器在不知情的情况下，给予了之前的恶意内容，让正常用户遭到攻击

#### (2) 反射型 XSS

1. 恶意用户分享了一个正常网站的链接，链接中带有恶意内容
2. 正常用户点击了该链接
3. 服务器在不知情的情况下，把链接的恶意内容读取了出来，放进了页面中，让正常用户遭到攻击

#### (3) DOM 型 XSS

1. 恶意用户通过任何方式，向服务器中注入了一些 DOM 元素，从而影响了服务器的 DOM 结构
2. 普通用户访问时，运行的是服务器的正常 JS 代码，但 DOM 结构已被篡改

### 3. XSS 防御方法

- **输入过滤**：使用 `xss` 库对用户输入进行过滤
- **输出编码**：在模板渲染时使用转义输出（如 EJS 的 `<%= %>` 而非 `<%- %>`）
- **CSP（内容安全策略）**：通过 HTTP 头限制页面可加载的资源来源
- **HttpOnly Cookie**：防止 JS 读取 Cookie

---

## 关键代码

### 服务端 — 使用 xss 库过滤输入（index.js）

使用 `xss` 库创建自定义过滤器，对请求体中的数据进行 XSS 过滤：

```js
const express = require("express");
const http = require("http");
const path = require("path");
const xss = require("xss");

const app = express();
const server = http.createServer(app);
app.use(express.static(path.resolve(__dirname, "public")));
app.use(express.json());
app.use(express.urlencoded());

// 创建自定义 XSS 过滤器，允许保留 style 属性
const myxss = new xss.FilterXSS({
  onTagAttr(tag, name, value, isWhiteAttr) {
    if (name === "style") {
      return `style="${value}"`;
    }
  },
});

// 中间件：对所有请求体数据进行 XSS 过滤
app.use((req, res, next) => {
  for (const key in req.body) {
    const value = req.body[key];
    req.body[key] = myxss.process(value);
  }
  next();
});

// 文章数据（简化为内存数组）
const articles = [];

app.post("/api/article/add", (req, res) => {
  articles.push(req.body.content);
  res.send({ code: 0, msg: "", data: "ok" });
});

// 渲染文章列表页面
app.get("/articles", (req, res) => {
  res.render("articles.ejs", {
    articles,
    redirect: req.query.redirect,
  });
});

server.listen(5008, () => {
  console.log("server listening on 5008");
});
```

### 客户端 — 发布文章页面（public/index.html）

用户提交文章内容，发送到服务器：

```js
const txt = document.getElementById("txtArticle");
document.querySelector("button").onclick = function () {
  fetch("/api/article/add", {
    method: "POST",
    headers: { "content-type": "application/json" },
    body: JSON.stringify({ content: txt.value }),
  })
    .then((resp) => resp.json())
    .then((resp) => {
      console.log(resp);
    });
};
```

### 文章列表模板（views/articles.ejs）

使用 EJS 模板引擎渲染文章列表，注意区分转义输出（`<%=`）和原始输出（`<%-`）：

```html
<body>
  <a href="/">发布文章</a>
  <!-- 反射型 XSS 风险：redirect 未转义 -->
  <p>
    <a href="/<%=redirect%>">跳转到：<%=redirect%></a>
  </p>
  <h1>文章列表</h1>
  <% articles.forEach(art => { %>
  <div style="border-bottom: 1px solid;">
    <%- art %>  <!-- 原始输出，存储型 XSS 风险 -->
  </div>
  <% }) %>
</body>
```

> **注意**：`<%- art %>` 使用了原始输出，如果输入未经 `xss` 库过滤，恶意脚本将被直接执行。安全的做法是使用 `<%= art %>` 进行转义输出。
