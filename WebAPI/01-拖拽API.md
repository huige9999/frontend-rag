# 01 - 拖拽API

## 一、拖拽基础概念

HTML5 拖拽 API 允许用户通过鼠标拖拽页面中的元素，并将其放置到指定的目标区域。

**核心要点：**
- 被拖拽元素需要设置 `draggable="true"` 属性
- 拖放目标区域需要监听 `dragover` 和 `drop` 事件
- 必须在 `dragover` 事件中调用 `e.preventDefault()`，否则 `drop` 事件不会触发

```html
<!-- 拖放目标区域 -->
<div class="drop-content">拖放区域</div>

<!-- 被拖拽元素，必须设置 draggable="true" -->
<div class="drag-box" draggable="true">拖拽元素</div>
```

## 二、最简拖拽示例

将红色方块拖入虚线框内，实现元素的移动。

```js
const dropContent = document.querySelector(".drop-content");
const dragBox = document.querySelector(".drag-box");

// 必须阻止 dragover 的默认行为，否则 drop 不会触发
dropContent.addEventListener("dragover", (e) => e.preventDefault());

// 在 drop 事件中执行实际操作：将拖拽元素移入目标区域
dropContent.addEventListener("drop", (e) => {
  dropContent.appendChild(dragBox);
});
```

## 三、拖拽事件详解

拖拽过程涉及两类元素（被拖拽元素 / 目标区域），各自有不同的事件。

**被拖拽元素的事件：**

| 事件 | 触发时机 |
|------|---------|
| `dragstart` | 开始拖拽时触发（一次） |
| `drag` | 拖拽过程中持续触发（高频） |
| `dragend` | 拖拽结束时触发（一次） |

**目标区域的事件：**

| 事件 | 触发时机 |
|------|---------|
| `dragenter` | 拖拽元素进入目标区域时触发 |
| `dragover` | 拖拽元素在目标区域内移动时持续触发 |
| `dragleave` | 拖拽元素离开目标区域时触发 |
| `drop` | 拖拽元素在目标区域内释放时触发 |

```js
// === 被拖拽元素的事件 ===
dragBox.addEventListener("dragstart", () => console.log("开始拖拽"));
dragBox.addEventListener("drag", () => console.log("拖拽过程中")); // 高频触发，慎用
dragBox.addEventListener("dragend", () => console.log("拖拽结束"));

// === 目标区域的事件 ===
dropContent.addEventListener("dragenter", () => {
  console.log("拖拽元素进入到目标区域");
});
dropContent.addEventListener("dragover", (e) => {
  e.preventDefault(); // 关键：必须阻止默认行为
  console.log("拖拽元素在目标区域内拖拽");
});
dropContent.addEventListener("dragleave", () => {
  console.log("拖拽元素离开目标区域");
});
dropContent.addEventListener("drop", (e) => {
  e.preventDefault();
  console.log("拖拽元素在目标区域内释放");
});
```

**阻止输入框的默认拖放行为：**

```js
// 防止输入框在拖拽时被填入拖拽的文本
document.querySelector('input').addEventListener('drop', function(e) {
  e.preventDefault();
});
```

## 四、dataTransfer 对象

`dataTransfer` 是拖拽事件的核心数据传递对象，用于在拖拽源和目标之间传递数据。

### 4.1 设置与读取数据

```js
// dragstart 时设置多种类型的数据
dragBox.addEventListener("dragstart", function(e) {
  e.dataTransfer.setData("text/plain", "我是一个拖拽文本");
  e.dataTransfer.setData("text/html", "<p>我是一个拖拽文本标签</p>");
});

// drop 时根据类型读取数据
dropContent.addEventListener("drop", function(e) {
  // 按类型读取
  dropContent.innerHTML = e.dataTransfer.getData("text/html");
  // types 属性可查看所有已设置的数据类型
  console.log(e.dataTransfer.types);
});
```

### 4.2 自定义拖拽图像

使用 `setDragImage()` 可自定义拖拽时跟随鼠标的半透明图像。

```js
dragBox.addEventListener("dragstart", function(e) {
  const img = document.createElement("img");
  img.src = "./assets/music.jpeg";
  // setDragImage(图片元素, 水平偏移, 垂直偏移)
  e.dataTransfer.setDragImage(img, 50, 50);
});
```

### 4.3 设置拖放效果

通过 `dropEffect` 属性控制拖拽时的视觉反馈（光标样式）。

```js
dropContent.addEventListener("dragover", function(e) {
  // 可选值: "copy" | "move" | "link" | "none"
  e.dataTransfer.dropEffect = "copy";
  e.preventDefault();
});
```

**dropEffect 可选值：**

| 值 | 效果 |
|----|------|
| `copy` | 拖放将复制数据 |
| `move` | 拖放将移动数据 |
| `link` | 拖放将创建链接 |
| `none` | 不允许放置 |

## 五、实战：拖拽排序

利用拖拽 API 实现列表项的拖拽排序，通过计算鼠标位置决定插入到目标元素的前面还是后面。

```html
<ul id="sortableList">
  <li draggable="true">Item 1</li>
  <li draggable="true">Item 2</li>
  <li draggable="true">Item 3</li>
  <li draggable="true">Item 4</li>
  <li draggable="true">Item 5</li>
</ul>
```

```js
const sortableList = document.getElementById("sortableList");
let draggingElement = null;

// 开始拖拽：标记当前拖拽元素
sortableList.addEventListener("dragstart", function(e) {
  e.target.classList.add("dragging");
  draggingElement = e.target;
});

// 拖拽经过：计算鼠标位置，实时调整列表顺序
sortableList.addEventListener("dragover", function(e) {
  e.preventDefault();
  e.dataTransfer.dropEffect = "move";

  // 只在目标为 LI 元素且不是当前拖拽元素时处理
  if (e.target !== draggingElement && e.target.tagName === "LI") {
    const rect = e.target.getBoundingClientRect();
    const mouseY = e.clientY - rect.top;      // 鼠标在目标元素内的 Y 坐标
    const halfHeight = rect.height / 2;       // 目标元素高度的一半

    if (mouseY < halfHeight) {
      // 鼠标在上半部分：插入到目标元素前面
      sortableList.insertBefore(draggingElement, e.target);
    } else {
      // 鼠标在下半部分：插入到目标元素后面
      sortableList.insertBefore(draggingElement, e.target.nextSibling);
    }
  }
});

// 拖拽结束：移除标记样式
sortableList.addEventListener("dragend", function(e) {
  e.target.classList.remove("dragging");
  draggingElement = null;
});
```

**核心思路：**
1. 用一个变量 `draggingElement` 记录当前正在拖拽的元素
2. 在 `dragover` 中通过 `getBoundingClientRect()` 获取目标元素的位置
3. 比较 `e.clientY` 与目标元素中点，决定插入到目标的前面还是后面
4. 利用 `insertBefore` 实时移动 DOM 节点，产生动态排序效果

## 六、知识总结

```
拖拽 API 三要素：
  1. draggable="true"        → 声明元素可拖拽
  2. dragover + preventDefault → 使目标区域可接收放置
  3. drop 事件               → 处理放置逻辑

dataTransfer 核心方法：
  - setData(type, data)      → 拖拽开始时存储数据
  - getData(type)            → 放置时读取数据
  - setDragImage(img, x, y)  → 自定义拖拽图像
  - dropEffect               → 控制拖拽视觉效果
```
