# Redis

## 1. 目录结构整理

```
Redis
├── 第1章 安装redis
│   ├── redis 简介（键值对数据库 / nosql / 五种值类型 / 内存读写 / 持久化）
│   ├── windows 中安装redis
│   ├── mac 中安装 redis（brew）
│   └── 可视化工具（VSCode 插件 Redis）
│
├── 第2章 redis的基本命令
│   ├── 进入redis命令交互（无认证 / 带认证 auth）
│   ├── 通用命令（keys/exists/type/dbsize/ttl/expire/rename/del/select/flushdb/flushall）
│   ├── 字符串（set/get/mget/incr/incrby/decr/decrby）
│   ├── list（rpush/lpush/llen/lrange/lindex/lset/lrem/lpop/rpop）
│   └── hash（hset/hget/hkeys/hvals/hgetall/hexists/hdel/hlen）
│
├── 第3章 在node中使用redis
│   ├── node-redis 客户端
│   ├── 创建连接（createClient：host/port/password）
│   └── 操作方式（与原生命令一致 + promisify 转换为 Promise）
│
├── 第4章 缓存响应体
│   ├── 缓存命中流程（请求 → 查 redis → 有则直接响应 / 无则走数据库并缓存）
│   └── 自定义 cache 中间件（劫持 res.write/res.end 抓取响应体，按 originalUrl 为 key 写入 redis）
│
└── 第5章 缓存session
    ├── express-session（会话中间件）
    ├── connect-redis（将 session 存储托管到 redis）
    └── 用法（RedisStore + client + ttl，替代默认内存存储）
```

> 注：原课程每节课对应一个 git 分支（1-1、2-1…），仓库为 DuYi-Edu/DuYi-Redis，readme 仅说明分支切换方式，无额外目录。此处按「5 个章节目录」原顺序整理。

---

## 2. 章节逻辑说明

这门课是一条**「认识 redis → 学会操作 → 在 Node 中调用 → 用 redis 解决真实缓存问题」主线**，从「redis 是什么、怎么装」开始，先把 redis 当成一个独立数据库去学它的命令，再用 node-redis 把它接入代码，最后落地到两个最经典的缓存场景（缓存响应体、缓存 session）。

### 先讲什么：认识 redis 与基本命令（第1、2章）

- **第1章 安装redis**：先把 redis 讲清楚——它是键值对 nosql 数据库、值有五种类型（string/hash/list/set/sorted set）、操作在内存完成所以读写极快但耗内存、默认异步持久化到磁盘以便重启恢复；这正是「为什么用它做缓存」的全部前提。再给出 windows/mac 安装与可视化工具，让本地能跑起来。
- **第2章 redis的基本命令**：进入 `redis-cli`（无认证与带 `auth` 认证两种），系统过一遍命令——通用命令（key 的增删查、过期、库选择）+ 三种高频数据类型的命令（字符串的 set/get/incr，list 的 push/pop/range，hash 的 hset/hget/hgetall）。

**为什么这么安排**：在使用任何工具前，先把它当成黑盒之外的东西搞懂——「它是什么、装在哪、怎么直接操作」。第1、2章解决的是「脱离代码，我能不能用 redis」，把 redis 本身当成一个独立的数据库来掌握，这是后面所有应用的基础。

### 再讲什么：把 redis 接入 Node（第3章）

- **第3章 在node中使用redis**：引入 `node-redis` 客户端，用 `createClient` 建立连接（host/port/password），并强调「操作方式和 redis 原生命令基本一致」——`client.set/get`。唯一新增的工程细节是用 `util.promisify` 把回调风格转成 Promise，为后面写 async 中间件铺路。

**为什么这么安排**：命令会了，但真实开发是在 Node 代码里调用。这一章是「shell 命令」到「JS 代码」的桥，也是唯一一次单独讲客户端 API——后面两章都不再讲怎么连，默认你已经会了。

### 最后讲什么：落地两个经典缓存场景（第4、5章）

- **第4章 缓存响应体**：第一个实战。讲清缓存命中流程（请求 → 查 redis → 命中直接响应 / 未命中走数据库并回填缓存），并手写一个 Express 中间件：用 `req.originalUrl` 当 key，**劫持 `res.write`/`res.end` 收集响应体**再写入 redis，支持 `ttl` 过期。这是整门课最硬核的一节。
- **第5章 缓存session**：第二个实战。用 `express-session` 管理会话、`connect-redis` 把 session 存储从默认内存换到 redis（`RedisStore` + client + ttl），解决多进程/重启后 session 丢失的问题。

**为什么放最后**：缓存是 redis 最典型的用途，前 3 章的全部知识都在这里收口——「连上 redis（第3章）→ 用 set/get 存取（第2章）→ 按 key/过期时间管理（第2章）」。第4章教你「自己造一个缓存中间件」理解原理，第5章教你「用现成库 connect-redis」解决标准问题，一造一用，覆盖 redis 作为缓存的两种典型姿势。

### 主线一句话总结

**认识 redis（是什么/值类型/内存与持久化）→ 装好并掌握命令（通用/字符串/list/hash）→ 接入 Node（node-redis + promisify）→ 实战缓存（手写缓存响应体中间件 + 用 connect-redis 托管 session）**，最终目标是能在 Node 服务里把 redis 当缓存用起来。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：为 Node/Express 服务接入 redis 缓存层（接口响应缓存、session 共享）；后续做接口限流、排行榜（sorted set）、消息队列（list）等也有现成的命令基础。
- **和实际项目的关系**：第4章「缓存响应体」是真实项目里降低数据库压力的直接手段；第5章「缓存 session」是服务多进程部署（如 pm2 集群、Egg 多 worker）时 session 必须外置的标配方案。
- **和其他课程的关系**：是《Node》《Egg》课程的配套存储层——Egg 中 `egg-session-redis`、Node 中 `express-session` + `connect-redis` 是同一思路；redis 命令本身与具体后端框架无关，可横向复用。
- **面试/教学高频场景**：redis 为什么快（内存 + 单线程 + 数据结构）（第1章）、五大数据类型及典型场景（第1、2章）、缓存击穿/穿透/雪崩（第4章延伸）、过期策略 ttl（第2章）、session 为何要存到 redis（第5章）。

---

## 4. 临时补充

- 原课程每个章节目录下都有 `index.js`（第3~5章）和 `package.json`，是可直接 `npm install && node index.js` 跑的 demo，复习时直接回对应目录跑代码最快。
- 第2章只讲了 string/list/hash 三种类型的命令，**set 和 sorted set 未展开**（第1章简介里提到但命令章没讲），用到时需自行补齐。
- 第4章的「劫持 res.write/res.end 抓响应体」是 Express 中间件获取响应体的通用技巧，与 redis 无关，写日志/响应压缩等场景也能复用。
- 第3章代码用的是旧版 node-redis（回调式 + `util.promisify`）；新版 node-redis（v4+）已原生支持 Promise，复习时注意区分 API 风格。
- 第4章 demo 用 `express.static("./public")` + `/api/news/:id`，配合浏览器/Postman 验证缓存命中（控制台打印「使用了缓存 / 没有使用缓存」），ttl 设为 10 秒方便观察过期。
