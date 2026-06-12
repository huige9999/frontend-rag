# 1-12 node生命周期

## 知识要点

### Node.js 事件循环（Event Loop）

Node.js 是单线程的，通过**事件循环**实现异步非阻塞 I/O。

### 事件循环阶段

```
   ┌───────────────────────────┐
┌─>│         timers             │  ← setTimeout / setInterval
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks      │  ← 系统级回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  ← 内部使用
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │         poll               │  ← I/O 回调
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │         check              │  ← setImmediate
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     close callbacks        │  ← close 事件回调
│  └─────────────┬─────────────┘
│                └──────────────> 退出或继续循环
```

### 微任务与宏任务

| 类型 | API |
|------|-----|
| **微任务**（优先执行） | `process.nextTick`、`Promise.then` |
| **宏任务** | `setTimeout`、`setInterval`、`setImmediate`、I/O 回调 |

> `process.nextTick` 的优先级高于 `Promise.then`

---

## 代码示例

### 1. 执行顺序分析（index.js）

分析微任务与宏任务的执行顺序：

```js
setImmediate(() => {
  console.log(1);        // 宏任务 - check 阶段
});

process.nextTick(() => {
  console.log(2);        // 微任务 - 最先执行
  process.nextTick(() => {
    console.log(6);      // 嵌套的 nextTick
  });
});

console.log(3);          // 同步代码

Promise.resolve().then(() => {
  console.log(4);        // 微任务
  process.nextTick(() => {
    console.log(5);      // nextTick 优先于后续 then
  });
});

// 输出顺序：3 → 2 → 6 → 4 → 5 → 1
```

### 2. setTimeout vs setImmediate（index copy.js）

在非 I/O 上下文中，`setTimeout` 和 `setImmediate` 的执行顺序不确定：

```js
setTimeout(() => {
  console.log("setTimeout");
}, 0);

setImmediate(() => {
  console.log("setImmediate");
});

// 顺序不确定，取决于系统性能
```

### 3. I/O 回调中的执行顺序（index copy 2.js）

在 I/O 回调中，`setImmediate` 一定先于 `setTimeout` 执行：

```js
const fs = require("fs");
fs.readFile("./index.js", () => {
  setTimeout(() => console.log(1), 0);
  setImmediate(() => console.log(2));
  // 输出：2 → 1（setImmediate 始终先执行）
});
```

### 4. setImmediate 递归性能（index copy 3.js）

使用 `setImmediate` 进行递归，不会阻塞 I/O：

```js
let i = 0;
console.time();
function test() {
  i++;
  if (i < 1000) {
    setImmediate(test);
  } else {
    console.timeEnd();
  }
}
test();
```

### 5. I/O 阻塞示例（面试题1.js）

I/O 回调中的同步操作会阻塞事件循环：

```js
const start = Date.now();
setTimeout(function f1() {
  console.log("setTimeout", Date.now() - start);
}, 200);

const fs = require("fs");
fs.readFile("./index.js", "utf-8", function f2() {
  console.log("readFile");
  const start = Date.now();
  while (Date.now() - start < 300) {} // 阻塞300ms
  // setTimeout 回调至少延迟到 300ms 之后才执行
});
```

### 6. 综合面试题（面试题2.js）

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");  // 微任务
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(function() {
  console.log("setTimeout0");
}, 0);
setTimeout(function() {
  console.log("setTimeout3");
}, 3);
setImmediate(() => console.log("setImmediate"));
process.nextTick(() => console.log("nextTick"));
async1();
new Promise(function(resolve) {
  console.log("promise1");
  resolve();
  console.log("promise2");
}).then(function() {
  console.log("promise3");
});
console.log("script end");

// 输出顺序：
// script start
// async1 start
// async2
// promise1
// promise2
// script end
// nextTick          ← nextTick 优先于 Promise
// async1 end
// promise3
// setTimeout0       ← 宏任务
// setImmediate
// setTimeout3
```
