# 4-5 Express路由

## 知识要点

- 使用 `express.Router()` 创建模块化路由
- 将路由按功能模块拆分到独立文件
- 使用统一的响应格式封装（`getSendResult.js`）
- `asyncHandler` 统一处理异步错误

## 路由模块化

将路由按功能拆分到 `routes/api/` 目录下的独立文件中：

### routes/init.js（主路由入口）

```js
const express = require("express");
const app = express();

// 静态资源
const path = require("path");
const staticRoot = path.resolve(__dirname, "../public");
app.use(express.static(staticRoot));

// 解析请求体
app.use(express.urlencoded({ extended: true }));
app.use(express.json());

// 处理 api 的请求（按模块拆分）
app.use("/api/student", require("./api/student"));

// 处理错误的中间件
app.use(require("./errorMiddleware"));

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```

### routes/api/student.js（学生路由模块）

```js
const express = require("express");
const router = express.Router();
const stuServ = require("../../services/studentService");
const { asyncHandler } = require("../getSendResult");

router.get("/", asyncHandler(async (req, res) => {
  const page = req.query.page || 1;
  const limit = req.query.limit || 10;
  const sex = req.query.sex || -1;
  const name = req.query.name || "";
  return await stuServ.getStudents(page, limit, sex, name);
}));

router.get("/:id", asyncHandler(async (req, res) => {
  return await stuServ.getStudentById(req.params.id);
}));

router.post("/", asyncHandler(async (req, res, next) => {
  return await stuServ.addStudent(req.body);
}));

router.delete("/:id", asyncHandler(async (req, res, next) => {
  return await stuServ.deleteStudent(req.params.id);
}));

router.put("/:id", asyncHandler(async (req, res, next) => {
  return await stuServ.updateStudent(req.params.id, req.body);
}));

module.exports = router;
```

### 统一响应格式（getSendResult.js）

```js
exports.getErr = function (err = "server internal error", errCode = 500) {
  return {
    code: errCode,
    msg: err,
  };
};

exports.getResult = function (result) {
  return {
    code: 0,
    msg: "",
    data: result,
  };
};

// 统一异步错误处理包装器
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

## 路由设计模式

| 请求方法 | 路径 | 功能 |
|---------|------|------|
| GET | /api/student | 获取学生列表 |
| GET | /api/student/:id | 获取单个学生 |
| POST | /api/student | 添加学生 |
| DELETE | /api/student/:id | 删除学生 |
| PUT | /api/student/:id | 更新学生 |
