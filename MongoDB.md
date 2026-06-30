# MongoDB

## 1. 目录结构整理

```
MongoDB
├── 第1章 入门：安装与基础概念
│   ├── 1. mongodb的安装（Windows/macOS 安装 + Robo 3T + db/collection/document 概念）
│   └── 2. mongodb的基本操作（mongo shell 常用命令、原生 CRUD 导览）
│
├── 第2章 Mongoose 建模与 CRUD
│   ├── 3. schema和model（mongoose 驱动、Schema 结构与约束、Model 定义）
│   ├── 4. 新增文档（insertOne/insertMany、save/create、验证机制、__v 与 _id）
│   ├── 5. 文档查询（find 游标、filter 操作符、projection 投影、mongoose 查询差异）
│   ├── 6. 文档更新（$set/$inc/$push/$pull 等更新操作符、upsert、mongoose 更新方式）
│   └── 7. 删除文档（deleteOne/deleteMany）
│
└── 第3章 进阶：索引与并发/扩展
    ├── 8. 索引（索引概念、createIndex、B-树存储、最佳实践）
    ├── 9. [扩展]mongoose的并发版本管理（__v 版本号、脏数据、VersionError、mongoose-update-if-current 插件）
    ├── 10. [补充]mongodb的分布式架构（分布式架构原理图）
    └── 11. [补充]虚拟属性和模型方法（virtual 虚拟属性、实例方法/静态方法）
```

> 注：原目录为 1~11 的平铺编号，整理时按「入门 → 建模与 CRUD → 进阶」语义合并为三个大模块，模块内保留原章节顺序与编号。

---

## 2. 章节逻辑说明

这门课是一条**「从认识 MongoDB 到用 Mongoose 在 Node 中稳定操作文档数据库」主线**，先建立「文档型数据库」的心智模型，再用 Mongoose 把 Schema/Model/CRUD 的工程化能力补齐，最后用索引、并发版本管理、虚拟属性等进阶主题收尾。

### 先讲什么：认识 MongoDB（第1章）

- **1. mongodb的安装**：Windows/macOS 安装、Robo 3T（类似 navicat 的图形客户端），并讲清三个基础概念——`db`（库）、`collection`（集合≈表）、`document`（文档≈记录），以及 MongoDB 作为 nosql + 文档型数据库的特点（无 SQL、学习成本低、存取快、结构灵活但难以约束）。
- **2. mongodb的基本操作**：进入 `mongo` shell，导览原生常用命令（`show dbs`、`use`、`show collections`、`insertOne/find/updateOne/deleteOne` 等）。

**为什么先讲**：先把「数据库长什么样、怎么连、怎么手动玩一下」跑通，建立对文档/集合/主键（`ObjectId` 原理）的直观认知，后面用代码操作时才不会陌生。

### 再讲什么：用 Mongoose 建模并完成完整 CRUD（第2章）

这是课程的主体，严格按 **「定义结构 → 增 → 查 → 改 → 删」** 的顺序展开：

- **3. schema和model**：从原生 `mongodb` 驱动引出 `mongoose`，讲清 Schema（描述字段、类型、约束）与 Model（对应集合中的文档）的关系，并设计出贯穿全课的「用户 / 用户操作」两个模型。
- **4. 新增文档**：原生 `insertOne/insertMany` vs mongoose `save/create`；重点讲新增的细节——自动 `_id`、自动 `__v`（埋下第9章并发管理的伏笔）、`validateBeforeSave` 验证机制、`ValidationError` 与 `unique` 唯一索引的区别。
- **5. 文档查询**：原生 `find` 返回游标（`next/hasNext/skip/limit/sort/count`，链式调用固定按 `sort → skip → limit` 执行）、`filter` 查询条件与 `$or/$in/$gt/$lt` 等操作符、`projection` 投影规则；再到 mongoose 的 `findById/findOne/find` 与 `DocumentQuery` 链式调用，以及 mongoose 与原生的查询差异。
- **6. 文档更新**：更新操作符（字段类 `$set/$inc/$mul/$rename/$unset`、数组类 `$addToSet/$push/$each/$pull/$`）、`upsert` 配置；mongoose 两种更新方式（实例 save 自动 diff、`updateOne/updateMany`）及其与原生的差异（字符串 `_id`、可省 `$set`、默认不触发验证需 `runValidators`）。
- **7. 删除文档**：原生与 mongoose 的 `deleteOne/deleteMany`。

**为什么这么安排**：先有 Schema/Model 这个「数据的形状」，才能谈对数据的增删改查；而 CRUD 内部按「增 → 查 → 改 → 删」走，是最符合「先有数据再处理数据」的学习路径。第4章刻意埋下的 `__v`/验证细节，恰好为第9章的并发版本管理做了铺垫。

### 最后讲什么：进阶与扩展（第3章）

- **8. 索引**：索引概念（像目录）、`createIndex`、`unique` 唯一索引、B-树存储、索引相关命令、最佳实践（大数据量/常用查询排序字段用索引、避免运行时频繁建删）。
- **9. [扩展]并发版本管理**：并发请求下的脏数据问题、mongoose 的脏数据判定、`__v` 版本号 + `increment` + `VersionError` 机制（仅对数组生效）、`mongoose-update-if-current` 插件（扩展到所有字段）。
- **10. [补充]分布式架构**：MongoDB 分布式架构原理图（导览性质）。
- **11. [补充]虚拟属性和模型方法**：`virtual` 虚拟属性（不持久化、getter）、`schema.methods` 实例方法与 `schema.static` 静态方法。

**为什么放最后**：索引是「数据量大了之后才需要」的性能优化；并发版本管理依赖第4章对 `__v` 的认知；分布式架构与虚拟属性/模型方法属于了解性扩展。这些都是在「会用 CRUD 之后」才回头看的高阶内容。

### 主线一句话总结

**认识 MongoDB（文档/集合/概念 + shell）→ 用 Mongoose 建模并打通 CRUD（Schema/Model → 增 → 查 → 改 → 删）→ 进阶（索引 / 并发版本 / 分布式 / 虚拟属性）**，最终目标是能在 Node 项目中用 mongoose 规范、稳定地操作 MongoDB。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：在 Node/Express/Egg 后端项目中用 mongoose 接入 MongoDB 作为数据源（定义 Schema/Model + 完整 CRUD）；为内容型、日志型、结构灵活的业务选择文档数据库方案；做 Mock 数据、爬虫数据落库、配置中心等场景。
- **和实际项目的关系**：第2章的 Schema 建模 + CRUD 是真实项目数据访问层的标准写法，可直接复用；第8章索引、第9章并发版本管理是数据量上升、并发写入后必然遇到的工程化问题。
- **和其他课程的关系**：是《Node》课程「数据库」部分的延伸——Node 课讲了 SQL（mysql + Sequelize），本课讲 NoSQL（mongodb + mongoose），两套 ORM 思路可对照复习；也为《Egg》中 `mongoose` 接入（egg-mongoose）打基础。
- **面试/教学高频场景**：MongoDB 与关系型数据库的概念对应（db/collection/document）、`ObjectId` 生成原理（时间戳+机器码+进程ID+自增量）、查询操作符（`$or/$in/$gt/$lt`）、索引与 B-树、mongoose 的 `__v` 并发版本管理机制。

---

## 4. 临时补充

- 原课程目录是 1~11 的平铺结构（每节一个 git 分支，见各章 `readme.md`），整理时按语义归为「入门 / 建模与 CRUD / 进阶」三大模块，但章节编号严格保留原顺序。
- 全课始终对照「**mongodb 原生操作 vs mongoose 操作**」两条线讲解（最典型见第5章笔记中的对比图），复习时可把第4~7章的原生写法和 mongoose 写法并列对照，理解 mongoose 在原生之上做了哪些封装（自动 `_id`/`__v`、字符串 `_id`、可省 `$set`、链式 `DocumentQuery`、验证、populate 关联等）。
- 第4章埋下的 `__v` 字段伏笔，到第9章才完整解释其并发版本管理用途；第9章又顺势引出 `mongoose-update-if-current` 插件，复习时建议 4 → 9 连起来看。
- 第2、5、6、7、8、9、10、11 章均带有 `models/`、`index.js`、`backup/`、`addTestUsers.js` 等可运行代码，第9、10、11 章还带有 `mongoose-update-if-current` 插件子目录，复习并发管理与虚拟属性时可直接回这些章节跑代码。
- 第10章「分布式架构」与第11章「虚拟属性和模型方法」在原目录中标注为 `[补充]`，第9章标注为 `[扩展]`，内容以原理图/示例为主，属于了解性补充，不作为核心考核点。
