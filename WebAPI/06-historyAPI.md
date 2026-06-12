# 06 - History API

History API 是浏览器提供的浏览器历史记录操作接口，挂载在 `window.history` 对象上，允许开发者通过 JavaScript 读取和操作浏览器的会话历史记录（即标签页的访问栈）。

---

## 一、history 对象核心属性与方法

| 属性 / 方法 | 说明 |
|------|------|
| `history.length` | 返回当前标签页历史记录栈中的条目数量 |
| `history.state` | 返回当前历史条目关联的状态对象（`pushState` / `replaceState` 存入的数据） |
| `history.back()` | 后退到上一条历史记录，等价于浏览器后退按钮 |
| `history.forward()` | 前进到下一条历史记录，等价于浏览器前进按钮 |
| `history.go(offset)` | 跳转到相对当前偏移量为 `offset` 的历史条目（负数后退，正数前进，0 刷新） |
| `history.pushState(state, title, url)` | 向历史栈**追加**一条新记录，不刷新页面 |
| `history.replaceState(state, title, url)` | **替换**当前历史记录条目，不刷新页面 |
| `history.scrollRestoration` | 控制返回页面时是否恢复滚动位置，值为 `"auto"`（默认）或 `"manual"` |

---

## 二、scrollRestoration — 控制滚动位置恢复

默认情况下，浏览器在通过后退/前进按钮返回页面时，会自动恢复该页面之前的滚动位置。通过设置 `scrollRestoration` 为 `"manual"` 可以禁止此行为。

```js
// 禁止浏览器自动恢复滚动位置
history.scrollRestoration = "manual";
```

**示例场景**（a.html）：页面包含 100 个 `<div>` 元素产生长页面滚动，设置 `scrollRestoration = "manual"` 后，从其他页面返回时页面不会自动滚回之前的位置。

要点：
- `"auto"`：浏览器自动恢复滚动位置（默认行为）
- `"manual"`：不自动恢复，页面将回到顶部
- 适用于需要精确控制滚动行为的 SPA 应用

---

## 三、pushState — 追加历史记录

`pushState` 用于在不刷新页面的情况下向浏览器历史栈追加一条新记录，同时修改地址栏 URL。

```js
// 语法
history.pushState(state, title, url);

// 示例：存储当前页面路由信息
history.pushState({ url: "/home" }, "", "/home");
```

参数说明：
- `state`：一个与该历史条目关联的状态对象，可以在 `popstate` 事件中通过 `event.state` 获取
- `title`：大多数浏览器忽略此参数，一般传空字符串 `""`
- `url`：新的地址栏 URL，必须与当前页面同源

要点：
- **不会触发页面刷新**，也不会触发 `popstate` 事件
- 仅改变地址栏显示和向历史栈添加条目
- `url` 参数受同源策略限制，不能跨域

---

## 四、popstate 事件 — 监听历史记录变化

当用户点击浏览器的前进/后退按钮，或通过 JS 调用 `history.back()`、`history.forward()`、`history.go()` 时，会触发 `popstate` 事件。

```js
window.onpopstate = function (e) {
  const state = e.state; // 获取 pushState 时存入的 state 对象
  if (state) {
    // 根据 state 恢复页面状态
    linkPage(state.url, false);
  }
};
```

要点：
- `pushState` 和 `replaceState` **不会**触发 `popstate` 事件
- 仅在前进/后退导航时触发
- 通过 `event.state` 可以读取当时通过 `pushState` 存入的状态数据

---

## 五、服务端配合实现 SPA 路由

SPA（单页应用）使用 History API 实现前端路由时，需要服务端配合：所有未匹配的 URL 都应返回同一个 HTML 入口文件，由前端 JS 根据 URL 解析路由并渲染对应内容。

### 服务端配置（server.js）

使用 Express 搭建一个简单的静态服务器，将所有路径的请求统一指向 `demo.html`：

```js
const express = require("express");
const path = require("path");
const server = express();

// 托管静态资源
server.use(express.static(path.join(__dirname, "static")));

// 所有未匹配的 GET 请求都返回 demo.html
server.get("*", (req, res) => {
  res.sendFile(path.join(__dirname, "static", "demo.html"));
});

server.listen(12345);
```

要点：
- `express.static` 优先匹配静态文件
- `server.get("*")` 作为兜底路由，保证前端路由路径（如 `/home`、`/xuanwu`）不会返回 404
- 这是 SPA 部署中经典的"HTML5 History 模式"服务端配置

---

## 六、SPA 前端路由完整示例（demo.html）

该示例演示了一个完整的单页应用前端路由系统，包含导航栏、多个页面区块、路由切换与数据模拟加载。

### 页面结构

导航栏包含 5 个链接，点击后切换到对应的内容区块：

```html
<div class="header">
  <ul class="center">
    <li><a href="/home">华为</a></li>
    <li><a href="/xuanwu">玄武框架</a></li>
    <li><a href="/hongmeng">鸿蒙系统</a></li>
    <li><a href="/qilin">麒麟芯片</a></li>
    <li><a href="/kunlun">昆仑玻璃</a></li>
  </ul>
</div>

<!-- 所有页面区块，默认隐藏，由 JS 控制显隐 -->
<div class="pages">
  <div id="home"><h1>华为首页</h1><div class="content">请稍等，数据响应中</div></div>
  <div id="xuanwu"><h1>玄武框架</h1><div class="content">请稍等，数据响应中</div></div>
  <div id="hongmeng"><h1>鸿蒙系统</h1><div class="content">请稍等，数据响应中</div></div>
  <div id="qilin"><h1>麒麟芯片</h1><div class="content">请稍等，数据响应中</div></div>
  <div id="kunlun"><h1>昆仑玻璃</h1><div class="content">请稍等，数据响应中</div></div>
</div>
```

### 路由跳转与 pushState

拦截导航链接点击，阻止默认行为，手动调用 `pushState` 并切换页面：

```js
const centerDom = document.querySelector(".center");
let activeDom = null;

// 页面加载时，根据当前路径初始化对应页面
linkPage(location.pathname === "/" ? "/home" : location.pathname);

// 拦截导航点击事件
centerDom.onclick = function (e) {
  e.preventDefault();
  const target = e.target;
  if (target.tagName === "A") {
    const url = target.getAttribute("href");
    if (url === location.pathname) return; // 当前页不重复跳转
    linkPage(url);
  }
};

function linkPage(url, isAdd = true) {
  // isAdd 为 true 时向历史栈追加记录（点击导航时）
  // isAdd 为 false 时不追加（浏览器前进/后退时）
  if (isAdd) {
    history.pushState({ url }, "", url);
  }
  // 切换页面显示
  activeDom && (activeDom.style.display = "none");
  const dom = document.getElementById(url.slice(1)); // "/home" -> "home"
  dom.style.display = "block";
  activeDom = dom;
  fetchData(url); // 模拟请求数据
}
```

要点：
- `e.preventDefault()` 阻止链接的默认跳转行为
- `url.slice(1)` 将路径（如 `/home`）转为 DOM id（`home`）
- `isAdd` 参数区分是主动导航还是浏览器后退触发的恢复

### 模拟数据请求

```js
function fetchData(url) {
  const dom = activeDom.querySelector(".content");
  dom.innerHTML = "请稍等，数据响应中";
  setTimeout(function () {
    dom.innerHTML = `请求到${url}的数据`;
  }, Math.random() * 3000);
}
```

使用 `setTimeout` 模拟网络请求的异步延迟效果。

### 监听浏览器前进/后退

```js
window.onpopstate = function (e) {
  const state = e.state;
  if (state) {
    // 浏览器后退/前进时，恢复对应页面（不追加新的历史记录）
    linkPage(state.url, false);
  }
};
```

要点：
- 用户点击浏览器后退/前进时触发 `popstate` 事件
- 通过 `event.state` 取出 `pushState` 时存入的 `{ url }` 数据
- 传入 `false` 避免再次向历史栈追加记录

---

## 七、总结

| 场景 | 使用的 API |
|------|------|
| 页面间普通链接导航（a/b/c/d.html） | 传统 `<a href>` 跳转，浏览器自动管理历史 |
| 控制返回页面时的滚动位置 | `history.scrollRestoration = "manual"` |
| SPA 中点击导航切换页面 | `history.pushState()` + 拦截 `<a>` 点击 |
| SPA 中浏览器前进/后退 | `window.onpopstate` 事件监听 |
| SPA 服务端兜底路由 | Express `server.get("*")` 返回入口 HTML |

核心流程：**点击导航 → preventDefault → pushState → 切换视图**，**浏览器后退 → popstate → 读取 state → 恢复视图**。
