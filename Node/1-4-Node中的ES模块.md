# 1-4 【扩展】Node中的ES模块

## 知识要点

### ES 模块（ESM）基础

Node.js 从 v12 开始正式支持 **ES Modules**：
- 文件扩展名使用 `.mjs`，或在 `package.json` 中设置 `"type": "module"`
- 使用 `import` / `export` 语法
- 与 CommonJS 不兼容

### ESM 与 CommonJS 的区别

| 特性 | CommonJS | ES Modules |
|------|----------|------------|
| 导出 | `module.exports` / `exports` | `export` / `export default` |
| 导入 | `require()` | `import` |
| 加载方式 | 同步运行时加载 | 编译时静态分析 |
| 文件扩展名 | `.js` | `.mjs` 或配置 `"type": "module"` |

---

## 代码示例

### 模块导出（a.mjs）

```js
export default 5;
export const a = 1;
```

### 动态导入（index.mjs）

ES 模块支持通过 `import()` 函数进行**动态导入**，返回一个 Promise：

```js
import("./a.mjs").then(r => console.log(r));
// 输出: { default: 5, a: 1 }
```

> **注意**：在 ES 模块中不能使用 `require()`，不能使用 `module.exports` 和 `exports`。
