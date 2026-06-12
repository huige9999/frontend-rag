# 1-3 Node的模块化细节

## 知识要点

### CommonJS 模块化机制

Node.js 默认采用 **CommonJS** 模块规范：
- 使用 `require()` 导入模块
- 使用 `module.exports` 或 `exports` 导出模块

### require 函数的执行流程

1. 将模块路径转换为**绝对路径**
2. 判断是否已有**缓存**（`require.cache`），有则直接返回缓存结果
3. **读取**文件内容
4. 将代码**包裹**到一个函数中，该函数签名如下：

```js
function(module, exports, require, __dirname, __filename) {
  // 模块代码
}
```

5. 创建 `module` 对象和 `exports` 引用（`const exports = module.exports`）
6. 调用该函数，上下文绑定为 `module.exports`
7. 返回 `module.exports`

### `exports` 与 `module.exports` 的区别

- `exports` 是 `module.exports` 的引用
- 直接给 `exports` 赋值会断开引用关系
- `this` 在模块顶层指向 `module.exports`

---

## 代码示例

### 模块导出（myModule.js）

以下代码演示了 `exports`、`module.exports` 和 `this` 的使用：

```js
console.log("当前模块路径：", __dirname);
console.log("当前模块文件：", __filename);
exports.c = 3;
module.exports = {
  a: 1,
  b: 2
};
this.m = 5;

console.log(this === exports); // true
```

> **注意**：`module.exports` 被重新赋值后，`exports.c = 3` 添加的属性不会出现在最终导出结果中，因为最终返回的是 `module.exports`。

### 模块导入（index.js）

```js
const result = require("./myModule");
console.log(result);
// 输出: { a: 1, b: 2 }
```

### require 函数模拟实现

```js
function require(modulePath) {
  // 1. 将modulePath转换为绝对路径
  // 2. 判断是否该模块已有缓存
  // if(require.cache["绝对路径"]){
  //   return require.cache["绝对路径"].result;
  // }

  // 3. 读取文件内容
  // 4. 包裹到一个函数中

  function __temp(module, exports, require, __dirname, __filename) {
    console.log("当前模块路径：", __dirname);
    console.log("当前模块文件：", __filename);
    exports.c = 3;
    module.exports = {
      a: 1,
      b: 2
    };
    this.m = 5;
  }

  // 6. 创建module对象
  module.exports = {};
  const exports = module.exports;

  __temp.call(module.exports, module, exports, require, module.path, module.filename);
  return module.exports;
}

require.cache = {};
```

### 模块查找规则（ab.js / index copy.js）

```js
// 1. 以 ./ 开头：相对路径查找
require("./ab");       // 查找当前目录下的 ab.js
require("./src");      // 查找当前目录下 src.js 或 src/index.js

// 2. 不以 ./ 开头：查找 node_modules
require("abc");        // 查找 node_modules/abc/index.js
require("abc/index");  // 查找 node_modules/abc/index.js
```
