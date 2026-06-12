# 08 - Clipboard API

Clipboard API 是浏览器提供的剪贴板操作接口，挂载在 `navigator.clipboard` 对象上，支持以编程方式读写系统剪贴板内容。

---

## 一、核心对象

```js
// Clipboard API 的入口
navigator.clipboard
```

该对象提供以下主要方法：

| 方法 | 说明 |
|------|------|
| `writeText(text)` | 将纯文本写入剪贴板 |
| `readText()` | 从剪贴板读取纯文本 |
| `write(data)` | 将任意数据（如图片、HTML）写入剪贴板 |
| `read()` | 从剪贴板读取任意数据（返回 ClipboardItem 数组） |

---

## 二、写入纯文本 — writeText()

将字符串内容复制到系统剪贴板，返回一个 Promise。

```js
// 获取页面中某段文字内容
const content = document.querySelector('.content-1');

// 写入剪贴板
async function copyText() {
  const val = content.innerText;
  await navigator.clipboard.writeText(val);
  alert('复制成功');
}
```

要点：
- 参数为要复制的纯文本字符串
- 异步方法，需使用 `await` 或 `.then()` 等待完成
- 仅支持纯文本，不支持富文本或图片

---

## 三、读取纯文本 — readText()

从系统剪贴板读取纯文本内容，返回一个 Promise。

```js
async function showClipboardText() {
  try {
    const val = await navigator.clipboard.readText();
    alert(val);
  } catch (error) {
    console.log(error); // 读取失败（如用户未授权）
  }
}
```

要点：
- 返回剪贴板中的纯文本字符串
- 必须使用 `try...catch` 包裹，因为用户可能拒绝授权
- 仅能读取纯文本，如果剪贴板中是图片则返回空字符串

---

## 四、读取任意数据 — read()

`read()` 方法返回 `ClipboardItem` 数组，可读取剪贴板中的多种格式数据（文本、图片等）。

```js
document.onpaste = async function () {
  console.log('触发 paste 粘贴事件');

  // read() 返回 ClipboardItem 数组
  const val = await navigator.clipboard.read();
  const item = val[0]; // 取第一个剪贴板项

  // 获取该项支持的所有 MIME 类型
  const types = item.types; // 例如 ['text/plain', 'text/html', 'image/png']

  // 遍历每种类型，读取对应的 Blob 数据
  types.forEach(async (type) => {
    const blob = await item.getType(type); // 返回 Blob 对象

    // 使用 FileReader 读取 Blob 内容
    const fr = new FileReader();
    fr.onload = function (e) {
      console.log(e.target.result); // 输出读取结果
    };
    fr.readAsText(blob); // 以文本方式读取
  });
};
```

要点：
- `read()` 返回的是 `ClipboardItem[]`，每个项可能包含多种格式的数据
- `item.types` 是一个字符串数组，表示该项包含的数据 MIME 类型
- `item.getType(type)` 按指定类型返回对应的 `Blob` 对象
- 需配合 `FileReader` 将 Blob 转换为可用的字符串或 Data URL

---

## 五、剪贴板事件

浏览器提供了三个剪贴板相关事件，可监听用户的复制/剪切/粘贴操作：

| 事件 | 触发时机 |
|------|----------|
| `copy` | 用户复制内容时 |
| `cut` | 用户剪切内容时 |
| `paste` | 用户粘贴内容时 |

```js
// 监听复制事件
document.oncopy = function (e) {
  console.log(e);
  console.log('触发 copy 事件，复制');
};

// 监听粘贴事件
document.onpaste = function () {
  console.log('触发 paste 粘贴事件');
};

// 监听剪切事件
document.oncut = function () {
  console.log('触发 cut 事件');
};
```

要点：
- 事件可以绑定在 `document` 上，也可以绑定在具体元素上
- 通过事件对象 `e` 可以获取和修改被复制/粘贴的内容
- 可用于拦截默认剪贴板行为，实现自定义处理

---

## 六、writeText 与 read 对比

| 特性 | `writeText()` / `readText()` | `write()` / `read()` |
|------|------|------|
| 数据类型 | 仅纯文本 | 任意类型（文本、图片、HTML 等） |
| 返回值 | `Promise<string>` | `Promise<ClipboardItem[]>` |
| 读取方式 | 直接得到字符串 | 需通过 `getType()` 获取 Blob，再用 FileReader 解析 |
| 复杂度 | 简单 | 较复杂 |
| 适用场景 | 普通文本复制粘贴 | 富文本、图片等复杂数据处理 |

---

## 七、注意事项

1. **安全上下文要求**：Clipboard API 只能在安全上下文（HTTPS 或 localhost）中使用
2. **权限授权**：读取操作（`read()`、`readText()`）需要用户授予剪贴板读取权限，建议始终使用 `try...catch` 处理拒绝的情况
3. **用户激活**：部分浏览器要求 Clipboard API 的调用必须由用户交互事件（如 click）触发，不能在任意时机自动调用
4. **兼容性**：现代浏览器基本支持，但 `read()` / `write()` 的支持度低于 `writeText()` / `readText()`
