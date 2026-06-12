# CSS Transform（变换）

利用 CSS 使元素完成几何变化，如 **平移、缩放、旋转、倾斜**。变换分为 **2D 变换** 和 **3D 变换**，属性差别不大，重点理解 3D 的相关原理。

---

## 一、变换坐标系

CSS Transform 使用笛卡尔坐标系，但与标准坐标系不同的是，**Y 轴方向从自下向上改为了自上向下**。

- 坐标原点 `(0, 0)` 相当于元素的中心，也是 **变换原点**（transform-origin）
- **注意：变换原点的不同会直接影响变换效果**，务必重视

---

## 二、2D 变换

### 2.1 平移 translate

| 函数 | 说明 |
|------|------|
| `translateX(x)` | 沿 X 轴方向移动 |
| `translateY(y)` | 沿 Y 轴方向移动 |
| `translate(x, y)` | 同时沿 X、Y 轴移动 |
| `translate(x)` | 等同于 `translate(x, 0)`，即 `translateX(x)` |

**常见用途 —— 元素居中：**

```css
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  /* 传统的 margin 居中需要知道元素尺寸 */
  /* margin-left: -100px; margin-top: -100px; */

  /* 使用 translate 居中，无需知道元素尺寸 */
  transform: translate(-50%, -50%);
}
```

> **技巧**：`translate` 使用百分比时，百分比参照的是 **元素自身的尺寸**，而非父元素尺寸。

### 2.2 缩放 scale

| 函数 | 说明 |
|------|------|
| `scaleX(sx)` | 沿 X 轴缩放 |
| `scaleY(sy)` | 沿 Y 轴缩放 |
| `scale(sx, sy)` | 同时沿 X、Y 轴缩放 |
| `scale(s)` | 等同于 `scale(s, s)`，等比例缩放 |

- 缩放倍率 `> 1` 放大，`< 1` 缩小，`= 1` 不变
- **负值有意义**：会在水平或垂直方向翻转后再缩放

```css
.box {
  transform: scale(1.5);   /* 放大 1.5 倍 */
}

.box:hover {
  transform: scale(0.8);   /* 鼠标悬停时缩小到 0.8 倍 */
}
```

### 2.3 旋转 rotate

```css
transform: rotate(角度);
```

- 正值：顺时针旋转
- 负值：逆时针旋转
- 单位：`deg`（度）、`rad`（弧度）、`turn`（圈）

```css
.box {
  transform: rotate(45deg);   /* 顺时针旋转 45 度 */
}
```

> **注意**：`rotate` 等同于 2D 平面内的旋转，围绕变换原点进行。

### 2.4 倾斜 skew

| 函数 | 说明 |
|------|------|
| `skewX(ax)` | 沿 X 轴方向倾斜 |
| `skewY(ay)` | 沿 Y 轴方向倾斜 |
| `skew(ax, ay)` | 同时沿 X、Y 轴倾斜 |
| `skew(ax)` | 等同于 `skew(ax, 0)`，即 `skewX(ax)` |

**数学原理：**

- `skewX(θ)`：`x' = x + y * tan(θ)`，`y' = y`
- `skewY(θ)`：`x' = x`，`y' = y + x * tan(θ)`

**直观理解：** 将元素想象成一张矩形纸片，固定底部在桌面上，将顶部推向左边或右边，形成一个斜角。

```css
.box {
  transform: skewX(20deg);   /* 沿 X 轴倾斜 20 度 */
}
```

### 2.5 变换原点 transform-origin

变换原点决定了变换的参考点，**默认是元素的中心点**（`50% 50%`）。

```css
transform-origin: x y;
```

**取值方式：**

| 关键字 | 对应值 |
|--------|--------|
| `left` | `0%` |
| `center` | `50%` |
| `right` | `100%` |
| `top` | `0%` |
| `bottom` | `100%` |

可以是数字（`px`）、百分比、关键字的任意组合：

```css
/* 左上角为变换原点 */
transform-origin: left top;
transform-origin: 0 0;
transform-origin: 0% 0%;

/* 自定义位置 */
transform-origin: 30px 50%;
```

**关键要点：**

- **每次变换时，变换原点位置不变**（除非自行设置）
- 多个变换函数组合时，坐标系会随变换 **重置**

### 2.6 多重变换

多个变换函数可以写在一起，**从左到右依次执行**：

```css
/* 先沿 X 轴平移 100px，再旋转 45deg */
.box {
  transform-origin: left top;
  transform: translateX(100px) rotate(45deg);
}
```

> **注意顺序**：`translateX(100px) rotate(45deg)` 和 `rotate(45deg) translateX(100px)` 的效果不同！因为第一个变换会改变坐标系，后续变换基于新的坐标系进行。

---

## 三、3D 变换

### 3.1 3D 平移 translateZ / translate3d

| 函数 | 说明 |
|------|------|
| `translateX(x)` | 沿 X 轴平移（2D） |
| `translateY(y)` | 沿 Y 轴平移（2D） |
| `translateZ(z)` | 沿 Z 轴平移（3D） |
| `translate3d(x, y, z)` | 三轴同时平移 |

**关键概念 —— 单独使用 `translateZ` 在没有透视的情况下无效。**

### 3.2 透视 perspective（景深）

透视是 3D 变换的核心，决定了 3D 效果的视觉强度。**透视距离 = 眼睛到屏幕的距离。**

默认情况下透视距离无限大，所以在 Z 轴方向的变化没有视觉效果：

```
infinity + 100 === infinity - 100  // 无法感知差异
```

**两种使用方式：**

```css
/* 方式一：设置在父级容器上（推荐，所有子元素共享同一透视点） */
.parent {
  perspective: 800px;
}
```

```css
/* 方式二：设置在当前元素上（作为 transform 的一个函数） */
.self {
  transform: perspective(800px) translateZ(100px);
}
```

> **两种方式的区别**：方式一（父级设置），所有子元素共享同一个透视消失点；方式二（元素自身），每个元素有各自独立的透视消失点。方式一更常用于实现一致的 3D 场景效果。

**perspective 值越小，3D 效果越夸张；值越大，3D 效果越平缓。**

### 3.3 透视原点 perspective-origin

控制观察者的视觉位置，取值方式与 `background-position` 相同：

```css
.parent {
  perspective: 800px;
  perspective-origin: center center;  /* 默认值，正中心 */
  perspective-origin: left top;       /* 左上角 */
  perspective-origin: 50% 50%;        /* 居中 */
}
```

### 3.4 3D 旋转 rotateX / rotateY / rotateZ / rotate3d

| 函数 | 说明 |
|------|------|
| `rotateX(deg)` | 绕 X 轴旋转（上下翻转） |
| `rotateY(deg)` | 绕 Y 轴旋转（左右翻转） |
| `rotateZ(deg)` | 绕 Z 轴旋转（平面旋转，等同于 `rotate`） |
| `rotate3d(x, y, z, deg)` | 绕自定义向量轴旋转 |

**rotateX 示例：**

```css
.box {
  transform: rotateX(45deg);  /* 绕 X 轴旋转 45 度，元素看起来向上翻转 */
}
```

**rotate3d 用法：**

参数 `x, y, z` 定义旋转向量的方向，`deg` 为旋转角度：

```css
/* 绕 (1, 1, 1) 向量旋转 45 度 */
.element {
  transform: rotate3d(1, 1, 1, 45deg);
}
```

> `rotate(xxx deg)` 等同于 `rotateZ(xxx deg)`。

### 3.5 3D 缩放 scaleZ

```css
transform: scaleZ(s);
```

`scaleZ` 是一个 **不常用** 的变换函数，因为在二维屏幕上无法直接观察 Z 轴方向的缩放效果。元素在三维空间中的深度感通常通过 `translateZ` 或 `perspective` 来模拟。

### 3.6 变换风格 transform-style

`transform-style` 决定子元素是位于三维空间中还是被扁平化为二维平面。

| 值 | 说明 |
|------|------|
| `flat`（默认） | 子元素被压缩在父元素的二维平面中 |
| `preserve-3d` | 子元素保留在三维空间中，符合真实世界的 3D 表现 |

```css
.container {
  transform-style: preserve-3d;
}
```

> **使用场景**：构建真正的 3D 结构（如正方体、3D 轮播）时，父元素必须设置 `transform-style: preserve-3d`，否则子元素的 Z 轴变换不会产生 3D 层叠效果。

### 3.7 背面可见性 backface-visibility

控制元素旋转到背面时是否可见：

```css
.element {
  backface-visibility: hidden;   /* 背面不可见（默认 visible） */
}
```

> **常见用途**：制作卡片翻转效果时，正反面分别设置 `backface-visibility: hidden`，确保旋转时只显示朝向用户的那一面。

---

## 四、正方体案例

通过 3D 变换组合实现一个 CSS 正方体，是理解 `transform-style: preserve-3d` 和三维空间定位的经典案例。

### 4.1 结构分析

正方体有 6 个面：前（front）、后（back）、左（left）、右（right）、上（top）、下（bottom）。每个面都是 200x200 的正方形，正方体的边长（即半边长）为 200px。

### 4.2 关键 CSS

**外层容器：设置透视 + 3D 变换风格**

```css
.wrapper {
  perspective: 800px;
}

.content {
  transform-style: preserve-3d;
  width: 400px;
  height: 400px;
  position: relative;
  transform: rotateY(45deg) rotateX(0deg);
}
```

**六个面的定位思路：**

每个面先旋转到对应方向，再沿 Z 轴推出半个边长的距离（200px）：

```css
/* 前面：直接向 Z 轴正方向推出 200px */
.front {
  transform: translateZ(200px);
}

/* 后面：先绕 Y 轴旋转 180 度，再推出 200px */
.back {
  transform: rotateY(180deg) translateZ(200px);
}

/* 左面：先绕 Y 轴旋转 90 度，再推出 200px */
.left {
  transform: rotateY(90deg) translateZ(200px);
}

/* 右面：先绕 Y 轴旋转 -90 度，再推出 200px */
.right {
  transform: rotateY(-90deg) translateZ(200px);
}

/* 上面：先绕 X 轴旋转 90 度，再推出 200px */
.top {
  transform: rotateX(90deg) translateZ(200px);
}

/* 下面：先绕 X 轴旋转 -90 度，再推出 200px */
.bottom {
  transform: rotateX(-90deg) translateZ(200px);
}
```

**每个面都是绝对定位的子元素：**

```css
.content li {
  width: 400px;
  height: 400px;
  position: absolute;
}
```

### 4.3 构建思路总结

构建 3D 物体时的通用模式：

1. **外层父级**设置 `perspective` 提供透视效果
2. **直接父级**设置 `transform-style: preserve-3d` 保持 3D 空间
3. **每个面**使用 `position: absolute` 叠放
4. **面的定位**：先 `rotate` 旋转到目标朝向，再 `translateZ` 推出到正确距离
5. 通过调整父元素的 `transform` 旋转角度来预览不同视角

> **核心公式**：`transform: rotate[Axis](angle) translateZ(halfSize)`
> 先旋转改变面的朝向，再沿新的 Z 轴方向推出距离，即可精确定位每个面。

---

## 五、实用技巧

### 5.1 transform 不影响文档流

`transform` 变换不会影响元素在文档流中的位置，也不会影响其他元素的布局，类似于 `relative` 定位的视觉效果。

### 5.2 性能优化

`transform` 和 `opacity` 是 CSS 中性能最好的动画属性，因为它们可以直接由 GPU 合成层处理，不会触发回流（reflow）和重绘（repaint）。

```css
/* 推荐：使用 transform 做动画 */
.box {
  transition: transform 0.3s ease;
}
.box:hover {
  transform: scale(1.1);
}

/* 不推荐：直接修改 width/height 做动画（触发回流） */
```

### 5.3 3D 变换三板斧

构建 3D 场景时的三个必备属性：

| 属性 | 设置位置 | 作用 |
|------|----------|------|
| `perspective` | 父级容器 | 提供透视效果，产生近大远小的视觉 |
| `transform-style: preserve-3d` | 直接父级 | 保持子元素的 3D 空间关系 |
| `backface-visibility: hidden` | 子元素 | 控制背面是否可见 |
