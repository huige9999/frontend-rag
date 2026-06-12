# 07. CSS Flex 弹性布局

Flexbox（弹性盒模型）是一种一维布局模式，提供了一种更加有效的方式来布局、对齐和分配容器内项目的空间，即使它们的大小是动态的或者未知的。Flexbox 布局的核心思想是让容器能够改变其子元素的宽度、高度和顺序，以最好地填充可用空间。

在 Flexbox 出现之前，CSS 中的浮动（float）和定位（position）并不算真正的布局方案。Flexbox 是 CSS 中第一批真正的布局工具之一。

---

## 一、声明 Flex 布局

```css
.container {
  display: flex;        /* 块级 Flex 容器 */
  /* display: inline-flex; */  /* 内联级 Flex 容器 */
}
```

### 两个核心概念

| 概念 | 说明 |
|---|---|
| **Flex 容器（Flex Container）** | 将 `display` 设置为 `flex` 或 `inline-flex` 的元素 |
| **Flex 项目（Flex Item）** | Flex 容器内的直接子元素，自动成为 Flex 项目 |

### flex vs inline-flex

- `display: flex` — 容器表现为**块级元素**，未设置宽度时宽度等同于父容器（`width: 100%`）
- `display: inline-flex` — 容器表现为**内联元素**，未设置宽度时宽度等同于所有 Flex 项目的宽度和

> **使用建议：** 使用 `display: inline-flex` 时最好结合 `min-width` 和 `min-height` 一起使用，不建议显式设置 `width` 和 `height`。

### 设置 display: flex 后发生了什么？

1. Flex 项目会**水平单行排列**，无论多少个项目都会在一行内展示，空间不足时会被**压缩**而非换行
2. 容器的格式化上下文变为 **FFC（Flexbox Formatting Context）**
3. 以下属性在 Flex 项目上**不再生效**：
   - `float` 和 `clear` — 不会让 Flex 项目脱离文档流
   - `vertical-align` — 不起作用
   - `::first-line` 和 `::first-letter` 伪元素 — 在 Flex 容器上不起作用

---

## 二、Flex 容器属性

### 2.1 flex-direction — 主轴方向

决定 Flex 项目在容器中的排列方向。

```css
.container {
  display: flex;
  flex-direction: row;            /* 默认值，水平从左到右 */
  /* flex-direction: row-reverse; */   /* 水平从右到左 */
  /* flex-direction: column; */        /* 垂直从上到下 */
  /* flex-direction: column-reverse; */ /* 垂直从下到上 */
}
```

| 值 | 说明 |
|---|---|
| `row` | 默认值，沿文本书写方向水平排列（LTR 从左到右） |
| `row-reverse` | 与 `row` 方向相反 |
| `column` | 垂直排列，从上到下 |
| `column-reverse` | 垂直排列，从下到上 |

### 2.2 flex-wrap — 换行控制

控制 Flex 项目是否换行。默认情况下所有项目排在一条线上，空间不足时会压缩。

```css
.container {
  display: flex;
  flex-wrap: nowrap;    /* 默认值，不换行，项目会被压缩 */
  /* flex-wrap: wrap; */       /* 空间不足时自动换行 */
  /* flex-wrap: wrap-reverse; */ /* 换行，但方向反转 */
}
```

| 值 | 说明 |
|---|---|
| `nowrap` | 默认值，不换行，项目缩小或溢出 |
| `wrap` | 空间不足时换行，新行在下方 |
| `wrap-reverse` | 换行但方向反转，新行在上方 |

### 2.3 flex-flow — 简写属性

`flex-direction` 和 `flex-wrap` 的简写形式。

```css
.container {
  flex-flow: row nowrap;       /* 默认值 */
  /* flex-flow: column wrap; */
  /* flex-flow: wrap; */          /* 仅设置 wrap，direction 使用默认值 */
}
```

### 2.4 justify-content — 主轴对齐

控制 Flex 项目在**主轴**方向上的对齐和分布方式。

```css
.container {
  display: flex;
  justify-content: flex-start;     /* 默认值，起点对齐 */
  /* justify-content: flex-end; */       /* 终点对齐 */
  /* justify-content: center; */         /* 居中对齐 */
  /* justify-content: space-between; */  /* 两端对齐，项目间等距 */
  /* justify-content: space-around; */   /* 项目两侧间距相等 */
  /* justify-content: space-evenly; */   /* 完全均匀分布 */
}
```

| 值 | 说明 |
|---|---|
| `flex-start` | 项目靠近主轴起点排列 |
| `flex-end` | 项目靠近主轴终点排列 |
| `center` | 项目在主轴上居中 |
| `space-between` | 平均分布，首项目靠起点，末项目靠终点 |
| `space-around` | 平均分布，每个项目两侧间距相同（项目间距 = 边缘间距 x2） |
| `space-evenly` | 完全均匀分布，间距与边缘间距都相等 |

### 2.5 align-items — 交叉轴对齐（单行）

控制所有 Flex 项目在**交叉轴**上的对齐方式。

```css
.container {
  display: flex;
  align-items: stretch;          /* 默认值，拉伸填满 */
  /* align-items: flex-start; */      /* 交叉轴起点对齐 */
  /* align-items: flex-end; */        /* 交叉轴终点对齐 */
  /* align-items: center; */          /* 交叉轴居中 */
  /* align-items: baseline; */        /* 基线对齐 */
}
```

| 值 | 说明 |
|---|---|
| `stretch` | 默认值，拉伸填满容器交叉轴空间（未设置具体尺寸时） |
| `flex-start` | 向交叉轴起始边界对齐 |
| `flex-end` | 向交叉轴结束边界对齐 |
| `center` | 在交叉轴上居中 |
| `baseline` | 根据基线对齐 |

### 2.6 align-content — 交叉轴对齐（多行）

控制**多行** Flex 项目在交叉轴方向上的分布方式。只在项目换行（`flex-wrap: wrap`）时才有效果。

```css
.container {
  display: flex;
  flex-wrap: wrap;
  align-content: stretch;          /* 默认值，拉伸占满 */
  /* align-content: flex-start; */      /* 所有行紧靠交叉轴起始边 */
  /* align-content: flex-end; */        /* 所有行紧靠交叉轴结束边 */
  /* align-content: center; */          /* 所有行在交叉轴方向居中 */
  /* align-content: space-between; */   /* 均匀分布，首行靠起点，末行靠终点 */
  /* align-content: space-around; */    /* 均匀分布，每行前后间距相同 */
  /* align-content: space-evenly; */    /* 完全均匀分布 */
}
```

> **align-items vs align-content 的区别：**
> - `align-items` — 用于单行 Flex 项目的对齐
> - `align-content` — 用于多行 Flex 项目之间的间隔和分布
> - 如果 Flex 项目不换行，`align-content` 不会有任何效果

### 2.7 Flex 对齐特性速记

通过属性名的命名规律来记忆：

| 关键词 | 含义 |
|---|---|
| `justify-` | 主轴方向 |
| `align-` | 交叉轴方向 |
| `-items` | 控制所有项目 |
| `-content` | 控制整体布局/多行分布 |
| `-self` | 控制单个项目自身（用在子元素上） |

---

## 三、Flex 项目属性

### 3.1 order — 排列顺序

在不改变 DOM 结构的前提下，通过数值控制 Flex 项目的排列顺序。数值越小越靠前，默认值为 `0`。

```css
.item-1 { order: 2; }   /* 排在后面 */
.item-2 { order: -1; }  /* 排在最前面 */
.item-3 { order: 0; }   /* 默认位置 */
```

> **实用技巧：** `order` 可以是负数，适合在不修改 HTML 结构的情况下调整视觉顺序。

### 3.2 flex-grow — 放大比例

控制 Flex 项目在**主轴上的增长比例**。定义每个项目相对于其他项目在剩余空间中分配的比例。默认值为 `0`（不放大）。

```css
.item {
  flex-grow: 0;    /* 默认值，不放大 */
}
.item-grow {
  flex-grow: 1;    /* 占据剩余空间 */
}
```

**分配规则：** 剩余空间按照各项目的 `flex-grow` 值比例分配。例如三个项目分别设置 `1:1:1`，则每个项目各占剩余空间的 1/3。

> **应用场景：** Sticky Footer 布局、三栏（圣杯）布局

### 3.3 flex-shrink — 缩小比例

控制 Flex 项目在**主轴上的收缩比例**。当空间不足时，每个项目按比例缩小。默认值为 `1`（等比缩小）。

```css
.item {
  flex-shrink: 1;    /* 默认值，等比缩小 */
}
.item-no-shrink {
  flex-shrink: 0;    /* 不缩小 */
}
```

**收缩计算公式：**

```
每个项目的收缩量 = (自身宽度 × 自身 flex-shrink) / (所有项目的 宽度×flex-shrink 之和) × 溢出总量
```

**示例：** 容器宽度 100px，三个项目宽度分别为 50px、100px、150px，总宽度 300px，溢出 200px：

```css
/* 假设三个项目 flex-shrink 均为 1 */
/* total = 50×1 + 100×1 + 150×1 = 300 */
/* 项目1收缩: 50/300 × 200 ≈ 33.3px → 最终宽度 ≈ 16.7px */
/* 项目2收缩: 100/300 × 200 ≈ 66.7px → 最终宽度 ≈ 33.3px */
/* 项目3收缩: 150/300 × 200 = 100px → 最终宽度 = 50px */
```

### 3.4 flex-basis — 初始尺寸

设置 Flex 项目在主轴上的**初始尺寸**，在分配多余空间之前的基本大小。默认值为 `auto`。

```css
.item {
  flex-basis: auto;        /* 默认值，使用项目本身的尺寸 */
  /* flex-basis: 100px; */      /* 指定固定初始宽度 */
  /* flex-basis: 30%; */        /* 支持百分比 */
}
```

**flex-basis 支持的关键字：**

| 关键字 | 说明 |
|---|---|
| `auto` | 使用项目自身的 width/height |
| `content` | 与 `max-content` 效果一致 |
| `max-content` | 文字不换行时的最大宽度 |
| `min-content` | 文字自然换行时的最小宽度 |
| `fit-content` | 根据内容自适应 |

**flex-basis 与 width 的关系：**

当 `flex-basis` 和 `width` 同时设置时，最终尺寸为：

```
max(flex-basis, min(width, 内容宽度))
```

> **最佳实践：** 在 Flex 布局中优先使用 `flex-basis` 而非 `width` 来设置项目宽度。

### 3.5 flex — 缩写属性

`flex-grow`、`flex-shrink` 和 `flex-basis` 三个属性的简写。

```css
.item {
  flex: flex-grow flex-shrink flex-basis;
}
```

**常用值速查表：**

| 写法 | 等同于 | 说明 |
|---|---|---|
| `flex: initial` | `0 1 auto` | 初始值，常用 |
| `flex: 0` | `0 1 0%` | 适用场景较少 |
| `flex: none` | `0 0 auto` | 不放大不缩小，推荐用于固定尺寸项目 |
| `flex: 1` | `1 1 0%` | 自动填充剩余空间，推荐 |
| `flex: auto` | `1 1 auto` | 根据内容分配空间，适用场景少但有用 |

**尺寸优先级：**

```
min-width/max-width > 弹性尺寸（flex-grow, flex-shrink）> 元素尺寸（width/flex-basis）
```

### 3.6 align-self — 单个项目交叉轴对齐

允许单个 Flex 项目覆盖容器的 `align-items` 属性，独立控制自身在交叉轴上的对齐方式。

```css
.item {
  align-self: auto;          /* 继承父容器 align-items 的值 */
  /* align-self: flex-start; */   /* 交叉轴起始对齐 */
  /* align-self: flex-end; */     /* 交叉轴结束对齐 */
  /* align-self: center; */       /* 交叉轴居中 */
  /* align-self: baseline; */     /* 基线对齐 */
  /* align-self: stretch; */      /* 拉伸填满 */
}
```

---

## 四、gap — 间隙属性

`gap` 属性用于设置 Flex 项目之间的间隙，是 `row-gap` 和 `column-gap` 的简写形式。

```css
.container {
  display: flex;
  gap: 10px;             /* 行和列间隙都是 10px */
  /* gap: 10px 20px; */       /* 行间隙 10px，列间隙 20px */
}
```

**分别设置行间隙和列间隙：**

```css
.container {
  display: flex;
  flex-wrap: wrap;
  row-gap: 10px;         /* 行间距 */
  column-gap: 20px;      /* 列间距 */
}
```

> **注意：** `gap` 设置的是项目之间的间隙，不影响容器边缘与首个/末个项目之间的距离。

---

## 五、实用案例

### 5.1 圣杯布局（三栏布局）

经典的三栏布局：左右固定宽度，中间自适应。

```css
.container {
  display: flex;
}
.left {
  width: 100px;
  height: 100px;
  background-color: #fac;
}
.right {
  width: 200px;
  height: 100px;
  background-color: #fca;
  order: 2;
}
.center {
  flex-grow: 1;      /* 自动填充剩余空间 */
  height: 200px;
  background-color: #cfa;
  order: 1;
}
```

```html
<div class="container">
  <div class="center"></div>
  <div class="left"></div>
  <div class="right"></div>
</div>
```

> **关键点：** 使用 `flex-grow: 1` 让中间栏填充剩余空间，配合 `order` 属性调整视觉顺序，HTML 中中间栏可以放在最前面有利于 SEO。

### 5.2 Sticky Footer 布局

当内容较少时，Footer 始终贴在页面底部；内容较多时，Footer 在内容下方正常显示。

```css
body {
  margin: 0;
}
.container {
  display: flex;
  flex-direction: column;   /* 垂直排列 */
  min-height: 100vh;         /* 最小高度为视口高度 */
}
.header {
  height: 100px;
  background-color: #fac;
}
.main {
  background-color: #acf;
  flex-grow: 1;              /* 自动填充剩余空间 */
}
.footer {
  height: 199px;
  background-color: #cfa;
}
```

```html
<div class="container">
  <div class="header">HEADER</div>
  <div class="main">MAIN</div>
  <div class="footer">FOOTER</div>
</div>
```

> **关键点：** 容器设置 `min-height: 100vh` 和 `flex-direction: column`，主内容区域使用 `flex-grow: 1` 自动撑开剩余空间，将 Footer 推到页面底部。

### 5.3 列表导航

等分导航栏，每个导航项自动平分可用空间。

```css
ul {
  display: flex;
  height: 100px;
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  flex: auto;               /* 等分可用空间 */
  text-align: center;
  line-height: 100px;
  border-left: 1px solid #fff;
  background-color: #0ff;
}
```

```html
<ul>
  <li>首页</li>
  <li>商品页</li>
  <li>购物车页</li>
  <li>我的</li>
</ul>
```

> **关键点：** `flex: auto`（等同于 `1 1 auto`）让每个导航项按比例平分容器宽度。

### 5.4 响应式卡片网格（结合 gap）

使用 `flex-wrap` + `gap` 实现自适应卡片布局。

```css
.container {
  width: 400px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;               /* 行列间距都是 10px */
  /* 也可以分别设置：row-gap: 10px; column-gap: 20px; */
}
.card {
  flex: 1 1 100px;         /* 最小宽度 100px，可放大可缩小 */
  height: 100px;
}
```

> **关键点：** `flex: 1 1 100px` 设置最小基准宽度为 100px，`flex-wrap: wrap` 允许换行，`gap` 控制间距，实现优雅的响应式布局。

---

## 六、属性总览

### Flex 容器属性

| 属性 | 作用 | 默认值 |
|---|---|---|
| `display` | 声明 Flex 容器 | — |
| `flex-direction` | 主轴方向 | `row` |
| `flex-wrap` | 是否换行 | `nowrap` |
| `flex-flow` | direction + wrap 简写 | `row nowrap` |
| `justify-content` | 主轴对齐 | `flex-start` |
| `align-items` | 交叉轴对齐（单行） | `stretch` |
| `align-content` | 交叉轴对齐（多行） | `stretch` |
| `gap` | 项目间隙 | `normal` |

### Flex 项目属性

| 属性 | 作用 | 默认值 |
|---|---|---|
| `order` | 排列顺序 | `0` |
| `flex-grow` | 放大比例 | `0` |
| `flex-shrink` | 缩小比例 | `1` |
| `flex-basis` | 初始尺寸 | `auto` |
| `flex` | grow + shrink + basis 简写 | `0 1 auto` |
| `align-self` | 单项目交叉轴对齐 | `auto` |
