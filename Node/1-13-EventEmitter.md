# 1-13 [扩展] EventEmitter

## 知识要点

### 什么是 EventEmitter？

`EventEmitter` 是 Node.js 事件驱动的核心类，位于 `events` 模块中。许多 Node.js 内置类（如 `Server`、`Socket`）都继承自 `EventEmitter`。

### 核心 API

| 方法 | 说明 |
|------|------|
| `ee.on(event, listener)` | 注册事件监听器 |
| `ee.once(event, listener)` | 注册一次性监听器（触发一次后自动移除） |
| `ee.emit(event, ...args)` | 触发事件，传入参数 |
| `ee.off(event, listener)` | 移除指定监听器 |

### 应用场景

- 自定义类的事件支持
- 网络请求的结果通知
- 流式数据处理的通知

---

## 代码示例

### 1. 基本用法（index copy 2.js）

注册事件并传递数据：

```js
const { EventEmitter } = require("events");
const ee = new EventEmitter();

ee.on("abc", (data1, data2) => {
  console.log("abc事件触发了", data1, data2);
});

ee.emit("abc", 123, 456);
// 输出: abc事件触发了 123 456
```

### 2. on / once / off 完整示例（index copy.js）

```js
const { EventEmitter } = require("events");
const ee = new EventEmitter();

ee.on("abc", () => {
  console.log("abc事件触发了1");
});

const fn2 = () => {
  console.log("abc事件触发了2");
};
ee.on("abc", fn2);

ee.once("abc", () => {
  console.log("abc事件触发了3", "该事件只触发一次");
});

ee.on("bcd", () => {
  console.log("bcd事件触发了3");
});

ee.emit("abc"); // 触发1、2、3

ee.off("abc", fn2); // 移除 fn2

ee.emit("abc"); // 触发1（3已被once自动移除，2被off移除）
ee.emit("abc"); // 触发1
ee.emit("bcd"); // 触发bcd的监听器

console.log("script end");
```

### 3. 实际应用 — 自定义网络请求类（MyRequest.js + index.js）

继承 `EventEmitter` 封装 HTTP 请求类，通过事件通知请求结果：

**MyRequest.js：**

```js
const http = require("http");
const { EventEmitter } = require("events");

module.exports = class extends EventEmitter {
  constructor(url, options) {
    super();
    this.url = url;
    this.options = options;
  }

  send(body = "") {
    const request = http.request(this.url, this.options, res => {
      let result = "";
      res.on("data", chunk => {
        result += chunk.toString("utf-8");
      });
      res.on("end", () => {
        this.emit("res", res.headers, result); // 触发 res 事件
      });
    });
    request.write(body);
    request.end();
  }
};
```

**index.js（使用方式）：**

```js
const MyRequest = require("./MyRequest");

const request = new MyRequest("http://duyi.ke.qq.com");
request.send();

request.on("res", (headers, body) => {
  console.log(headers);
  console.log(body);
});
```

> 通过继承 `EventEmitter`，`MyRequest` 类具备了事件驱动能力，实现了请求与响应处理的解耦。
