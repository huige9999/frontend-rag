# 09. CSS 视觉效果

本章涵盖 CSS 中三大视觉增强能力：**滤镜（filter）**、**背景滤镜（backdrop-filter）** 和 **混合模式（blend-mode）**，帮助开发者在不借助图片处理工具的情况下，用纯 CSS 实现丰富的视觉效果。

---

## 一、CSS 滤镜（filter）

CSS `filter` 属性用于对元素本身进行图形渲染处理，如模糊、亮度调整、色调变化等。所有滤镜函数可以组合使用。

```css
/* 滤镜组合使用 */
filter: blur(5px) brightness(1.2) contrast(150%);
```

### 1.1 滤镜函数一览

| 滤镜函数 | 释义 | 示例 |
| --- | --- | --- |
| `blur(5px)` | 模糊 | 高斯模糊，参数值越大越模糊 |
| `brightness(2.4)` | 亮度 | 0 = 纯黑，1 = 正常，>1 更亮 |
| `contrast(200%)` | 对比度 | 0 = 纯灰(#808080)，1 = 正常，>1 对比更强 |
| `drop-shadow(4px 4px 8px blue)` | 投影 | 符合真实世界阴影规则的投影 |
| `grayscale(50%)` | 灰度 | 0 = 正常，1 = 完全灰度 |
| `hue-rotate(90deg)` | 色调旋转 | 改变色调，饱和度和亮度不变 |
| `invert(75%)` | 反相 | 0 = 正常，1 = 完全反相 |
| `opacity(25%)` | 透明度 | 效果类似 opacity 属性，但可启用硬件加速 |
| `saturate(230%)` | 饱和度 | 0 = 灰度，1 = 正常，>1 更饱和 |
| `sepia(60%)` | 褐色 | 老照片效果，1 = 深褐色 |

### 1.2 blur — 模糊

`blur()` 函数接受长度值（不支持百分比），参数表示高斯函数的标准偏差，即屏幕上互相融合的像素数量。值越大，模糊效果越明显。

```css
img {
  filter: blur(10px);
}
```

### 1.3 brightness — 亮度

参数支持数值和百分比值，范围是 0 到无穷大。0 或 0% 为纯黑色，1 或 100% 为正常亮度，大于 1 则提升亮度。

```css
img {
  filter: brightness(0);   /* 纯黑 */
  filter: brightness(1);   /* 正常 */
  filter: brightness(2.4); /* 提亮 */
}
```

### 1.4 contrast — 对比度

参数值 0 或 0% 表示毫无对比度，图像变成纯灰色色块（#808080）；1 或 100% 为正常对比度。

```css
img {
  filter: contrast(200%);
}
```

### 1.5 drop-shadow — 投影

使用 `drop-shadow()` 函数可以给元素设置**符合真实世界阴影规则**的投影效果。

```css
filter: drop-shadow(x偏移, y偏移, 模糊大小, 色值);
```

```css
img {
  filter: drop-shadow(3px 3px 3px #00f);
}
```

**drop-shadow 与 box-shadow 的区别：**

- `drop-shadow()` 不支持扩展参数（真实投影无扩展）
- `drop-shadow()` 没有内投影效果（不支持 inset）
- `drop-shadow()` 不支持投影叠加
- `drop-shadow()` 会跟随元素的真实轮廓（如虚线边框），而 `box-shadow` 只跟随盒模型矩形

```css
/* box-shadow：只跟随矩形边框 */
.box1 {
  box-shadow: 5px 5px 5px #000;
}

/* drop-shadow：跟随元素真实轮廓 */
.box2 {
  filter: drop-shadow(5px 5px 5px #000);
}
```

### 1.6 grayscale — 灰度

实现元素的去色效果，参数值为 1 或 100% 时完全灰度。

```css
img {
  filter: grayscale(1);
}
```

**实际用途：**
- 点亮图鉴效果（默认灰度，hover 恢复彩色）
- 网页整体灰度（哀悼日等场景）

### 1.7 hue-rotate — 色调旋转

调整元素的色调，饱和度和亮度保持不变，参数为角度值。

```css
img {
  filter: hue-rotate(90deg);
}
```

**案例：彩色文字动态变色**

利用 `hue-rotate` 配合渐变文字和动画，实现炫酷的彩虹文字效果：

```css
.box {
  -webkit-background-clip: text;
  color: transparent;
  background-image: linear-gradient(
    to right, #f00, #ff0, #0f0, #0ff, #00f, #f0f, #f00
  );
  animation: hue 2s linear infinite;
}

@keyframes hue {
  from { filter: hue-rotate(0deg); }
  to   { filter: hue-rotate(360deg); }
}
```

### 1.8 invert — 反相

让元素的亮度和色调同时反转，参数值 1 或 100% 时完全反相。

```css
img {
  filter: invert(50%);
}
```

### 1.9 saturate — 饱和度

参数值 0 或 0% 等同于 `grayscale(1)` 的灰度效果，1 为正常饱和度，大于 1 则饱和度提升。

```css
img {
  filter: saturate(50%);   /* 降低饱和度 */
  filter: saturate(230%);  /* 提升饱和度 */
}
```

### 1.10 sepia — 褐色（老照片效果）

让元素视觉效果向褐色靠拢，适合制作老照片风格。

```css
img {
  filter: sepia(60%);
  filter: sepia(1);  /* 深褐色 */
}
```

---

## 二、元素融合效果（filter 组合）

利用 `blur()` + `contrast()` 的组合，可以实现两个元素相互融合的视觉效果——当两个模糊的元素靠近时，它们的边缘会自然地"粘合"在一起。

**核心原理：** 外层容器设置高对比度 `contrast()`，内部元素设置模糊 `blur()`，两者配合产生融合。

```css
.container {
  filter: contrast(20);
  background-color: #fff;
}

.box1, .box2 {
  background-color: #00f;
  border-radius: 50%;
  filter: blur(10px);
}
```

加上动画后，元素在运动过程中会出现"水滴融合"般的效果：

```css
@keyframes move {
  from {
    transform: translateY(-100px);
  }
}

.box1 {
  animation: move 2s linear infinite alternate-reverse;
}
.box2 {
  animation: move 2s linear infinite alternate;
}
```

---

## 三、backdrop-filter 与毛玻璃效果

### 3.1 backdrop-filter 概述

`backdrop-filter` 属性用于对元素**背后的区域**（而非元素本身）应用滤镜效果，语法与 `filter` 几乎一致。

**filter 与 backdrop-filter 的区别：**

| 特性 | filter | backdrop-filter |
| --- | --- | --- |
| 作用对象 | 元素本身 | 元素背后的区域 |
| 主要用途 | 对元素内容进行图像处理 | 创建背景模糊和透明效果 |
| 使用方式 | 单独作用于元素 | 通常与背景相关属性配合使用 |

```css
/* filter：模糊元素本身 */
.filter {
  filter: blur(8px);
}

/* backdrop-filter：模糊元素背后的内容 */
.backdrop-filter {
  backdrop-filter: blur(8px);
}
```

### 3.2 毛玻璃效果

毛玻璃效果（Frosted Glass）是 `backdrop-filter` 最经典的应用场景，核心组合为：

- **半透明背景** (`background-color: rgba(...)`)
- **背景模糊** (`backdrop-filter: blur(...)`)
- **圆角边框** (`border-radius`)

```css
.item {
  width: 300px;
  padding: 20px 10px;
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 8px;
  backdrop-filter: blur(10px);
}
```

### 3.3 背景模糊+前景清晰

通过伪元素应用 `backdrop-filter`，可以实现背景模糊但前景内容保持清晰的效果：

```css
.grid {
  background-image: url(./image.png);
  background-size: 100%;
  position: relative;
}

/* 伪元素覆盖整个区域并应用模糊 */
.grid::before {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  backdrop-filter: blur(10px);
}

/* 前景图片通过 z-index 保持清晰 */
.grid img {
  position: relative;
  z-index: 1;
}
```

---

## 四、混合模式（blend-mode）

混合模式决定元素的内容如何与其父元素的内容和背景进行混合。CSS 提供了两个混合模式属性：

- **`mix-blend-mode`**：控制元素内容与**其下方内容**的混合方式
- **`background-blend-mode`**：控制元素的**多个背景层**之间的混合方式

### 4.1 混合模式六大分类

| 分类 | 模式 | 说明 |
| --- | --- | --- |
| **覆盖组** | `normal` | 默认值，不混合，单纯覆盖 |
| **去亮组** | `darken` | 取两者中较暗的值 |
| | `multiply` | 正片叠底，结果更暗 |
| | `color-burn` | 颜色加深，暗处更暗 |
| **去暗组** | `lighten` | 取两者中较亮的值 |
| | `plus-lighter` | 类似变亮，对中色调影响更大 |
| | `screen` | 滤色，整体变亮 |
| | `color-dodge` | 颜色减淡，亮的更亮 |
| **对比组** | `overlay` | 叠加，亮的更亮暗的更暗 |
| | `soft-light` | 柔光，效果更柔和 |
| | `hard-light` | 强光，效果更强烈 |
| **差值组** | `difference` | 差值，取绝对差 |
| | `exclusion` | 排除，类似差值但更柔和 |
| **色彩组** | `hue` | 色相混合，不改变饱和度和亮度 |
| | `saturation` | 饱和度混合，不改变色相和亮度 |
| | `color` | 颜色混合，同时改变色相和饱和度 |
| | `luminosity` | 亮度混合，只改变亮度 |

> 注意：`plus-lighter` 仅支持 `mix-blend-mode`，不支持 `background-blend-mode`。

### 4.2 常用混合模式详解

#### darken（变暗）

两张图叠在一起，谁亮就丢弃谁，保留较暗的部分。适合在白色/浅色背景上去除白色区域，提取暗色图形。

```css
img {
  mix-blend-mode: darken;
}
```

**案例：暗色图形融合**

将黑色剪影图（PNG 白色背景）与天空背景图融合，白色区域自动消失：

```css
.sky {
  background-image: url(./sky.jpg);
}
.sky img {
  mix-blend-mode: darken;
}
```

#### lighten（变亮）

与 `darken` 相反，两张图叠在一起，谁暗就丢弃谁，保留较亮的部分。适合在黑色/深色背景上提取亮色图形。

```css
img {
  mix-blend-mode: lighten;
}
```

#### multiply（正片叠底）

整体变暗。黑色与任何颜色混合结果为黑色，白色与任何颜色混合结果不变。

```css
.element {
  mix-blend-mode: multiply;
}
```

#### screen（滤色）

整体变亮。白色与任何颜色混合结果为白色，黑色与任何颜色混合结果不变。

```css
.element {
  mix-blend-mode: screen;
}
```

#### overlay（叠加）

亮的更亮，暗的更暗，是 `multiply` 和 `screen` 的结合。

```css
.element {
  mix-blend-mode: overlay;
}
```

#### difference（差值）

取两者颜色值的绝对差，常用于创建反色或对比效果。

```css
.element {
  mix-blend-mode: difference;
}
```

**案例：自动反色文字**

利用 `difference` 混合模式，文字可以在黑白色块交界处自动呈现对比色：

```css
.box {
  background-image: linear-gradient(to right, #000 50%, #fff 50%);
}
.content {
  color: #fff;
  mix-blend-mode: difference;
}
/* 文字在黑色区域显示为白色，在白色区域自动显示为黑色 */
```

---

## 五、实战案例

### 5.1 文字故障效果（Glitch Effect）

利用 `mix-blend-mode: lighten` + `filter: contrast()` + `text-shadow` + 动画，实现赛博朋克风格的文字故障闪烁效果。

**核心思路：**
- 主文字为白色
- `::before` 伪元素覆盖红色文字，带红色 `text-shadow` 和微小位移动画
- `::after` 伪元素覆盖青色文字，带青色 `text-shadow`，使用 `mix-blend-mode: lighten` 混合
- 两个伪元素以不同频率和延迟执行位移动画，产生红蓝色差抖动

```css
.text-magic {
  position: relative;
  color: white;
  font-size: 64px;
}

.text-magic::before {
  content: "渡一教育";
  position: absolute;
  top: 0;
  left: 0;
  color: red;
  z-index: 2;
  filter: contrast(200%);
  text-shadow: 1px 0 0 red;
  animation: move 0.95s infinite;
}

.text-magic::after {
  content: "渡一教育";
  position: absolute;
  top: 0;
  left: -1px;
  z-index: 3;
  color: cyan;
  filter: contrast(200%);
  text-shadow: -1px 0 0 cyan;
  mix-blend-mode: lighten;
  animation: move 1.1s infinite 0.2s;
}

@keyframes move {
  10%  { top: -0.4px; left: -1.1px; }
  20%  { top: 0.4px;  left: -0.2px; }
  30%  { left: 0.5px; }
  40%  { top: -0.3px; left: -0.7px; }
  50%  { left: 0.2px; }
  60%  { top: 1.8px;  left: -1.2px; }
  70%  { top: -1px;   left: 0.1px; }
  80%  { top: -0.4px; left: -0.9px; }
  90%  { left: 1.2px; }
  100% { left: -1.2px; }
}
```

### 5.2 元素融合效果

利用 `blur()` + 高 `contrast()` 的组合，两个模糊圆形靠近时会自然"融合"：

```css
.container {
  filter: contrast(20);
  background-color: #fff;
}

.box1, .box2 {
  background-color: #00f;
  border-radius: 50%;
  filter: blur(10px);
}

.box1 {
  width: 150px; height: 150px;
  animation: move 2s linear infinite alternate-reverse;
}
.box2 {
  width: 50px; height: 50px;
  animation: move 2s linear infinite alternate;
}
```

### 5.3 drop-shadow vs box-shadow 对比

对于非矩形元素（如虚线边框、不规则形状），`drop-shadow` 能生成跟随轮廓的阴影：

```css
/* box-shadow：只跟随矩形盒模型 */
.box1 {
  border: 10px dashed #fac;
  box-shadow: 5px 5px 5px #000;
}

/* drop-shadow：跟随元素真实轮廓（包括虚线间隙） */
.box2 {
  border: 10px dashed #fac;
  filter: drop-shadow(5px 5px 5px #000);
}
```

---

## 六、实用技巧总结

1. **滤镜可组合使用**：`filter: blur(2px) brightness(1.5) contrast(120%);`
2. **元素融合关键**：父容器 `contrast()` + 子元素 `blur()`，缺一不可
3. **毛玻璃三件套**：`backdrop-filter: blur()` + `rgba` 背景色 + `border-radius`
4. **去除白色背景**：使用 `mix-blend-mode: darken`
5. **去除黑色背景**：使用 `mix-blend-mode: lighten`
6. **文字自动反色**：`mix-blend-mode: difference`，文字颜色与背景自动取反
7. **故障效果核心**：双伪元素 + 不同色彩 `text-shadow` + `lighten` 混合 + 错位动画
8. **`drop-shadow` 适合不规则图形**：如 PNG 图标、虚线边框等，阴影跟随真实轮廓
