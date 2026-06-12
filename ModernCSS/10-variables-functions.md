# 10. CSS 变量与函数

## 一、CSS 自定义属性（变量）基础

### 1.1 什么是 CSS 变量

CSS 变量也称为**自定义属性（Custom Properties）**，以 `--` 开头命名，用于存储和重复使用值。通过 `var()` 函数来引用变量。

### 1.2 定义与使用变量

```css
/* 在 :root 中定义全局变量 */
:root {
  --main-color: #3498db;
  --font-size: 16px;
}

/* 使用 var() 函数引用变量 */
body {
  color: var(--main-color);
  font-size: var(--font-size);
}
```

> `:root` 选择器代表文档根元素（即 `<html>`），在此处定义的变量可在整个文档中使用。

### 1.3 变量命名规则

- 必须以 `--` 开头
- 命名限制较少，支持中文变量名
- 不支持 `$`、`[`、`]`、`^`、`(`、`)`、`%`、`"` 等字符，使用时需要转义

```css
.box {
  --color: blue;
  --变量: 24px;
  ---: 2px dashed skyblue;   /* 连字符作为变量名 */
  --23: 20;                   /* 数字开头也可以 */
  --\]: 20px;                 /* 特殊字符需转义 */

  color: var(--color);
  border: var(---);
  font-size: var(--变量);
  z-index: var(--23);
  margin-left: var(--\]);
}
```

---

## 二、var() 函数详解

### 2.1 完整语法

```
var(自定义属性名, 后备CSS属性值)
```

```css
.box {
  --what: 32px;
  color: var(--what, red);     /* --what 有效，值为 32px，后备值不生效 */
  font-size: var(--what);      /* 32px */
}
```

### 2.2 后备值机制

- 后备值**仅当**自定义属性**一定无效**时才生效
- 如果自定义属性值可能有效，即使不合理，后备值也不会渲染
- 如果自定义属性值不合法，`var()` 解析为当前属性的**初始值或继承值**（即按 `unset` 规则渲染）

### 2.3 变量值的特性

**自定义属性值可以是任意值或表达式：**

```css
.box {
  --dir: to right;
  --start-color: skyblue 50%;
  --end-color: #fac 50%;

  background-image: linear-gradient(
    var(--dir),
    var(--start-color),
    var(--end-color)
  );
}
```

**自定义属性值可以相互传递：**

```css
.box {
  --ab: 20px;
  --abc: var(--ab);    /* 变量值引用另一个变量 */
  font-size: var(--abc); /* 最终为 20px */
}
```

---

## 三、变量的作用域与继承

CSS 变量的作用域与 JavaScript 中的作用域概念类似：

| 概念 | JavaScript | CSS |
|------|-----------|-----|
| 全局作用域 | 全局变量 | `:root` 中定义 |
| 局部作用域 | 函数作用域 | 选择器中定义 |
| 作用域链 | 作用域链查找 | DOM 树继承查找 |

### 3.1 全局作用域

```css
:root {
  --color: #fac;     /* 全局变量，所有元素可用 */
}
```

### 3.2 局部作用域与继承

```css
.box {
  --color: red;      /* 局部变量，仅 .box 及其子元素可用 */
  --size: 30;
}

.bar {
  color: var(--color);          /* 继承自 .box，值为 red */
  font-size: calc(var(--size) * 1px);  /* 与权重无关 */
}
```

> **重要**：CSS 变量的继承与选择器权重无关，完全遵循 DOM 树结构。

### 3.3 !important 与变量

```css
:root {
  --color: #fac !important;   /* !important 可作用于变量声明 */
}
```

---

## 四、行间样式与 JavaScript 操作 CSS 变量

### 4.1 行间样式设置变量

CSS 变量可以直接在 HTML 行间样式中定义和使用：

```html
<div class="container" style="--color: red">
  <div class="box" style="color: var(--color)">123</div>
</div>
```

### 4.2 JavaScript 操作 CSS 变量

通过 `style` 对象的 `getPropertyValue()` 和 `setProperty()` 方法来读取和设置 CSS 变量：

```javascript
const box = document.querySelector(".box");

// 获取 CSS 变量值
const color = box.style.getPropertyValue("--color");

// 设置 CSS 变量值
box.style.setProperty("--color", "green");
```

### 4.3 常用 API 总结

| 方法 | 说明 |
|------|------|
| `element.style.setProperty(name, value)` | 设置 CSS 变量 |
| `element.style.getPropertyValue(name)` | 获取行间样式中的 CSS 变量 |
| `getComputedStyle(element).getPropertyValue(name)` | 获取计算后的 CSS 变量值 |

---

## 五、calc() 函数

### 5.1 基本语法

`calc()` 允许在 CSS 中进行数学运算，支持加减乘除：

```css
.box {
  /* 加减法：运算符两侧必须有空格 */
  left: calc(50% - 150px);
  top: calc(50% - 150px);

  /* 乘除法 */
  flex-basis: calc((100% - 40px) / 3);
}
```

### 5.2 运算规则

| 运算符 | 注意事项 |
|--------|---------|
| `+` / `-` | 运算符两侧**必须有空格** |
| `*` / `/` | 无强制空格要求，但建议保持一致 |
| 单位 | 可以混合不同单位进行计算（如 `%` 和 `px`） |

### 5.3 calc() 与变量结合

`calc()` 与 CSS 变量结合使用，可以实现动态计算：

```css
.item {
  width: 8px;
  height: 25px;
  border-radius: 8px;
  background-color: #000;
  position: absolute;
  left: calc(50% - 4px);
  transform-origin: 0 150px;
  /* 变量参与旋转角度计算 */
  transform: rotateZ(calc(var(--index) * var(--base-deg)));
  /* 动画延迟动态计算 */
  animation: animate var(--time) linear infinite;
  animation-delay: calc(var(--index) * 0.05s - var(--time) / 2);
}
```

---

## 六、其他 CSS 函数

### 6.1 attr() 函数

获取 HTML 属性值，可用于 `content` 属性：

```css
a::after {
  content: '(' attr(href) ')';
}
```

### 6.2 min() / max() / clamp() 函数

```css
.box {
  /* clamp(最小值, 推荐值, 最大值) */
  /* 等价于 max(最小值, min(推荐值, 最大值)) */
  width: clamp(400px, 50%, 600px);
}
```

- `min(val1, val2, ...)` — 取最小值
- `max(val1, val2, ...)` — 取最大值
- `clamp(min, preferred, max)` — 限制在区间范围内

---

## 七、实战案例

### 7.1 案例：主题切换

通过切换 class 来改变 CSS 变量值，实现深色/浅色主题切换：

**CSS 部分：**

```css
.box {
  width: 300px;
  height: 100px;
  line-height: 100px;
  text-align: center;
  color: var(--color);
  background-color: var(--bg);
  transition: all .3s;
}

/* 深色主题 */
.dark-theme {
  --color: #fff;
  --bg: #000;
}

/* 浅色主题 */
.light-theme {
  --color: #fac;
  --bg: skyblue;
}
```

**HTML 部分：**

```html
<div class="box dark-theme">主题</div>
```

**JavaScript 部分：**

```javascript
const box = document.querySelector(".box");
box.addEventListener("click", () => {
  box.classList.toggle("dark-theme");
  box.classList.toggle("light-theme");
});
```

> **要点**：通过 `classList.toggle()` 切换不同的类名，每个类名定义不同的变量值，配合 `transition` 实现平滑过渡。

---

### 7.2 案例：3D 文字面板

鼠标移动时，通过 JavaScript 动态修改 CSS 变量，实现 3D 旋转效果：

**CSS 部分：**

```css
body {
  perspective: 800px;
}

.container {
  display: grid;
  width: 100vw;
  height: 100vh;
  place-items: center;
  transform-style: preserve-3d;
}

.inner {
  border-radius: 8px;
  font-size: 40px;
  padding: 20px 30px;
  color: #fff;
  background-image: linear-gradient(to right, skyblue, #e111ac);
  /* 通过 CSS 变量控制旋转角度，默认值为 0deg */
  transform: rotateX(var(--rx, 0deg)) rotateY(var(--ry, 0deg));
  transition: transform .3s ease;
}
```

**JavaScript 部分：**

```javascript
const innerBox = document.querySelector(".inner");
const rect = innerBox.getBoundingClientRect();

innerBox.onmousemove = function (e) {
  const x = e.clientX - rect.left;
  const y = e.clientY - rect.top;
  // 根据鼠标位置计算旋转角度
  innerBox.style.setProperty("--rx", `${-(y - rect.height / 2)}deg`);
  innerBox.style.setProperty("--ry", `${(x - rect.width / 2) / 15}deg`);
};

innerBox.onmouseleave = function () {
  // 鼠标离开时恢复初始状态
  innerBox.style.setProperty("--rx", 0);
  innerBox.style.setProperty("--ry", 0);
};
```

> **要点**：利用 `var()` 的默认值特性，鼠标离开时只需将变量重置为 `0`，`transition` 会自动处理过渡动画。

---

### 7.3 案例：环形加载动画

利用 CSS 变量 + `calc()` + JavaScript 动态创建元素，实现圆形排列的加载动画：

**CSS 部分：**

```css
.box {
  width: 300px;
  height: 300px;
  position: absolute;
  left: calc(50% - 150px);
  top: calc(50% - 150px);
}

.item {
  width: 8px;
  height: 25px;
  background-color: #000;
  border-radius: 8px;
  position: absolute;
  left: calc(50% - 4px);
  transform-origin: 0 150px;
  transform: rotateZ(calc(var(--index) * var(--base-deg)));
  animation: animate var(--time) linear infinite;
  animation-delay: calc(var(--index) * 0.05s - var(--time) / 2);
}

@keyframes animate {
  0%, 50% {
    background: #050c09;
    box-shadow: none;
  }
  51%, 100% {
    background: #38d2dd;
    box-shadow: 0 0 5px #38d2dd, 0 0 15px #38d2dd,
                0 0 30px #38d2dd, 0 0 60px #38d2dd,
                0 0 90px #38d2dd;
  }
}
```

**JavaScript 部分：**

```javascript
const ITEM_NUM = 30;
const baseDeg = 360 / ITEM_NUM;
const box = document.querySelector(".box");

// 设置动画总时长和基础角度
box.style.setProperty("--time", `${ITEM_NUM * 0.1}s`);
box.style.setProperty("--base-deg", `${baseDeg}deg`);

// 动态创建子元素，每个设置不同的 --index
for (let i = 0; i < ITEM_NUM; i++) {
  const div = document.createElement("div");
  div.classList.add("item");
  div.style.setProperty("--index", i);
  box.appendChild(div);
}
```

> **要点**：
> - `transform-origin: 0 150px` 将旋转中心偏移到圆心，实现环形排列
> - `animation-delay: calc(var(--index) * 0.05s - var(--time) / 2)` 利用延迟差制造波浪效果
> - JavaScript 动态设置 `--index`，每个元素的旋转角度和动画延迟都不同

---

## 八、总结

| 特性 | 说明 |
|------|------|
| `--变量名` | 定义 CSS 自定义属性 |
| `var(--变量名, 后备值)` | 使用变量，可设置后备值 |
| 作用域 | 遵循 DOM 树继承，与选择器权重无关 |
| `calc()` | 数学运算，支持混合单位 |
| `clamp(min, val, max)` | 将值限制在区间范围内 |
| JS 交互 | `setProperty()` / `getPropertyValue()` 操作变量 |
