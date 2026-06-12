# 08. CSS Grid 布局

## 一、Grid 基本概念

CSS Grid 是一种强大的二维布局系统，可以同时控制行和列的排列方式。

### 1.1 启用 Grid 布局

```css
.container {
  display: grid;        /* 块级网格容器 */
  display: inline-grid; /* 内联网格容器 */
}
```

- `grid`：容器外部保持块状特性，宽度默认 100%，不和内联元素在同一行显示。
- `inline-grid`：容器外部保持内联特性，可以和图片文字在同一行显示。

### 1.2 Grid 容器与子项属性一览

| **容器属性（Grid 容器）** | **子项属性（Grid 子项）** |
| --- | --- |
| grid-template-columns | grid-column-start |
| grid-template-rows | grid-column-end |
| grid-template-areas | grid-row-start |
| grid-template | grid-row-end |
| grid-auto-columns | grid-column |
| grid-auto-rows | grid-row |
| grid-auto-flow | grid-area |
| grid | justify-self |
| justify-items | align-self |
| align-items | place-self |
| place-items | |
| justify-content | |
| align-content | |
| place-content | |
| gap / row-gap / column-gap | |

### 1.3 核心术语

- **网格（Grid）**：由行和列构成的二维布局结构。
- **行（Row）**：水平方向的网格轨道。
- **列（Column）**：垂直方向的网格轨道。
- **网格线（Grid Line）**：划分网格的虚拟线条，从 1 开始编号，也支持负数（从末尾倒数）。
- **单元格（Cell）**：行与列交叉形成的最小单位。

---

## 二、grid-template-columns / grid-template-rows

定义网格容器的列宽和行高。

```css
.container {
  display: grid;
  grid-template-columns: 50px 50px 50px; /* 三列，每列 50px */
  grid-template-rows: 50px 50px;         /* 两行，每行 50px */
}
```

### 2.1 支持的参数类型

**长度值：** `px`、`em`、`rem`、`vw/vh`、`%`

**分数单位：** `fr`（fraction，代表可用空间的一份）

```css
grid-template-columns: 1fr 2fr 1fr; /* 三列，比例为 1:2:1 */
```

**关键词：**
- `auto`：自动计算大小
- `min-content`：内容的最小尺寸
- `max-content`：内容的最大尺寸

### 2.2 repeat() 函数

用于重复创建相同尺寸的轨道。

```css
grid-template-columns: repeat(4, 1fr);  /* 4 列等宽 */
grid-template-columns: repeat(3, 100px); /* 3 列，每列 100px */
```

- `auto-fill`：自动填充，根据容器大小创建尽可能多的轨道，空轨道保留空间。
- `auto-fit`：与 auto-fill 类似，但空轨道会被收缩为 0，不占据空间。

> **注意：** `repeat()` 函数只能作用在 `grid-template-columns` 和 `grid-template-rows` 这两个属性上。

### 2.3 minmax() 函数

设置轨道尺寸的范围。

```css
grid-template-columns: minmax(100px, 1fr); /* 最小 100px，最大 1fr */
```

### 2.4 fit-content() 函数

限制网格轨道尺寸在给定上限和内容所需最小值之间。

```css
/* fit-content(limit) = max(min-content, min(limit, max-content)) */
grid-template-columns: fit-content(200px);
```

### 2.5 网格线命名

可以给网格线取名字，方便后续引用。

```css
.container {
  grid-template-columns: [line-start] 50px [line-2] 50px [line-3] 50px [line-end];
  grid-template-rows: [row-start] 50px [row-end];
}
```

---

## 三、grid-template-areas

通过字符串直观地定义网格区域的布局结构。

```css
.container {
  display: grid;
  height: 100vh;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: 1fr 5fr 1fr;
  grid-template-areas:
    "头部 头部 头部 头部"
    "侧边栏 content content content"
    ". footer footer .";
}

.header  { grid-area: 头部;   background-color: aqua; }
.sidebar { grid-area: 侧边栏; background-color: #ff0; }
.content { grid-area: content; background-color: chartreuse; }
.footer  { grid-area: footer; background-color: blueviolet; }
```

**语法规则：**
- 每行字符串用引号包裹，字符串之间用空格分隔。
- 单元格名称可以使用中文或英文，`.` 代表空的单元格。
- 每个命名区域必须是**矩形**，L 型、凹凸型都是无效区域。
- 配合子项的 `grid-area` 属性使用。

### 3.1 grid-template 简写

`grid-template` 是 `grid-template-rows`、`grid-template-columns`、`grid-template-areas` 三个属性的简写。

**基本格式：**

```css
/* 行 / 列 */
grid-template: 1fr 5fr 1fr / repeat(4, 1fr);

/* 带区域名称 */
grid-template:
  "头部 头部 头部 头部" 1fr
  "侧边栏 content content content" 5fr
  ". footer footer ." 1fr
  / 1fr 1fr 1fr 1fr;
```

> **注意：** 包含 `<string>`（区域名称）的 `grid-template` 缩写不支持 `repeat()` 函数。

---

## 四、grid-auto-columns / grid-auto-rows

### 4.1 显式网格与隐式网格

- **显式网格**：通过 `grid-template-*` 明确定义的网格区域。
- **隐式网格**：超出显式定义范围的、自动生成的网格区域。

`grid-auto-columns` 和 `grid-auto-rows` 用于指定隐式网格轨道的尺寸。

```css
.grid {
  display: grid;
  grid-template: 20px 20px / 20px 20px; /* 显式：2行2列，每轨道 20px */
  grid-auto-rows: 50px;   /* 隐式行高 50px */
  grid-auto-columns: 50px; /* 隐式列宽 50px */
}
```

当子项数量超过显式定义的 2x2=4 个格子时，多出的子项会按照隐式网格的尺寸（50px）进行排列。

---

## 五、grid-auto-flow

控制网格项目的自动放置顺序。

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-flow: row; /* 默认值 */
}
```

| 属性值 | 说明 |
| --- | --- |
| `row`（默认） | 按行方向依次填充，一行填满再换行 |
| `column` | 按列方向依次填充，一列填满再换列 |
| `dense` | 启用"密集"打包算法，后续较小的项目会尝试填入前面空缺的位置 |
| `row dense` | 按行排列 + 密集打包 |
| `column dense` | 按列排列 + 密集打包 |

---

## 六、gap 间距属性

设置网格行与列之间的间隙。

```css
.container {
  display: grid;
  row-gap: 10px;    /* 行间距 */
  column-gap: 20px; /* 列间距 */
  gap: 10px 20px;   /* 简写：row-gap column-gap */
  gap: 10px;        /* 行列间距相同 */
}
```

---

## 七、对齐属性

### 7.1 justify-items（单元格内水平对齐）

控制每个子项在其所在单元格内的水平对齐方式。

```css
.container {
  justify-items: start | end | center | stretch;
}
```

| 属性值 | 描述 |
| --- | --- |
| `start` | 左对齐 |
| `end` | 右对齐 |
| `center` | 居中对齐 |
| `stretch`（默认） | 拉伸占满单元格整个宽度 |

### 7.2 align-items（单元格内垂直对齐）

控制每个子项在其所在单元格内的垂直对齐方式。

```css
.container {
  align-items: start | end | center | stretch | baseline;
}
```

| 属性值 | 描述 |
| --- | --- |
| `start` | 顶部对齐 |
| `end` | 底部对齐 |
| `center` | 居中对齐 |
| `stretch` | 拉伸占满整个单元格高度 |
| `baseline` | 按文字基线对齐（无文字则按元素底部对齐） |

> `normal` 是默认值，通常表现为 `stretch`；如果子项具有内在尺寸或比例（如 `<img>`），则表现为 `start`。

### 7.3 place-items 简写

```css
place-items: <align-items> <justify-items>;
place-items: center; /* 垂直和水平都居中 */
```

### 7.4 justify-content（网格整体水平分布）

控制网格整体在容器内的水平分布方式。**前提**：子项总尺寸小于容器尺寸。

```css
.container {
  justify-content: start | end | center | space-around | space-between | space-evenly;
}
```

| 属性值 | 描述 |
| --- | --- |
| `start` | 全部靠左 |
| `end` | 全部靠右 |
| `center` | 整体居中 |
| `space-around` | 每个项目两侧间距相等（项目间距 = 边距的 2 倍） |
| `space-between` | 首尾贴边，中间均匀分布 |
| `space-evenly` | 所有间距完全相等 |

### 7.5 align-content（网格整体垂直分布）

控制网格整体在容器内的垂直分布方式，属性值与 `justify-content` 相同。

```css
.container {
  align-content: start | end | center | space-around | space-between | space-evenly;
}
```

### 7.6 place-content 简写

```css
place-content: <align-content> <justify-content>;
place-content: center center;
```

---

## 八、Grid 子项属性（项目定位）

### 8.1 grid-column-start / grid-column-end / grid-row-start / grid-row-end

通过网格线编号指定项目所占的区域范围。

```css
.item-1 {
  grid-column-start: 1;   /* 起始列线 */
  grid-column-end: 3;     /* 结束列线 */
  grid-row-start: 1;      /* 起始行线 */
  grid-row-end: 3;        /* 结束行线 */
}
```

**要点**：四条线合围成一块区域，找线对应位置即可。

也支持使用命名的网格线：

```css
.item {
  grid-column-start: A;
  grid-column-end: B;
  grid-row-start: 2;
  grid-row-end: 4;
}
```

### 8.2 span 关键字

`span` 指定项目跨越的轨道数量，相当于合并单元格。

```css
.item {
  grid-column: 1 / span 2; /* 从第1条线开始，跨越2个轨道 */
  grid-row: 1 / span 3;    /* 从第1条线开始，跨越3个轨道 */
}
```

> **注意：** 不建议在 `start` 和 `end` 同时使用 `span <number>` 语法，因为 `end` 中设置的 `span` 值不会产生效果。

### 8.3 缩写属性

```css
/* grid-column: start / end */
.item {
  grid-column: 1 / 3;
  grid-row: 2 / 5;
}

/* grid-area: row-start / column-start / row-end / column-end */
.item {
  grid-area: 1 / 2 / 3 / 4;
}
```

### 8.4 grid-area

既可以配合 `grid-template-areas` 使用区域名称，也可以直接用线号指定范围。

```css
/* 使用区域名称（配合 grid-template-areas） */
.header { grid-area: 头部; }

/* 使用线号：row-start / col-start / row-end / col-end */
.item   { grid-area: 1 / 2 / 3 / 4; }
```

---

## 九、justify-self / align-self（子项自身对齐）

单独控制某个子项在其单元格内的对齐方式，优先级高于容器级别的 `justify-items` / `align-items`。

```css
.item {
  justify-self: auto | normal | stretch | start | end | center | baseline;
  align-self: auto | normal | stretch | start | end | center | baseline;
}
```

| 属性值 | 描述 |
| --- | --- |
| `auto`（默认） | 继承容器上的 `justify-items` / `align-items` 值 |
| `normal` | 通常表现为 `stretch`，内在尺寸元素表现为 `start` |
| `stretch` | 子项拉伸填满 |
| `start` | 起始对齐 |
| `end` | 末尾对齐 |
| `center` | 居中对齐 |
| `baseline` | 按文本基线对齐 |

### place-self 简写

```css
.item {
  place-self: <align-self> <justify-self>;
}
```

---

## 十、grid 简写属性

`grid` 是以下属性的缩写集合：`grid-template-rows`、`grid-template-columns`、`grid-template-areas`、`grid-auto-rows`、`grid-auto-columns`、`grid-auto-flow`。

**四大类语法：**

```css
/* 1. 重置 */
grid: none;

/* 2. 等同于 grid-template */
grid: <grid-template>;

/* 3. 显式行 + 隐式列（auto-flow 在斜线右侧） */
grid: <grid-template-rows> / [ auto-flow && dense? ] <grid-auto-columns>?;

/* 4. 隐式行 + 显式列（auto-flow 在斜线左侧） */
grid: [ auto-flow && dense? ] <grid-auto-rows>? / <grid-template-columns>;
```

**实际示例：**

```css
/* 定义2行(50px) + 自动列流(50px) */
.grid {
  grid: 50px 50px / auto-flow 50px;
}

/* 等同于分开写： */
.grid {
  grid-template-rows: 50px 50px;
  grid-auto-flow: column;
  grid-auto-columns: 50px;
}
```

---

## 十一、实战案例：Bilibili 布局

模拟 B 站首页的网格布局：左侧轮播图占 2 行 2 列，右侧排列视频卡片。

```css
.grid {
  display: grid;
  width: 1226px;
  height: 500px;
  margin: 0 auto;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
}

/* 轮播图：跨 2 行 2 列 */
.lbt {
  grid-area: 1 / 2 / 3 / 4; /* row-start / col-start / row-end / col-end */
  background-color: #ff0;
}

/* 视频卡片 */
.item {
  width: 250px;
  height: 200px;
  background-color: #0ff;
}
```

**布局解析：**
- 5 列等宽布局（`repeat(5, 1fr)`）
- 轮播图从第 2 列第 1 行开始，跨越到第 4 列第 3 行
- 其余视频卡片自动填充剩余空间

---

## 十二、实战案例：Grid 项目定位

通过网格线命名和 `grid-column` / `grid-row` 精确控制项目位置。

```css
.grid {
  display: grid;
  grid-template-columns: 50px 50px 50px 50px;
  grid-template-rows: 50px 50px 50px 50px;
}

.item1 {
  grid-column: 1 / 3; /* 占第1~2列 */
  grid-row: 1 / 3;    /* 占第1~2行 */
  background-color: #fac;
}

.item2 {
  grid-column: 3 / 5; /* 占第3~4列 */
  grid-row: 2 / 5;    /* 占第2~4行 */
  background-color: #cfa;
}
```

**带命名网格线的定位：**

```css
.grid {
  display: grid;
  grid:
    [A-start] 100px [A-end B-start] 100px [B-end C-start] 100px [C-end]
    / [A-start] 100px [A-end B-start] 100px [B-end C-start] 100px [C-end];
}

.item {
  grid-column-start: A;
  grid-column-end: B;
  grid-row-start: 2;
  grid-row-end: 4;
  background-color: #f0f;
}
```

---

## 十三、要点总结

| 概念 | 关键要点 |
| --- | --- |
| Grid vs Flexbox | Grid 是二维布局（行列同时控制），Flexbox 是一维布局 |
| fr 单位 | 代表可用空间的比例份额，类似 Flexbox 的 `flex` |
| repeat() | 快速创建重复轨道，支持 `auto-fill` / `auto-fit` |
| 显式 vs 隐式网格 | 显式通过 template 定义，隐式通过 auto 属性控制 |
| grid-auto-flow | 控制自动放置方向，`dense` 可紧凑填充空位 |
| gap | 替代传统 margin 设置网格间距 |
| 对齐属性 | items 控制单元格内对齐，content 控制网格整体分布 |
| grid-area | 既支持区域名称也支持线号，是最灵活的定位方式 |
