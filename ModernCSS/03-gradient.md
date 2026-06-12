# CSS 渐变（Gradient）

CSS 渐变是 CSS 中第一次真正意义上使用纯 CSS 代码创建的图像。它可以应用在任何需要使用图像的场景，例如 `background-image`、`border-image` 等。

CSS 渐变本质上是一个 **CSS 函数**，返回值是一张图像。

---

## 一、线性渐变 linear-gradient

### 1.1 基本语法

```css
background-image: linear-gradient(渐变方向, 颜色1 变化区间, 颜色2 变化区间, ...);
```

### 1.2 渐变方向

| 方式 | 示例 | 说明 |
|------|------|------|
| 关键字 | `to right`、`to left`、`to top`、`to bottom` | 从一侧到另一侧 |
| 关键字组合 | `to top left`、`to left top` | 对角线方向 |
| 角度 | `45deg`、`90deg`、`180deg` | 精确控制方向 |
| 默认值 | `to bottom`（等同于 `180deg`） | 自上而下 |

> **角度参考**：`0deg` = 向上，`90deg` = 向右，`180deg` = 向下，`270deg` = 向左。

### 1.3 变化区间（渐变断点）

渐变断点用于精确控制颜色变化的位置：

- **一个值**：作为颜色变化的边界位置
- **两个值**：定义该颜色区间的起始和终止位置

```css
/* 颜色在 100px 处突然变化 —— 硬边界效果 */
background-image: linear-gradient(45deg, #fff 100px, skyblue 100px 200px, #fff 200px);
```

```css
/* 从左到右平滑渐变 */
background-image: linear-gradient(to right, #ffffff, #6dd5fa, #2980b9);
```

### 1.4 文字渐变效果

利用 `background-clip: text` 可以实现文字渐变效果：

```css
.box {
    background-image: linear-gradient(to right, #ffffff, #6dd5fa, #2980b9);
    -webkit-background-clip: text;
    color: transparent;
}
```

> **关键点**：需要将文字颜色设为 `transparent`，让背景图透出来。

### 1.5 重复线性渐变 repeating-linear-gradient

通过 `repeating-linear-gradient()` 可以创建重复的条纹图案：

```css
/* 网格背景 —— 两个渐变叠加 */
background-image:
    repeating-linear-gradient(transparent 0 10px, skyblue 10px 20px),
    repeating-linear-gradient(90deg, #fac 0 10px, transparent 10px 20px);
```

```css
/* 边框渐变 */
border: 10px solid;
border-image: repeating-linear-gradient(45deg, transparent 0 10px, skyblue 10px 20px) 20;
```

> **实用技巧**：重复渐变常用于创建网格线、条纹背景、斜线纹理等装饰性图案。

---

## 二、径向渐变 radial-gradient

### 2.1 基本语法

```css
background-image: radial-gradient(形状 半径大小 at 原点位置, 渐变颜色1 边界, 渐变颜色2 边界, ...);
```

### 2.2 原点位置

使用 `at` 关键字指定渐变中心，属性值与 `background-position` 一致：

| 方式 | 示例 |
|------|------|
| 关键字 | `at center`（默认值）、`at top`、`at left`、`at bottom` |
| 组合关键字 | `at left top`、`at right bottom` |
| 具体数值 | `at 0 0`、`at left 30px top 50px` |

### 2.3 形状

| 值 | 说明 |
|------|------|
| `circle` | 圆形渐变 |
| `ellipse` | 椭圆渐变（默认） |

> **提示**：当只提供一个半径值时，按圆形处理；提供两个值时，按椭圆处理。

### 2.4 半径大小关键字

| 关键字 | 描述 |
|--------|------|
| `closest-side` | 渐变中心距离容器**最近的边**作为终止位置 |
| `closest-corner` | 渐变中心距离容器**最近的角**作为终止位置 |
| `farthest-side` | 渐变中心距离容器**最远的边**作为终止位置 |
| `farthest-corner` | 渐变中心距离容器**最远的角**作为终止位置（**默认值**） |

### 2.5 代码示例

```css
/* 基本径向渐变 */
background-image: radial-gradient(
    closest-side at left 30px top 50px,
    red 0px 10px,
    blue 10px 20px,
    green 20px
);
```

### 2.6 重复径向渐变 repeating-radial-gradient

```css
/* 同心圆环效果 */
background-image: repeating-radial-gradient(skyblue 0 10px, #fff 10px 20px);
```

> **实用场景**：水波纹效果、同心圆图案、靶心图形等。

---

## 三、锥形渐变 conic-gradient

### 3.1 基本语法

```css
background-image: conic-gradient(from 起始角度 at 中心位置, 角渐变断点);
```

### 3.2 参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `from 角度` | 起始角度，默认 `0deg`（从上方开始） | `from 45deg` |
| `at 位置` | 中心位置，同 `background-position` | `at center`、`at top left` |
| 角渐变断点 | 使用角度值定义颜色变化边界 | `red 0 120deg` |

### 3.3 代码示例

**饼图效果：**

```css
/* 三等分饼图 */
background-image: conic-gradient(
    from 0deg at center,
    red 0 120deg,
    green 120deg 240deg,
    blue 240deg 360deg
);
border-radius: 50%;
```

**棋盘格效果：**

```css
/* 利用 background-size 平铺棋盘 */
background-size: 40px 40px;
background-image: conic-gradient(
    #fff 0 90deg, #000 90deg 180deg,
    #fff 180deg 270deg, #000 270deg 360deg
);
```

**色轮效果：**

```css
/* 彩虹色轮 */
border-radius: 50%;
background-image: conic-gradient(#f00, #ff0, #0f0, #0ff, #00f, #f00);
```

> **实用技巧**：锥形渐变非常适合制作饼图、色轮、加载动画（如 conic-spinner）以及棋盘格纹理等。

### 3.4 重复锥形渐变 repeating-conic-gradient

与线性、径向类似，锥形渐变也有重复版本：

```css
background-image: repeating-conic-gradient(red 0 30deg, blue 30deg 60deg);
```

---

## 四、三种渐变对比总结

| 特性 | linear-gradient | radial-gradient | conic-gradient |
|------|----------------|-----------------|----------------|
| 渐变方向 | 沿直线方向 | 从中心向外辐射 | 围绕中心旋转 |
| 定位方式 | 方向/角度 | `at` 中心位置 | `from` 角度 `at` 中心 |
| 典型场景 | 条纹、文字渐变 | 光晕、水波纹 | 饼图、色轮 |
| 重复版本 | `repeating-linear-gradient` | `repeating-radial-gradient` | `repeating-conic-gradient` |

---

## 五、实用技巧

1. **硬边界技巧**：当相邻颜色的断点值相同时，产生清晰的分界线而非平滑过渡
2. **多渐变叠加**：多个渐变可以用逗号分隔叠加，形成复杂图案
3. **渐变可用于**：`background-image`、`border-image`、`list-style-image` 等所有接受图像的属性
4. **渐变断点单位**：支持 `px`、`%`、`em` 以及角度单位 `deg`、`turn` 等
