# 01. Border — 边框相关现代 CSS 属性

本节涵盖三大核心属性：`border-radius`（圆角）、`border-image`（边框图片）、`box-shadow`（盒阴影）。

---

## 一、border-radius（圆角）

### 1.1 基本语法

`border-radius` 用于设置元素的外边框圆角，可分别控制四个角。

```css
/* 四个角统一值 */
border-radius: 10px;

/* 左上  右上  右下  左下（顺时针） */
border-radius: 10px 20px 30px 40px;

/* 三个值：左上  右上+左下  右下 */
border-radius: 10px 20px 30px;

/* 两个值：左上+右下  右上+左下 */
border-radius: 10px 20px;
```

> **Tips:** 值的顺序与 `margin`/`padding` 一致，遵循顺时针规则：上 → 右 → 下 → 左。

### 1.2 水平/垂直半径分离写法

`border-radius` 支持用 `/` 分隔**水平半径**和**垂直半径**，从而实现椭圆弧效果：

```css
/* 语法：水平半径{1,4} / 垂直半径{1,4} */
border-radius: 30px 40px;

/* 等价于分别指定四个角的水平/垂直半径 */
border-radius: 70%;

/* 完整八值写法 */
border-radius: a b c d / e f g h;
```

单个角的精确控制：

```css
/* 单独设置某个角：水平半径 垂直半径 */
border-top-left-radius: 30px 40px;
```

### 1.3 缩放系数规则

当圆角半径之和超过元素尺寸时，浏览器会自动按比例缩小：

```
f = min(width/(a+b), width/(c+d), height/(e+f), height/(g+h))
```

例如，一个 350x350 的元素设置 `border-radius: 70%`，实际计算为 `350/700 = 0.5`，最终呈现为一个椭圆或圆形。

### 1.4 实战案例

#### 异形头像

利用不对称的圆角值 + 百分比，生成不规则形状：

```css
.box1 {
  width: 200px;
  height: 200px;
  background-image: url(./assets/wyz.jpg);
  background-size: cover;
  border-radius: 69% 31% 37% 63% / 57% 33% 67% 43%;
}
```

#### 异形相框

配合 `border` 使用，产生不规则边框效果：

```css
.box2 {
  border: 20px solid #fac;
  border-radius: 20% 80% 20% 80% / 80% 20% 80% 20%;
}
```

#### 列表角标

仅对单个角设置圆角，形成角标装饰：

```css
li::before {
  content: "1";
  display: block;
  position: absolute;
  width: 30px;
  height: 30px;
  background-color: #fac;
  /* 仅右下角为圆角 */
  border-bottom-right-radius: 100%;
  color: #fff;
}
```

#### 交叉圆弧装饰

利用伪元素 `::before` 和 `::after` 各设一个对角圆弧，产生交叉弧线效果：

```css
.box4::before {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 100% 0;
  border-top: 2px solid #fac;
  border-left: 2px solid #fca;
  border-right: 2px solid #caf;
  border-bottom: 2px solid #cfa;
}

.box4::after {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 0 100%;
  border-top: 2px solid #0ff;
  border-left: 2px solid #afc;
  border-right: 2px solid #f0f;
  border-bottom: 2px solid #acf;
}
```

> **Tips:** `border-radius: 100% 0` 表示左上和右下为完全圆角，右上和左下无圆角，从而画出四分之一圆弧。

---

## 二、border-image（边框图片）

通过图片来增强边框的表现力，常用于装饰性边框。

### 2.1 九宫格原理

`border-image` 的核心思想是**九宫格切割**：将一张图片按指定比例切成 9 个区域（4 角 + 4 边 + 中心），其中四角固定不变形，四边根据 `repeat` 规则拉伸或平铺，中心区域可选择性填充。

### 2.2 子属性详解

#### border-image-source

引入图片资源：

```css
border-image-source: url(./assets/border-img.jpg);
```

#### border-image-slice

将图片切割为九宫格，接受数字或百分比（1~4 个值）：

```css
/* 数字：像素值（不带单位） */
border-image-slice: 33.3% 33.3%;

/* fill 关键字：保留中心区域 */
border-image-slice: 33.3% fill;
```

> **Tips:** 对于一张 3x3 等分的图，`33.3%` 是最常用的切割值。

#### border-image-width

设置边框图片的宽度：

```css
border-image-width: 20px;
```

#### border-image-outset

让边框图片向外扩展，超出元素边界：

```css
border-image-outset: 20px;
```

#### border-image-repeat

控制四边图片的重复方式（1~2 个值）：

| 值 | 说明 |
|---|---|
| `stretch` | 拉伸图片以填充边框（**默认值**） |
| `repeat` | 平铺图片以填充边框 |
| `round` | 平铺图像，不能整数次平铺时自动缩放图片 |
| `space` | 平铺图像，不能整数次平铺时用空白间隙填充 |

```css
border-image-repeat: space;
```

### 2.3 完整示例

```css
.box {
  width: 100px;
  height: 100px;
  border: 20px solid #fac;
  border-image-source: url(./assets/border-img.jpg);
  border-image-slice: 33.3% 33.3%;
  border-image-width: 20px;
  border-image-repeat: space;
}
```

也支持简写形式：

```css
border-image: url(./assets/border-img.jpg) 33.3%;
```

> **Tips:** `border-image` 需要配合 `border` 使用，必须先设置 `border-width` 和 `border-style`，否则边框图片不会显示。

---

## 三、box-shadow（盒阴影）

### 3.1 基本语法

```css
/* 标准语法 */
box-shadow: 水平偏移量 垂直偏移量 模糊半径 扩张半径 阴影颜色;

/* 完整语法（含 inset 内阴影） */
box-shadow: inset 水平偏移量 垂直偏移量 模糊半径 扩张半径 阴影颜色;
```

| 参数 | 说明 |
|---|---|
| `inset` | 可选，将外部阴影改为内部阴影 |
| 水平偏移量 | 正值向右，负值向左 |
| 垂直偏移量 | 正值向下，负值向上 |
| 模糊半径 | 值越大阴影越模糊（不能为负值） |
| 扩张半径 | 正值扩大阴影面积，负值缩小 |
| 阴影颜色 | 支持 `rgba()` 实现半透明效果 |

### 3.2 基础示例

```css
/* 外阴影 + 内阴影组合 */
.box {
  width: 200px;
  height: 200px;
  background-color: #f20;
  box-shadow:
    inset 0 0 0 10px #0f0,   /* 绿色内阴影（无模糊，相当于内边框） */
    0 0 0 20px #00f;          /* 蓝色外阴影（无模糊，相当于外边框） */
}
```

> **Tips:** 当模糊半径为 0 时，`box-shadow` 会产生一条硬边阴影线，可以模拟额外的边框效果，且不占布局空间。

### 3.3 水滴效果案例

结合 `border-radius` 的不规则圆角 + 多层 `box-shadow`，可以制作逼真的水滴效果：

```css
.water {
  width: 200px;
  height: 200px;
  border-radius: 34% 66% 70% 30% / 30% 30% 70% 70%;
  box-shadow:
    inset 10px 10px 20px rgba(0, 0, 0, 0.2),       /* 内部暗部阴影 */
    inset -20px -20px 20px rgba(255, 255, 255, 0.5), /* 内部高光 */
    10px 10px 15px rgba(0, 0, 0, 0.3);              /* 外部投影 */
}

/* 高光点（小白色水滴反光） */
.water::before {
  content: '';
  position: absolute;
  width: 15px;
  height: 15px;
  background-color: #fff;
  border-radius: 44% 56% 52% 48% / 43% 38% 62% 57%;
  top: 20%;
  left: 40%;
}

/* 次级高光点 */
.water::after {
  content: '';
  position: absolute;
  width: 8px;
  height: 8px;
  background-color: #fff;
  border-radius: 44% 56% 52% 48% / 43% 38% 62% 57%;
  top: 40%;
  left: 30%;
}
```

> **Tips:** 制作水滴/玻璃质感的核心思路——`inset` 阴影一侧加亮（白色半透明），另一侧加暗（黑色半透明），再加上外部投影。

### 3.4 更精细的水滴（大尺寸版）

```css
.water {
  width: 300px;
  height: 300px;
  border-radius: 42% 58% 77% 23% / 40% 31% 69% 60%;
  box-shadow:
    inset 10px 20px 30px rgba(0, 0, 0, 0.5),        /* 深色内阴影 */
    inset -10px -10px 15px rgba(255, 255, 255, 0.8),  /* 亮色高光 */
    10px 10px 20px rgba(0, 0, 0, 0.3),                /* 主投影 */
    15px 20px 30px rgba(0, 0, 0, 0.05);               /* 柔和次投影 */
}

/* 高光用半透明白色 */
.water::before {
  content: "";
  position: absolute;
  width: 20px;
  height: 20px;
  top: 22%;
  left: 48%;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 51% 49% 35% 65% / 40% 54% 46% 60%;
}
```

### 3.5 box-shadow 像素画（马里奥案例）

`box-shadow` 可以叠加多个无模糊的阴影块，每个阴影就是一个"像素"，从而实现像素画效果：

```css
.marry {
  width: 1em;
  height: 1em;
  box-shadow:
    1em 0 0 #df2516,
    2em 0 0 #df2516,
    3em 0 0 #df2516,
    4em 0 0 #df2516,
    /* ... 每一行声明一个像素的位置和颜色 */
    0 1em 0 #df2516,
    1em 1em 0 #df2516;
  /* 更多像素点... */
}
```

> **Tips:** 原理——元素本身设为 `1em x 1em`，每个 `box-shadow` 偏移量以 `em` 为单位逐像素排列，模糊半径为 `0`，形成硬边色块。这是纯 CSS 像素画的核心技巧。

---

## 总结

| 属性 | 核心用途 | 关键点 |
|---|---|---|
| `border-radius` | 圆角 / 异形形状 | 支持 `水平/垂直` 分离写法，八值语法实现不规则形状 |
| `border-image` | 图片边框 | 九宫格切割，配合 `border` 使用 |
| `box-shadow` | 阴影 / 质感 / 像素画 | `inset` 内阴影 + 多层叠加实现光影效果 |
