# 02. ES Module

## 课程概述

ES Module 是 JavaScript 官方的模块化标准，提供了在浏览器和 Node.js 环境中统一的模块化解决方案。

## 学习目标

- 理解 ES Module 的基本概念和特点
- 掌握模块的导出（export）和导入（import）语法
- 了解具名导出和默认导出的区别
- 学会使用静态导入和动态导入
- 能够在实际项目中应用模块化开发

## 模块化对比

### CommonJS vs ES Module

| 特性 | CommonJS | ES Module |
|------|----------|-----------|
| 标准类型 | 社区规范 | 官方标准 |
| 支持环境 | Node.js | Node.js、浏览器 |
| 依赖类型 | 动态依赖 | 静态依赖、动态依赖 |
| 加载方式 | 运行时加载 | 编译时加载（静态）、运行时加载（动态） |
| 输出值 | 值的拷贝 | 值的引用（动态绑定） |

## ES Module 导出语法

### 导出方式分类

ES Module 提供两种导出方式：

1. **具名导出（Named Export）**：可以导出多个成员
2. **默认导出（Default Export）**：只能导出一个成员

一个模块可以同时使用两种导出方式，最终会合并为一个「对象」导出。

### 具名导出

```javascript
// 方式1：直接导出声明
export const a = 1;
export function b() {}
export const c = () => {};

// 方式2：先声明后导出
const d = 2;
export { d };

// 方式3：导出时使用别名
const k = 10;
export { k as temp };
```

### 默认导出

```javascript
// 方式1：直接导出值
export default 3;

// 方式2：导出函数
export default function() {}

// 方式3：先声明后导出
const e = 4;
export { e as default };
```

### 混合导出

```javascript
const f = 4, g = 5, h = 6;
export { f, g, h as default };

// 最终导出的对象结构：
/*
{
  f: 4,
  g: 5,
  default: 6
}
*/
```

### 导出注意事项

⚠️ **导出代码必须为顶级代码，不可放到代码块中**

```javascript
// ❌ 错误示例
if (condition) {
  export const a = 1; // 语法错误
}

// ✅ 正确示例
const a = 1;
if (condition) {
  // 可以在这里使用 a
}
```

## ES Module 导入语法

### 静态导入

静态导入必须在代码的顶部，不能放在代码块中。

```javascript
// 仅运行模块，不导入任何内容
import "模块路径";

// 导入具名导出的成员
import { a, b } from "模块路径";

// 导入默认导出的成员
import c from "模块路径";

// 同时导入默认和具名成员
import c, { a, b } from "模块路径";

// 导入整个模块对象
import * as obj from "模块路径";

// 导入时使用别名
import { a as temp1, b as temp2 } from "模块路径";

// 导入 default 并使用别名（default 是关键字）
import { default as a } from "模块路径";
```

### 动态导入

动态导入返回一个 Promise，可以在任意位置使用。

```javascript
// 动态导入返回 Promise
import("模块路径")
  .then(module => {
    // module 是模块对象
    console.log(module);
  });

// 使用 async/await
const module = await import("模块路径");
```

### 导入注意事项

⚠️ **静态导入代码必须在代码顶端，不可放入代码块中**

⚠️ **静态导入的代码绑定的符号是常量，不可更改**

```javascript
// ❌ 错误示例
if (condition) {
  import { a } from './module.js'; // 语法错误
}

// ❌ 错误示例
import { a } from './module.js';
a = 2; // TypeError: Assignment to constant variable
```

## 实战示例

### 示例1：基础导出导入

**math.js（导出模块）**
```javascript
// 导出两个函数 sum，isOdd
export default {
  sum(a, b) {
    return a + b;
  },
  isOdd(n) {
    return n % 2 !== 0;
  },
};
```

**index.js（导入模块）**
```javascript
// 静态导入
// import math from './math.js';
// const result = math.sum(1, 2);
// console.log(result);

// 动态导入
setTimeout(async () => {
  const m = await import('./math.js');
  const math = m.default;
  const result = math.isOdd(1, 2);
  console.log(result);
}, 1000);
```

### 示例2：具名导出与别名

**test.js**
```javascript
// 使用别名导出
const b = 1;
export { b as a };

// 最终导出对象：{ a: 1 }
```

**index.js**
```javascript
// 方式1：直接导入
import { a } from './test.js';
console.log(a);

// 方式2：导入时使用别名
import { a as b } from './test.js';
console.log(b);

// 方式3：导入整个模块对象
import * as m from './test.js';
console.log(m); // { a: 1 }
```

### 示例3：混合导出

**test.js**
```javascript
export const a = 1;
export const b = 2;
export function c() {}

export default {
  a: 1,
  b: 2,
};

// 最终导出对象：
/*
{
  a: 1,
  b: 2,
  c: function() {},
  default: { a: 1, b: 2 }
}
*/
```

**index.js**
```javascript
// 仅导入默认导出
import test from './test.js';
console.log(test); // { a: 1, b: 2 }

// 导入默认和具名导出
import test, { a, b } from './test.js';
console.log(test, a, b);

// 导入整个模块对象
import * as test from './test.js';
console.log(test);

// 仅运行模块，不导入任何内容
import './test.js';
```

### 示例4：综合项目实战

**项目结构**
```
综合练习/
├── index.html
└── js/
    ├── api/
    │   └── user.js       # 用户相关 API
    ├── doms.js           # DOM 元素管理
    ├── login.js          # 登录逻辑
    └── main.js           # 入口文件
```

**api/user.js（API 模块）**
```javascript
// 负责和用户相关的远程请求
// 具名导出一个登录方法
export async function login(loginId, loginPwd) {
  const resp = await fetch('https://study.duyiedu.com/api/user/login', {
    method: 'POST',
    headers: {
      'content-type': 'application/json',
    },
    body: JSON.stringify({ loginId, loginPwd }),
  }).then((resp) => resp.json());
  return resp.data;
}
```

**doms.js（DOM 元素模块）**
```javascript
// 导出所有可能用到的dom元素
export const formContainer = document.getElementById('formContainer');
export const userName = document.getElementById('userName');
export const userPassword = document.getElementById('userPassword');
export const btnSubmit = document.getElementById('btnSubmit');
```

**login.js（登录逻辑模块）**
```javascript
import * as doms from './doms.js';
import { login } from './api/user.js';

// 导出一个函数，调用该函数，会自动获取文本框的值完成登录
let isLoginning = false; // 当前是否正在登录中

export default async function() {
  if (isLoginning) {
    return; // 正在登录中
  }
  isLoginning = true;
  doms.btnSubmit.value = '登录中...';
  
  // 1. 获取当前的账号密码
  const loginId = doms.userName.value;
  const loginPwd = doms.userPassword.value;

  // 2. 做一些简单的验证
  if (!loginId) {
    alert('请填写账号');
    return;
  }
  if (!loginPwd) {
    alert('请填写密码');
    return;
  }
  
  // 3. 远程请求
  const resp = await login(loginId, loginPwd);
  if (resp) {
    alert(`登录成功，欢迎你，${resp.nickname}`);
  } else {
    alert('登录失败');
  }
  
  isLoginning = false;
  doms.btnSubmit.value = '登录';
}
```

**main.js（入口模块）**
```javascript
// js入口模块
import login from './login.js';
import { formContainer } from './doms.js';

formContainer.onsubmit = function(e) {
  e.preventDefault();
  login();
};
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>登录界面</title>
  <link rel="stylesheet" href="./css/index.css" />
</head>
<body>
  <div class="form-container">
    <h1 class="title">用户登录</h1>
    <form id="formContainer" class="form-wrapper">
      <div class="form-item">
        <input
          id="userName"
          class="text"
          type="text"
          placeholder="请输入登录账号"
          autocomplete="new-password"
        />
      </div>
      <div class="form-item password-container">
        <input
          id="userPassword"
          type="password"
          placeholder="请输入登录密码"
          autocomplete="new-password"
        />
      </div>
      <input id="btnSubmit" type="submit" class="signin" value="登录" />
    </form>
  </div>
  <!-- 使用 type="module" 启用 ES Module -->
  <script src="./js/main.js" type="module"></script>
</body>
</html>
```

## 在浏览器中使用 ES Module

在 HTML 中使用 ES Module 需要注意以下几点：

1. **script 标签必须添加 `type="module"` 属性**
2. **模块文件会自动延迟执行，类似于 defer 属性**
3. **模块代码运行在严格模式下**
4. **导入的模块路径必须是完整的 URL 或相对路径**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>ES Module Demo</title>
</head>
<body>
  <!-- 正确：使用 type="module" -->
  <script src="./js/index.js" type="module"></script>
  
  <!-- 错误：不指定 type="module" 会报错 -->
  <!-- <script src="./js/index.js"></script> -->
</body>
</html>
```

## 模块化开发的优势

### 1. 命名空间隔离
每个模块都有独立的作用域，避免全局变量污染。

### 2. 代码组织清晰
将相关功能组织在同一个模块中，代码结构更清晰。

### 3. 便于维护和复用
模块可以独立开发、测试和维护，提高代码复用性。

### 4. 按需加载
支持动态导入，可以实现代码分割和按需加载。

### 5. 静态分析
静态导入的结构可以在编译时确定，便于工具进行静态分析和优化。

## 练习题

### 练习1：具名导出与导入

创建以下文件结构：
```
练习题1/
├── index.html
├── index.js
└── test.js
```

**任务**：
1. 在 `test.js` 中使用别名导出变量 `a`（值为 1）
2. 在 `index.js` 中用三种不同方式导入并使用 `a`
3. 观察导入的模块对象结构

### 练习2：混合导出

创建以下文件结构：
```
练习题2/
├── index.html
├── index.js
└── test.js
```

**任务**：
1. 在 `test.js` 中同时导出具名成员（a, b, c）和默认导出
2. 在 `index.js` 中尝试不同的导入方式
3. 理解默认导出和具名导出的区别

### 练习3：综合项目

创建一个完整的登录系统：

1. **user.js**：封装用户登录 API
2. **doms.js**：管理所有 DOM 元素
3. **login.js**：实现登录逻辑
4. **main.js**：程序入口

要求：
- 使用 ES Module 组织代码
- 合理划分模块职责
- 正确使用导出和导入

## 最佳实践

### 1. 模块划分原则

- **单一职责**：每个模块只负责一个功能
- **高内聚低耦合**：模块内部紧密相关，模块间依赖最小化
- **合理分层**：按功能层次组织模块（API、业务逻辑、UI 等）

### 2. 导出建议

- **优先使用具名导出**：便于代码补全和重构
- **默认导出用于模块主体**：如主要的类或函数
- **导出稳定的公共接口**：避免频繁修改导出的 API

### 3. 导入建议

- **按需导入**：只导入需要的成员，减少内存占用
- **统一导入风格**：项目中保持一致的导入方式
- **使用路径别名**：配置构建工具支持路径别名，简化导入路径

### 4. 性能优化

- **使用动态导入**：实现代码分割和懒加载
- **预加载关键模块**：使用 `<link rel="modulepreload">` 预加载
- **避免循环依赖**：合理设计模块结构，避免循环引用

## 兼容性说明

### 浏览器支持

现代浏览器都支持 ES Module：
- Chrome 61+
- Firefox 60+
- Safari 10.1+
- Edge 16+

### Node.js 支持

Node.js 从 12.0 版本开始稳定支持 ES Module：
- 使用 `.mjs` 扩展名
- 或在 `package.json` 中设置 `"type": "module"`

### 降级方案

对于不支持 ES Module 的环境，可以使用：
- **打包工具**：Webpack、Rollup、Vite 等
- **转译工具**：Babel + SystemJS
- **Polyfill**：使用相应的 polyfill 方案

## 总结

ES Module 是现代 JavaScript 开发的重要基础，掌握模块化开发对于构建复杂应用至关重要。通过本章节的学习，你应该能够：

1. 理解 ES Module 的基本概念和特点
2. 熟练使用 export 和 import 语法
3. 区分具名导出和默认导出的使用场景
4. 在实际项目中应用模块化开发思想
5. 了解模块化的最佳实践和性能优化

继续练习和实际应用，将帮助你更深入地理解和掌握 ES Module。