# 12 - MutationObserver API

> MutationObserver 接口提供了监视对 DOM 树所做更改的能力。它被设计为旧的 Mutation Events 功能的替代品，该功能是 DOM3 Events 规范的一部分。

## 核心概念

- MutationObserver 是一个**异步**的 DOM 变动观察器
- 当 DOM 元素发生属性变化、子节点增删、文本内容修改等操作时，Observer 会触发回调
- 典型应用场景：数据绑定、自动计算、输入校验、富文本编辑器等

## 基本使用步骤

### 1. 创建 Observer 实例

传入一个回调函数，DOM 发生变化时会自动调用，回调参数 `mutations` 是一个 MutationRecord 数组，记录了所有变动信息。

```js
const mob = new MutationObserver((mutations) => {
  console.log(mutations); // mutations 是 MutationRecord 数组
});
```

### 2. 调用 observe 方法开始观察

指定要观察的目标元素和观察选项：

```js
mob.observe(target, options);
```

`options` 配置项说明：

| 选项 | 类型 | 说明 |
|------|------|------|
| `attributes` | Boolean | 观察目标属性的变化 |
| `characterData` | Boolean | 观察目标文本数据的变化 |
| `childList` | Boolean | 观察目标子节点的增删（不含修改子节点本身） |
| `subtree` | Boolean | 同时观察目标及其所有后代节点 |
| `attributeOldValue` | Boolean | 记录属性变化前的旧值 |
| `characterDataOldValue` | Boolean | 记录文本变化前的旧值（设置后可省略 characterData） |
| `attributeFilter` | Array | 只观察指定的属性，如 `['src', 'class']` |

### 3. 停止观察

```js
mob.disconnect(); // 停止观察，不再触发回调
```

## 示例一：基础 DOM 变动监听

监听元素的属性变化、子节点增删、文本内容修改：

```html
<div class="box">
  <div class="son">123</div>
</div>

<script>
const box = document.querySelector(".box");

// 创建 Observer，回调接收 mutations 数组
const mob = new MutationObserver((mutations) => {
  console.log(mutations);
});

// 配置观察项：属性 + 子节点 + 文本 + 后代
mob.observe(box, {
  attributes: true,       // 监听属性变化（如 style、id 等）
  childList: true,        // 监听子节点增删
  characterData: true,    // 监听文本内容变化
  subtree: true,          // 监听所有后代节点
});

// 以下操作都会触发 Observer 回调
function changeBox() {
  box.style.color = 'red';          // attributes 变化
  box.setAttribute('id', 'demo');   // attributes 变化
  box.appendChild(document.createElement('div')); // childList 变化
  box.childNodes[0].data = 123;     // characterData 变化
}
</script>
```

**关键点**：`subtree: true` 非常重要，没有它只能观察到直接子节点的变化，后代节点的变化会被忽略。

## 示例二：实战 - 可编辑表格自动计算总价

利用 MutationObserver 监听 `contenteditable` 单元格的文本变化，自动计算"数量 x 单价 = 总价"：

```html
<table id="data-table">
  <thead>
    <tr>
      <th>商品</th><th>数量</th><th>单价</th><th>总价</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>商品 1</td>
      <td contenteditable="true">2</td>
      <td contenteditable="true">10</td>
      <td>20</td>
    </tr>
  </tbody>
</table>
<button id="add-button">添加商品</button>
```

```js
const table = document.querySelector("#data-table");
const addBtn = document.querySelector("#add-button");

// 添加新商品行
let goodsNum = 2;
addBtn.onclick = function () {
  const tr = document.createElement("tr");
  tr.innerHTML = `
    <td>商品 ${++goodsNum}</td>
    <td contenteditable="true">0</td>
    <td contenteditable="true">0</td>
    <td>0</td>
  `;
  table.tBodies[0].appendChild(tr);
};

// 用 MutationObserver 监听表格内容变化，自动计算总价
const mob = new MutationObserver((mutations) => {
  mutations.forEach(mutation => {
    const target = mutation.target;                          // 发生变化的单元格
    const trParent = target.parentElement.parentElement;    // 找到所在 <tr>
    const qty = trParent.children[1].innerText;              // 数量
    const price = trParent.children[2].innerText;            // 单价
    trParent.children[3].innerText = qty * price;            // 计算并更新总价
  });
});

// 只需监听 characterData + subtree，捕获 editable 单元格的文本编辑
mob.observe(table, {
  characterData: true,  // 监听文本变化
  subtree: true,        // 监听表格内所有后代节点
});
```

**核心思路**：
- 单元格设置 `contenteditable="true"` 后，用户编辑会触发 `characterData` 变化
- Observer 回调中通过 `mutation.target` 定位到具体编辑的单元格
- 向上找到 `<tr>` 行，读取数量和单价，计算并回写总价
- 新添加的商品行也会被监听到（因为 `subtree: true`），无需额外绑定事件

## MutationRecord 对象属性

每次回调中的 `mutations` 数组元素包含以下常用属性：

| 属性 | 说明 |
|------|------|
| `type` | 变动类型：`'attributes'` / `'characterData'` / `'childList'` |
| `target` | 发生变动的 DOM 节点 |
| `addedNodes` | 新增的子节点列表（NodeList） |
| `removedNodes` | 删除的子节点列表（NodeList） |
| `previousSibling` | 变动节点的前一个兄弟节点 |
| `nextSibling` | 变动节点的后一个兄弟节点 |
| `attributeName` | 发生变化的属性名 |
| `oldValue` | 变化前的旧值（需开启对应 OldValue 选项） |

## 注意事项

1. **异步触发**：MutationObserver 是微任务，会在当前同步代码执行完毕后才触发回调，而非每次变动都立即触发
2. **批量处理**：多次 DOM 变更会在一次回调中作为数组批量传入，性能优于旧的 Mutation Events
3. **subtree 很关键**：如果需要观察后代节点的变化，必须设置 `subtree: true`
4. **及时断开**：观察完毕后调用 `disconnect()` 释放资源，避免内存泄漏
