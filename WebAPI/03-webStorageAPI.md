# 03 - Web Storage API

## 一、基本概念

Web Storage API 提供了浏览器本地键值对存储的能力，包含两个对象：

| 对象 | 说明 |
|------|------|
| `localStorage` | 持久化存储，关闭浏览器后数据仍在 |
| `sessionStorage` | 会话存储，关闭标签页后数据清除 |

**共同特点：**
- 只能存储**字符串**类型的数据（对象需 `JSON.stringify` / `JSON.parse` 转换）
- 遵循同源策略（协议 + 域名 + 端口相同才能访问）
- 容量一般为 5MB

## 二、CRUD 操作

### 2.1 新增 / 修改 — setItem

```js
localStorage.setItem("user", "多多");
sessionStorage.setItem("user", "多多");
```

> 如果 key 已存在，则会覆盖原值。

### 2.2 查询 — getItem

```js
console.log(localStorage.getItem("user"));   // "多多"
console.log(sessionStorage.getItem("user")); // "多多"
```

> 如果 key 不存在，返回 `null`。

### 2.3 删除 — removeItem / clear

```js
// 删除指定 key
localStorage.removeItem("user");

// 清空所有存储
localStorage.clear();
```

### 2.4 遍历所有键值对

```js
function getAllMsg() {
  for (let i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i);
    const item = localStorage.getItem(key);
    console.log(item);
  }
}
```

> `localStorage.length` 获取存储条目数，`localStorage.key(index)` 按索引获取键名。

## 三、实战：主题切换持久化

利用 localStorage 记住用户选择的主题，刷新页面后自动恢复。

### 3.1 CSS 变量定义主题

```css
:root {
  --bg: #409eff;        /* 默认蓝色背景 */
  --btn-color: #67c23a;
}
.green-theme {
  --bg: #67c23a;        /* 绿色主题 */
  --btn-color: #409eff;
}
body {
  transition: background 1s;
  background: var(--bg);
}
button {
  background: var(--btn-color);
  color: #fff;
  /* ... */
}
```

### 3.2 JS 逻辑：读取主题 + 切换

```js
const THEME = "__THEME__";

// 页面加载时恢复主题
const data = localStorage.getItem(THEME);
document.body.classList.add(data);

// 点击按钮切换主题
document.querySelector("button").addEventListener("click", () => {
  if (document.body.classList.contains("green-theme")) {
    document.body.classList.remove("green-theme");
    localStorage.removeItem(THEME);       // 移除主题记录
  } else {
    document.body.classList.add("green-theme");
    localStorage.setItem(THEME, "green-theme");  // 记录主题
  }
});
```

**流程说明：**
1. 页面加载 → 从 localStorage 读取主题 → 应用对应 class
2. 点击按钮 → 切换 class → 同步写入/移除 localStorage
3. 下次打开页面自动恢复上次选择

## 四、API 速查表

| 方法 / 属性 | 说明 |
|-------------|------|
| `setItem(key, value)` | 存储数据 |
| `getItem(key)` | 读取数据 |
| `removeItem(key)` | 删除指定数据 |
| `clear()` | 清空所有数据 |
| `length` | 存储条目数量 |
| `key(index)` | 按索引获取键名 |
