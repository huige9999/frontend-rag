# 4-2 nodemon

## 知识要点

- nodemon 是一个开发工具，用于监听文件变化自动重启 Node 服务器
- 安装：`npm install -g nodemon` 或 `npm install --save-dev nodemon`
- 使用 nodemon 代替 node 命令启动应用：`nodemon index.js`
- nodemon 会监听项目目录下的文件变化，当文件被修改后自动重启服务
- 可通过 `nodemon.json` 配置文件自定义监听规则

## 使用示例

### 启动应用

```shell
# 传统方式，修改代码需手动重启
node index.js

# 使用 nodemon，修改代码自动重启
nodemon index.js
```

### package.json 配置

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  }
}
```

运行 `npm run dev` 即可启动带自动重启的开发服务器。

## 本节代码

本节代码与 4-1 基本一致，主要变化是引入了 nodemon 作为开发工具：

```js
require("./init");
const express = require("express");
const app = express();
console.log(process.env.NODE_ENV);
app.get("*", (req, res) => {
  res.send("abc123");
});

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```

可以看到 `process.env.NODE_ENV` 用于获取当前运行环境，配合 nodemon 可以方便地在开发环境中调试。
