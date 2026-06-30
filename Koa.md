# Koa

## 1. 目录结构整理

```
Koa
├── 第1章 koa概述
│   ├── 1-1. 什么是 Koa
│   └── 1-2. 对比 Express（更轻量 / 更合理的对象结构 / 更友好的中间件——洋葱模型）
│
├── 第2章 koa api
│   ├── 2-1. 创建 Koa 应用
│   ├── 2-2. 注册中间件
│   ├── 2-3. context（req / res / request / response）
│   ├── 2-4. 响应流程
│   ├── 2-5. 简化 api（request/response 成员提取到 context）
│   ├── 2-6. cookie（原生支持 / 加密 cookie / 旋转加密 KeyGrip）
│   ├── 2-7. 自定义空间（ctx.state）
│   └── 2-8. 错误处理（EventEmitter + app.on('error') / ctx.throw）
│
├── 第3章 中间件练习
│   ├── 3-1. 手写 koa-static（静态资源服务）
│   └── 3-2. 手写 koa-fallback（单页应用 history fallback）
│
└── 第4章 koa常用中间件
    ├── 4-1. 常用中间件生态总览（路由/解析/渲染/静态/缓存/session/jwt/压缩/日志/跨域/上传/代理/单页/转换）
    ├── 4-2. 路由实战（@koa/router）
    └── 4-3. 代理实战（koa-connect + http-proxy-middleware）
```

> 注：课程原仓库「每节课一个 git 分支」（如 `1-1`、`2-1`），本地仅 checkout 了 `master`，故上面的小节编号是按各章笔记 md 的小标题还原的，保留原课程顺序。

---

## 2. 章节逻辑说明

这门课是一条**「认识 Koa → 用熟 Koa 核心 API → 自己造中间件 → 接入生态」主线**，篇幅不长但层层递进：先建立「Koa 比 Express 优在哪儿、洋葱模型是什么」的认知，再把应用/context/cookie/错误处理这些天天要用的 API 过一遍，然后用「自己手写 koa-static、koa-fallback」验证对中间件模型的理解，最后引入官方/社区常用中间件，落地到路由与代理实战。

### 先讲什么：认识 Koa 与洋葱模型（第1章）

- **1-1 什么是 Koa**：Koa 是 Express 原班人马打造的「更轻量、更优雅」的 web 框架，当前主流是 `koa2`。
- **1-2 对比 Express**：从三个角度讲清 Koa 的定位——①更轻量（不内置中间件、不提供路由匹配，一切交给中间件）；②更合理的对象结构（`app / context / request / response`，区分原生 `req/res` 与封装对象）；③更友好的中间件（真正支持 async，形成「等后续中间件完成再回到自己」的**洋葱模型**，对比 Express 中 `1 2 4 3` 的乱序问题）。

**为什么先讲**：洋葱模型是 Koa 的灵魂，也是后面所有 API 和中间件的共同基底。先把「为什么有 Koa、它好在哪」讲透，后面看任何 API 都有抓手。

### 再讲什么：Koa 核心 API（第2章）

逐个过一遍写 Koa 应用最高频的对象与能力：

- **2-1 创建 Koa 应用**：两种启动方式（`http.createServer(app.callback())` 与 `app.listen`）。
- **2-2 注册中间件**：`app.use`，中间件签名 `function(ctx, next)`。
- **2-3 context**：`ctx` 持有 `req`/`res`（原生 http 对象）与 `request`/`response`（Koa 封装对象），Koa 建议优先用封装对象。
- **2-4 响应流程**：给 `body` 赋值时 `status` 会自动置 200/204。
- **2-5 简化 api**：把 `request`/`response` 的常用成员通过访问器提升到 `context` 上。
- **2-6 cookie**：原生 `ctx.cookies.set/get`，配合 `app.keys` + `signed: true` 实现加密 cookie（底层 KeyGrip「旋转加密」，多密钥轮流加解密，更难破解）。
- **2-7 自定义空间**：中间件之间传额外信息（如登录用户）统一放 `ctx.state`。
- **2-8 错误处理**：Koa 实现了 `EventEmitter`，用 `app.on('error')` 监听、`ctx.throw(status, msg)` 主动抛出。

**为什么这么安排**：从「怎么起服务」到「怎么写中间件」到「请求/响应对象怎么用」到「cookie/状态/错误怎么处理」，正好覆盖一次请求生命周期里要碰到的全部核心对象，是把第1章的概念变成手感的阶段。

### 然后讲什么：用中间件原理自己造轮子（第3章）

- **3-1 手写 koa-static**：用洋葱模型实现静态资源服务——判断 GET → 解析路径 → 区分目录/文件（目录递归找 `index.html`）→ 读取不到交给 `next` → 命中则 `ctx.body = fs.createReadStream` + 设 `ctx.type`。
- **3-2 手写 koa-fallback**：把满足「GET + accept 含 text/html + 路径无扩展名」的请求改写到 `/index.html`，给单页应用做 history 路由兜底。

**为什么这么安排**：前两章是「用」，这一章是「练」。手写 static 和 fallback 是把洋葱模型、`ctx` 控制、`next` 移交控制权真正吃透的最佳练手题——写完这两个，对「中间件该怎么设计」就有了体感。

### 最后讲什么：常用中间件生态与实战（第4章）

- **4-1 常用中间件生态总览**：一张表过完 Koa 主流中间件——路由（`@koa/router`）、请求体解析（`koa-bodyparser`）、模板渲染（`koa-views`）、静态/缓存（`koa-static`/`koa-static-cache`）、会话（`koa-session`）、鉴权（`koa-jwt`）、压缩（`koa-compress`）、日志（`koa-logger`）、跨域（`@koa/cors`）、上传（`@koa/multer`）、代理（`http-proxy-middleware`）、单页支持（`connect-history-api-fallback`）、旧中间件转换（`koa-convert`）、express/connect 中间件转换（`koa-connect`）。
- **4-2 路由实战**：用 `@koa/router` 建 `prefix`、注册 `router.get`、`module.exports = router.routes()` 挂到 `app.use`。
- **4-3 代理实战**：`koa-connect`（c2k）把 express 风格的 `http-proxy-middleware` 转成 Koa 中间件，做 `/api` 反向代理。

**为什么放最后**：前一章证明了「你能自己造中间件」，这一章回答「那真实的生态里都有哪些现成轮子、怎么组合」。从手写走向「站在巨人肩膀上」，并以路由 + 代理两个最常见需求收尾，课程闭环。

### 主线一句话总结

**认识 Koa 与洋葱模型（为什么有它）→ 核心对象与 API（怎么用它）→ 手写中间件（怎么造它）→ 常用中间件生态与实战（怎么组合它）**，最终目标是理解 Koa「极简内核 + 一切皆中间件 + 洋葱模型」的设计哲学，并能在真实项目里搭起一个 Koa 服务。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：独立搭建一个极简的 Koa 后端服务（路由 + 静态资源 + 代理 + 单页托管）；理解 Express 系框架「中间件」这一核心抽象；为阅读 Egg/Nest 等更重框架打基础。
- **和实际项目的关系**：洋葱模型与「手写中间件」的思路，是实际项目里写日志/鉴权/错误处理/统一响应等横切逻辑的直接范式；4-3 的代理实战是本地开发联调后端接口的常见配置。
- **和其他课程的关系**：是《Node》（Express 那条线）与《Egg》之间的「桥梁课」——Express 讲机制、Koa 讲更优雅的洋葱模型、Egg 讲在 Koa 之上的约定与插件化，三者构成一条完整的 Node Web 框架演进线，建议对照复习。
- **面试/教学高频场景**：洋葱模型原理与执行顺序（第1章）、Koa 与 Express 的对象结构差异（第1章）、async 中间件为何能 `await next`（第1章）、`ctx.state` 的用途（第2章）、cookie 的旋转加密（第2章）、手写一个静态/单页兜底中间件（第3章）。

---

## 4. 临时补充

- 课程仓库原为「每节课一个 git 分支」（`git checkout 1-1` 切课），本地只 checkout 了 `master`，所以本 md 的小节编号是按各章笔记 md 的标题还原的，并非原仓库的分支编号，整理时保留了原内容顺序。
- 第1章反复用「Express 的 `1 2 4 3` vs Koa 的 `1 2 3 4`」来说明洋葱模型，是这门课最核心的一张对比图，复习中间件执行顺序直接回 1-2。
- 第3章的两个手写中间件（`koa-static.js` / `koa-fallback.js`）其实就是第4章 `koa-static`、`connect-history-api-fallback` 的「迷你实现版」，复习时把 3-1/3-2 和 4-1 对照看，能一次把「原理 ↔ 生态」打通。
- 第4章的 `koa-connect`（c2k）是把 express/connect 生态接入 Koa 的关键粘合剂，4-3 的代理实战正是靠它把 `http-proxy-middleware` 转成 Koa 中间件——这是 Koa「内核极简、靠中间件拼装」哲学的一个典型缩影。
