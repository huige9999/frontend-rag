# CSS clip-path 裁剪

## 一、基本概念

`clip-path` 属性用于创建一个裁剪区域，只有该区域内的内容才会显示，区域外的部分会被隐藏（裁掉）。

**核心优势：**
- 以前裁剪三角形需要用 `border` 模拟，现在一行代码搞定
- 支持多种裁剪方式：基本图形（圆形、椭圆、多边形、矩形）和 SVG 路径
- 可以与 `transition`、`animation` 结合实现流畅的动画效果

**基本语法：**

```css
clip-path: <clip-source> | <basic-shape> | <geometry-box>;
```

**裁剪方式分类：**

| 类型 | 语法 | 说明 |
|------|------|------|
| SVG 路径 | `url(#id)` | 引用 SVG 中定义的裁剪路径 |
| 矩形 | `inset()` | 内缩矩形裁剪 |
| 圆形 | `circle()` | 圆形裁剪 |
| 椭圆 | `ellipse()` | 椭圆形裁剪 |
| 多边形 | `polygon()` | 自定义多边形裁剪 |
| 路径 | `path()` | 使用 SVG path 语法裁剪 |

---

## 二、clip-path: inset() — 矩形裁剪

`inset()` 从四个方向向内缩进，定义一个矩形裁剪区域。

```css
clip-path: inset(top right bottom left);
```

**参数说明：**
- 四个值分别表示上、右、下、左的内缩量
- 支持像素 `px`、百分比 `%` 等单位
- 值越大，裁剪掉的区域越多

```css
/* 上方和下方各裁掉 20px */
clip-path: inset(20px 0 20% 0);

/* 只保留顶部 4% 的窄条 */
clip-path: inset(0 0 96% 0);

/* 只保留左侧窄条 */
clip-path: inset(0 96% 0 0);

/* 只保留底部窄条 */
clip-path: inset(96% 0 0 0);
```

> **实用技巧：** 通过将某个方向的值设为极大值（如 96%），可以只保留元素某一条边的窄条，非常适合做边框动画效果。

---

## 三、clip-path: circle() — 圆形裁剪

`circle()` 定义一个圆形裁剪区域。

```css
clip-path: circle(半径 at x y);
```

**参数说明：**
- 第一个参数：圆的半径，支持 `px`、`%` 等单位
- `at x y`：圆心位置，支持像素、百分比、关键词（`top`、`bottom`、`left`、`right`、`center`）

```css
/* 以元素中心为圆心，200px 为半径 */
clip-path: circle(200px at 50%);

/* 以左上角为圆心，30px 为半径（小圆点） */
clip-path: circle(30px at 0 0);

/* 以右下角为圆心，30px 为半径 */
clip-path: circle(30px at bottom right);

/* 以右下角为圆心，500px 为半径（接近完全展开） */
clip-path: circle(500px at bottom right);
```

> **实用技巧：** 通过 `transition` 让半径从极小值过渡到极大值，可以实现圆形展开/收缩的遮罩动画。

---

## 四、clip-path: ellipse() — 椭圆裁剪

`ellipse()` 定义一个椭圆形裁剪区域，可以分别控制水平和垂直方向的半径。

```css
clip-path: ellipse(水平半径 垂直半径 at x y);
```

**参数说明：**
- 第一个参数：水平方向的半径
- 第二个参数：垂直方向的半径
- `at x y`：椭圆中心位置

```css
/* 水平半径 50px，垂直半径 60px，中心在 (100px, 100px) */
clip-path: ellipse(50px 60px at 100px 100px);
```

> **实用技巧：** 椭圆裁剪常用于非正方形元素上的圆形效果，或需要拉伸变形的裁剪场景。

---

## 五、clip-path: polygon() — 多边形裁剪

`polygon()` 通过指定多个顶点坐标来定义任意多边形裁剪区域，是最灵活的裁剪方式。

```css
clip-path: polygon(x1 y1, x2 y2, x3 y3, ...);
```

**参数说明：**
- 每对 `x y` 值表示一个顶点坐标
- 坐标支持百分比 `%` 和像素 `px`
- 多个顶点按顺序连线形成裁剪区域

```css
/* 三角形：顶部中间为起点，左下和右下为另外两个点 */
clip-path: polygon(50% 0, 0 100%, 100% 100%);

/* 菱形 */
clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);

/* 五边形 */
clip-path: polygon(50% 0%, 100% 38%, 82% 100%, 18% 100%, 0% 38%);

/* 星形 */
clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%);
```

> **实用技巧：** 可以使用 [Clippy — CSS clip-path 生成器](https://bennettfeely.com/clippy/) 工具快速生成各种多边形裁剪代码。

---

## 六、clip-path 与 SVG 路径

可以使用 SVG 中定义的 `<clipPath>` 来实现更复杂的裁剪形状（如心形、曲线等）。

**HTML 中定义 SVG 裁剪路径：**

```html
<svg>
  <defs>
    <clipPath id="heart">
      <path d="M 300 350 C 100 150 200 50 300 150 C 500 0 400 300 300 350"></path>
    </clipPath>
  </defs>
</svg>
```

**CSS 中引用：**

```css
.box {
  width: 500px;
  height: 500px;
  background-color: skyblue;
  clip-path: url(#heart);
}
```

也可以直接使用 `path()` 函数：

```css
clip-path: path("M 300 350 C 100 150 200 50 300 150 C 500 0 400 300 300 350");
```

> **注意：** `url(#id)` 引用 SVG 中定义的路径，`path()` 直接内联 SVG path 语法，两者效果相同。

---

## 七、clip-path 与 transition/animation 结合

`clip-path` 的一个强大特性是可以与 CSS 过渡和动画结合，实现流畅的遮罩展开效果。

### 7.1 圆形展开遮罩（hover 过渡）

利用 `transition` 让 `circle()` 的半径从极小过渡到极大，实现遮罩展开效果。

```css
.box {
  position: relative;
  width: 600px;
  height: 400px;
  border: 10px dashed #fca;
}

/* 左上角蓝色遮罩 */
.box::before {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  clip-path: circle(30px at 0 0);       /* 初始：左上角小圆点 */
  background-color: skyblue;
  transition: all linear 2s;
}

/* 右下角紫色遮罩 */
.box::after {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  clip-path: circle(30px at bottom right); /* 初始：右下角小圆点 */
  background-color: #caf;
  transition: all linear 2s;
}

/* hover 时展开遮罩 */
.box:hover::before {
  clip-path: circle(500px at 0 0);       /* 展开到 500px */
}

.box:hover::after {
  clip-path: circle(500px at bottom right); /* 展开到 500px */
}
```

**效果：** 鼠标悬停时，左上角和右下角的遮罩从两个小圆点向各自方向圆形展开覆盖整个元素。

### 7.2 边框光线动画（animation 关键帧）

利用 `inset()` 配合 `@keyframes`，实现边框光线沿四周循环移动的效果。

```css
.box {
  position: relative;
  padding: 20px;
  width: 400px;
  text-align: center;
}

/* 用伪元素做边框光线 */
.box::after {
  content: "";
  position: absolute;
  left: -3px;
  top: -3px;
  right: -3px;
  bottom: -3px;
  border: 5px solid #cfa;
  clip-path: inset(0 96% 0 0);           /* 初始：左侧窄条 */
  animation: clip-inset 2s linear infinite, hue-rotate 2s linear infinite;
}

/* 色相旋转动画 */
@keyframes hue-rotate {
  to {
    filter: hue-rotate(360deg);
  }
}

/* 边框光线循环动画 */
@keyframes clip-inset {
  0%, 100% {
    clip-path: inset(0 96% 0 0);         /* 左侧 */
  }
  25% {
    clip-path: inset(0 0 96% 0);         /* 顶部 */
  }
  50% {
    clip-path: inset(0 0 0 96%);         /* 右侧 */
  }
  75% {
    clip-path: inset(96% 0 0 0);         /* 底部 */
  }
}
```

**效果：** 一条发光边框线沿着元素的四周顺时针循环移动，同时颜色不断变化（通过 `hue-rotate` 实现彩虹效果）。

---

## 八、实用技巧总结

1. **相同类型的 clip-path 才能做过渡动画**：`circle()` 到 `circle()` 可以过渡，`circle()` 到 `polygon()` 不能过渡
2. **百分比坐标**：使用百分比而非固定像素，可以实现响应式的裁剪效果
3. **伪元素 + clip-path**：用伪元素做遮罩层，不影响原始内容
4. **调试工具**：浏览器开发者工具中可以直接编辑 `clip-path` 的参数值，实时预览裁剪效果
5. **性能优势**：`clip-path` 由 GPU 加速，动画性能优于 `overflow: hidden` 等方案
6. **兼容性**：现代浏览器均已支持，如需兼容旧浏览器可添加 `-webkit-clip-path` 前缀
