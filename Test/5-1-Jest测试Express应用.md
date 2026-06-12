# Jest 测试 Express 应用

我们这里要测试的项目，是之前 React 篇章中开发的 coderstation 服务器：

- 服务器框架：Express
- 数据库：MongoDB

这里我们针对 Express 服务器端应用进行测试，主要是测试该应用所提供的端口是否能够正常的工作，会连接真实的数据库，这里实际上是属于一个集成测试。

这里在进行测试的时候，需要安装如下的依赖：

```js
npm i jest supertest
```

这里简单的介绍一下 *supertest*，这是一个用于测试 *HTTP* 服务器的 *Node.js* 库，它提供了一个高级抽象，让你可以轻松地发送 *HTTP* 请求并对响应进行断言。*Supertest* 通常与测试框架（如 *Jest、Mocha* 等）一起使用，以便编写端到端的 *API* 测试。

*Supertest* 的一些主要特点包括：

1. 链式语法：*Supertest* 使用流畅的链式语法，让你可以轻松地编写和阅读测试代码。例如，你可以通过 .*get*('/') 发送 *GET* 请求，通过 .*expect*(200) 验证响应状态码等。
2. 内置断言：*Supertest* 支持许多内置断言，如响应状态码、内容类型、响应头等。你可以使用这些断言来验证 *API* 响应是否符合预期。
3. 与测试框架集成：*Supertest* 可以与流行的测试框架（如 *Jest、Mocha* 等）无缝集成，使你可以使用熟悉的测试工具编写端到端的 *API* 测试。
4. 支持 *Promises* 和 *async/await*：*Supertest* 支持 *Promises* 和 *async/await*，让你可以编写更简洁、可读的异步测试代码。

官方文档地址：*https://github.com/ladjs/supertest*



安装完依赖之后，在项目的根目录下面创建 tests 目录，用来放置我们的测试文件，接下来就可以针对每一个模块进行一个接口的集成测试。

假设我们这里对 issue 这个模块进行测试，测试代码如下：

```js
const app = require("../app");
const request = require("supertest");
const mongoose = require("mongoose");

// 当前测试套件里面的所有的测试用例跑完之后关闭数据库连接
afterAll(async () => {
  await mongoose.connection.close();
});

// 接下来来书写我们的测试用例

test("测试分页获取问题", async () => {
  // act 行为
  const res = await request(app).get("/api/issue").query({
    current: 1,
    pageSize: 5,
    issueStatus: true,
  });

  // assertion 断言
  expect(res.statusCode).toBe(200);
  expect(res.body.data.currentPage).toBe(1);
  expect(res.body.data.eachPage).toBe(5);
  expect(res.body.data.count).toBe(21);
  expect(res.body.data.totalPage).toBe(5);
});

test("根据id获取其中一个问答的详情", async () => {
  const res = await request(app).get("/api/issue/6357abfb37fe7a1aab3aeae8");

  // 进行断言
  expect(res.statusCode).toBe(200);
  expect(res.body.data._id).toBe("6357abfb37fe7a1aab3aeae8");
  expect(res.body.data.issueTitle).toBe(
    "如果遇到组件使用到主题相关颜色一般怎么处理比较好?"
  );
});

test("新增问答", async () => {

    // 这里有一个注意点：
    // 由于我们连接的是真实的数据库，发送的也是真实的请求
    // 因此这里会真实的修改数据库的状态

    // 如果不想真实的数据库受影响：
    // 解决方案：
    // 1. 使用单独的测试数据库（推荐此方案）
    // 2. 使用模拟

  const res = await request(app).post("/api/issue").send({
    issueTitle: "hello",
    issueContent: "thank you",
    userId: "6343d93b0513e856480148c9",
    typeId: "6344fbcd890d0f907da2193e",
  });

  expect(res.statusCode).toBe(200);
  expect(res.body.data.issueTitle).toBe("hello");
  expect(res.body.data.issueContent).toBe("thank you");
  expect(res.body.data.scanNumber).toBe(0);
  expect(res.body.data.commentNumber).toBe(0);
  expect(res.body.data.issueStatus).toBe(false);
  expect(res.body.data.userId).toBe("6343d93b0513e856480148c9");
  expect(res.body.data.typeId).toBe("6344fbcd890d0f907da2193e");

  // 新增失败的验证
  const res2 = await request(app).post("/api/issue").send({
    issueTitle: "hello",
    issueContent: "thank you",
  });

  expect(res2.body.code).toBe(406);
  expect(res2.body.msg).toBe("数据验证失败");
  expect(res2.body.data).toBeNull();

});
```

这里面有这么几个注意点：

1. 在测试套件跑完之后，可能会存在一些服务一直处于开启状态，这里我们可以给 jest 添加一个配置

```js
"scripts": {
    // ...
    "test": "jest --detectOpenHandles"
 },
```

添加该配置后，jest 就能够侦查是哪个服务没有在测试跑完后关闭，比如我们上面的例子，就是 mongodb 没有关闭



2. 我们需要在测试完成后，关闭 mongodb 服务，可以在生命周期钩子函数中（afterAll）里面做关闭操作

```js
// 当前测试套件里面的所有的测试用例跑完之后关闭数据库连接
afterAll(async () => {
  await mongoose.connection.close();
});
```



3. 在进行测试的时候，会发送真实的请求，并且会连接真实的数据库，这意味着会更改数据库的状态

如果不想真实的数据库状态受影响，有如下两种方案：

- 使用单独的测试数据库
- 使用模拟方案

推荐使用第一种（测试数据库）方案，因为这里本来就是在做集成测试。

---

## 关键代码

### package.json

项目依赖配置，包含 express、mongoose、jest、supertest 等核心依赖。

```js
{
  "name": "app",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www",
    "test": "jest --detectOpenHandles"
  },
  "dependencies": {
    "cookie-parser": "~1.4.4",
    "debug": "~2.6.9",
    "dotenv": "^16.0.3",
    "express": "~4.16.1",
    "express-jwt": "^7.7.5",
    "express-session": "^1.17.3",
    "http-errors": "~1.6.3",
    "jest": "^29.5.0",
    "jsonwebtoken": "^8.5.1",
    "md5": "^2.3.0",
    "mongoose": "^6.6.5",
    "morgan": "~1.9.1",
    "multer": "^1.4.5-lts.1",
    "supertest": "^6.3.3",
    "svg-captcha": "^1.4.0",
    "validate.js": "^0.13.1"
  }
}
```

### app.js

Express 应用入口文件，负责创建服务器实例、注册中间件、挂载路由以及统一错误处理。

```js
const createError = require("http-errors");
const express = require("express");
const path = require("path");
const cookieParser = require("cookie-parser");
const logger = require("morgan");
const session = require("express-session");
const { ServiceError, UnknownError } = require("./utils/errors");

// 默认读取项目根目录下的 .env 环境变量文件
require("dotenv").config();
// 进行数据库初始化
require("./db/init");

// 引入路由
const bookRouter = require("./routes/book");
const issueRouter = require("./routes/issue");
const adminRouter = require("./routes/admin");
const captchaRouter = require("./routes/captcha");
const userRouter = require("./routes/user");
const typeRouter = require("./routes/type");
const interviewRouter = require("./routes/interview");
const commentRouter = require("./routes/comment");
const uploadRouter = require("./routes/upload");

// 创建服务器实例
const app = express();

// 使用 session 中间件
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: true,
    saveUninitialized: true,
  })
);

app.use(logger("dev"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, "public")));

// 使用路由中间件
app.use("/api/book", bookRouter);
app.use("/api/issue", issueRouter);
app.use("/api/admin", adminRouter);
app.use("/api/user", userRouter);
app.use("/api/type", typeRouter);
app.use("/api/comment", commentRouter);
app.use("/api/interview", interviewRouter);
app.use("/api/upload", uploadRouter);
app.use("/res/captcha", captchaRouter);

// catch 404 and forward to error handler
app.use(function (req, res, next) {
  next(createError(404));
});

// 错误处理，一旦发生了错误，就会到这里来
app.use(function (err, req, res, next) {
  if (err instanceof ServiceError) {
    res.send(err.toResponseJSON());
  } else {
    res.send(new UnknownError().toResponseJSON());
    throw err;
  }
});

module.exports = app;
```

### routes/issue.js

问答模块路由，定义了问答的 CRUD 接口（GET 分页查询、GET 详情、POST 新增、DELETE 删除、PATCH 修改）。

```js
/**
 * 问答模块对应二级路由
 */

const express = require("express");
const router = express.Router();

// 引入业务层方法
const {
  addIssueService,
  findIssueByPageService,
  findIssueByIdService,
  updateIssueService,
  deleteIssueService,
  searchIssueByPageService,
} = require("../services/issueService");

const { formatResponse } = require("../utils/tools");

/**
 * 根据分页获取问答信息
 */
router.get("/", async function (req, res) {
  const result = await findIssueByPageService(req.query);
  res.send(formatResponse(0, "", result));
});

/**
 * 根据 id 获取其中一个问答具体信息
 */
router.get("/:id", async function (req, res) {
  const result = await findIssueByIdService(req.params.id);
  res.send(formatResponse(0, "", result));
});

/**
 * 新增问答
 */
router.post("/", async function (req, res, next) {
  const result = await addIssueService(req.body);
  if (result && result._id) {
    res.send(formatResponse(0, "", result));
  } else {
    next(result);
  }
});

/**
 * 根据 id 删除某一个问答
 */
router.delete("/:id", async function (req, res) {
  const result = await deleteIssueService(req.params.id);
  res.send(formatResponse(0, "", result));
});

/**
 * 根据 id 修改某一个问答
 */
router.patch("/:id", async function (req, res) {
  const result = await updateIssueService(req.params.id, req.body);
  res.send(formatResponse(0, "", result));
});

module.exports = router;
```

### services/issueService.js

问答模块业务逻辑层，负责数据验证、默认值填充以及关联评论的级联删除等业务处理。

```js
const {
  findIssueByPageDao,
  findIssueByIdDao,
  addIssueDao,
  deleteIssueDao,
  updateIssueDao,
  searchIssueByPageDao
} = require("../dao/issueDao");
const {
  findIssueCommentByIdDao,
  deleteCommentDao,
} = require("../dao/commentDao");
const { validate } = require("validate.js");
const { issueRule } = require("./rules");
const { ValidationError } = require("../utils/errors");

/**
 * 按分页查询问答
 */
module.exports.findIssueByPageService = async function (queryObj) {
  return await findIssueByPageDao(queryObj);
};

/**
 * 根据 id 获取其中一个问答信息
 */
module.exports.findIssueByIdService = async function (id) {
  return await findIssueByIdDao(id);
};

/**
 * 新增问答
 */
module.exports.addIssueService = async function (newIssueInfo) {
  // 首先进行同步的数据验证
  const validateResult = validate.validate(newIssueInfo, issueRule);
  if (!validateResult) {
    // 验证通过

    // 添加其他信息
    newIssueInfo.scanNumber = 0; // 浏览数，默认为 0
    newIssueInfo.commentNumber = 0; // 评论数，默认为 0
    // 上架日期
    newIssueInfo.issueDate = new Date().getTime().toString();

    // 添加状态，默认是未过审状态
    newIssueInfo.issueStatus = false;

    return await addIssueDao(newIssueInfo);
  } else {
    // 数据验证失败
    return new ValidationError("数据验证失败");
  }
};

/**
 * 删除某一个问答
 */
module.exports.deleteIssueService = async function (id) {
  // 首先需要删除该问答对应的评论

  // 获取该 issueId 对应的所有评论
  const commentResult = await findIssueCommentByIdDao(id);

  for (let i = 0; i < commentResult.length; i++) {
    await deleteCommentDao(commentResult[i]._id);
  }

  // 接下来再删除该问答
  return await deleteIssueDao(id);
};

/**
 * 修改某一个问答
 */
module.exports.updateIssueService = async function (id, newInfo) {
  return await updateIssueDao(id, newInfo);
};
```

### dao/issueDao.js

问答模块数据访问层，封装了 mongoose 对 issue 集合的增删改查操作。

```js
// 引入模型
const issueModel = require("../models/issueModel");

/**
 * 分页查找问答
 */
module.exports.findIssueByPageDao = async function (queryObj) {
  const pageObj = {
    currentPage: Number(queryObj.current),
    eachPage: Number(queryObj.pageSize),
  };

  const queryCondition = {};
  if (queryObj.issueTitle) {
    queryCondition.issueTitle = new RegExp(queryObj.issueTitle, "i");
  }
  if (queryObj.typeId) {
    queryCondition.typeId = queryObj.typeId;
  }
  if (queryObj.issueStatus != undefined) {
    queryCondition.issueStatus = queryObj.issueStatus;
  }

  pageObj.count = await issueModel.countDocuments(queryCondition); // 数据总条数
  pageObj.totalPage = Math.ceil(pageObj.count / pageObj.eachPage); // 总页数
  pageObj.data = await issueModel
    .find(queryCondition)
    .skip((pageObj.currentPage - 1) * pageObj.eachPage)
    .sort({ issueDate: -1 })
    .limit(pageObj.eachPage);
  return pageObj;
};

/**
 * 根据 id 获取其中一个问答的详情
 */
module.exports.findIssueByIdDao = async function (id) {
  return issueModel.findOne({
    _id: id,
  });
};

/**
 * 新增问答
 */
module.exports.addIssueDao = async function (newIssueInfo) {
  return await issueModel.create(newIssueInfo);
};

/**
 * 根据 id 删除问答
 */
module.exports.deleteIssueDao = async function (id) {
  return issueModel.deleteOne({
    _id: id,
  });
};

/**
 * 根据 id 修改问答
 */
module.exports.updateIssueDao = async function (id, newInfo) {
  return issueModel.updateOne({ _id: id }, newInfo);
};
```

### tests/issue.test.js

问答模块集成测试，使用 supertest 发送真实 HTTP 请求测试分页查询、详情获取和新增接口。

```js
const app = require("../app");
const request = require("supertest");
const mongoose = require("mongoose");

// 当前测试套件里面的所有的测试用例跑完之后关闭数据库连接
afterAll(async () => {
  await mongoose.connection.close();
});

// 接下来来书写我们的测试用例

test("测试分页获取问题", async () => {
  // act 行为
  const res = await request(app).get("/api/issue").query({
    current: 1,
    pageSize: 5,
    issueStatus: true,
  });

  // assertion 断言
  expect(res.statusCode).toBe(200);
  expect(res.body.data.currentPage).toBe(1);
  expect(res.body.data.eachPage).toBe(5);
  expect(res.body.data.count).toBe(21);
  expect(res.body.data.totalPage).toBe(5);
});

test("根据id获取其中一个问答的详情", async () => {
  const res = await request(app).get("/api/issue/6357abfb37fe7a1aab3aeae8");

  // 进行断言
  expect(res.statusCode).toBe(200);
  expect(res.body.data._id).toBe("6357abfb37fe7a1aab3aeae8");
  expect(res.body.data.issueTitle).toBe(
    "如果遇到组件使用到主题相关颜色一般怎么处理比较好?"
  );
});

test("新增问答", async () => {

    // 这里有一个注意点：
    // 由于我们连接的是真实的数据库，发送的也是真实的请求
    // 因此这里会真实的修改数据库的状态

    // 如果不想真实的数据库受影响：
    // 解决方案：
    // 1. 使用单独的测试数据库（推荐此方案）
    // 2. 使用模拟

  const res = await request(app).post("/api/issue").send({
    issueTitle: "hello",
    issueContent: "thank you",
    userId: "6343d93b0513e856480148c9",
    typeId: "6344fbcd890d0f907da2193e",
  });

  expect(res.statusCode).toBe(200);
  expect(res.body.data.issueTitle).toBe("hello");
  expect(res.body.data.issueContent).toBe("thank you");
  expect(res.body.data.scanNumber).toBe(0);
  expect(res.body.data.commentNumber).toBe(0);
  expect(res.body.data.issueStatus).toBe(false);
  expect(res.body.data.userId).toBe("6343d93b0513e856480148c9");
  expect(res.body.data.typeId).toBe("6344fbcd890d0f907da2193e");

  // 新增失败的验证
  const res2 = await request(app).post("/api/issue").send({
    issueTitle: "hello",
    issueContent: "thank you",
  });

  expect(res2.body.code).toBe(406);
  expect(res2.body.msg).toBe("数据验证失败");
  expect(res2.body.data).toBeNull();

});
```

### tests/book.test.js

书籍模块集成测试，测试分页获取书籍接口。

```js
const request = require("supertest");
const app = require("../app");
const mongoose = require("mongoose");

/* 测试完成后关闭数据库连接。*/
afterAll(async () => {
  await mongoose.connection.close();
});

test("测试分页获取书籍", async () => {
  const res = await request(app)
    .get("/api/book")
    .query({ current: 1, pageSize: 5 }); // 添加查询参数

  // 进行断言
  expect(res.statusCode).toBe(200);
  expect(res.body.data.currentPage).toBe(1);
  expect(res.body.data.eachPage).toBe(5);
  expect(res.body.data.count).toBe(20);
  expect(res.body.data.totalPage).toBe(4);
});
```

### db/connect.js

数据库连接配置文件，使用 mongoose 连接 MongoDB 数据库。

```js
/**
 * 该文件负责连接数据库
 */

const mongoose = require("mongoose");

// 定义链接数据库字符串
const dbURI = "mongodb://" + process.env.DB_HOST + "/" + process.env.DB_NAME;

// 连接
mongoose.connect(dbURI, { useNewUrlParser: true, useUnifiedTopology: true });

// 监听
mongoose.connection.on("connected", function () {
  console.log(`coderstation 数据库已经连接...`);
});
```

### utils/errors.js

自定义错误类，定义了业务错误基类 ServiceError 及其子类（ValidationError、ForbiddenError、UploadError、NotFoundError、UnknownError）。

```js
/**
 * 自定义错误
 * 当错误发生的时候，我们捕获到发生的错误，然后抛出我们自定义的错误
 */
const { formatResponse } = require("./tools");

/**
 * 业务处理错误基类
 */
class ServiceError extends Error {
  /**
   *
   * @param {*} message 错误消息
   * @param {*} code 错误的消息码
   */
  constructor(message, code) {
    super(message);
    this.code = code;
  }

  // 方法
  // 格式化的返回错误信息
  toResponseJSON() {
    return formatResponse(this.code, this.message, null);
  }
}

// 文件上传错误
exports.UploadError = class extends ServiceError {
  constructor(message) {
    super(message, 413);
  }
};

// 禁止访问错误
exports.ForbiddenError = class extends ServiceError {
  constructor(message) {
    super(message, 401);
  }
};

// 验证错误
exports.ValidationError = class extends ServiceError {
  constructor(message) {
    super(message, 406);
  }
};

// 无资源错误
exports.NotFoundError = class extends ServiceError {
  constructor() {
    super("not found", 406);
  }
};

// 未知错误
exports.UnknownError = class extends ServiceError {
  constructor() {
    super("server internal error", 500);
  }
};

module.exports.ServiceError = ServiceError;
```
