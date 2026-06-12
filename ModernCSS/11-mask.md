# 11. CSS Mask（CSS 遮罩）

CSS 遮罩（Mask）可以让一个元素按照某张图像的轮廓显示。简单来说，就是把元素的部分内容遮盖起来，按照给定的形状去显示可见区域。

遮罩的核心原理：**遮罩图像中不透明（白色）的部分，对应的元素内容可见；透明部分，对应的元素内容不可见。**

## mask-image（遮罩图片）

`mask-image` 用于定义一个元素的蒙版图像，将元素的可见部分限制在指定的形状或图案中。

**支持的值类型：**

- `url()` — 使用图片作为遮罩
- `linear-gradient()` — 使用线性渐变作为遮罩
- `radial-gradient()` — 使用径向渐变作为遮罩
- `none` — 无遮罩（默认值）

**示例：使用图片作为遮罩**

```css
img {
  mask-image: url(./assets/bird.png);
}
```

**示例：使用渐变作为遮罩**

```css
img {
  mask-image: linear-gradient(to right, #fff, transparent);
}
```

**示例：使用多个遮罩**

可以同时使用多个遮罩图像，用逗号分隔：

```css
img {
  mask-image: linear-gradient(#fff 50px, #fff 100px),
    linear-gradient(#fff 50px, #fff 100px);
}
```

> **提示：** 多个遮罩图像常配合 `mask-composite` 使用，以实现复杂的遮罩叠加效果。

## mask-size（遮罩大小）

`mask-size` 用于设置遮罩图像的大小，语法和行为与 `background-size` 一致。

**常用值：**

- `cover` — 等比缩放，完全覆盖遮罩区域
- `contain` — 等比缩放，完整显示遮罩图像
- 具体长度值，如 `150px`、`200px 200px`
- 百分比值，如 `100% 100%`

```css
img {
  mask-size: 150px;
}

/* 多遮罩时分别设置大小 */
img {
  mask-size: 200px 200px;
}
```

> **提示：** 当使用图片作为遮罩时，需注意遮罩大小与元素大小的关系，避免遮罩过小导致重复平铺。

## mask-position（遮罩位置）

`mask-position` 用于设置遮罩图像的位置，语法和行为与 `background-position` 一致。

**常用值：**

- 关键字：`top`、`bottom`、`left`、`right`、`center`
- 长度值：`10px 20px`
- 百分比值：`50% 50%`

```css
img {
  mask-position: right center;
}

/* 多遮罩时分别设置位置 */
img {
  mask-position: 0 0, 150px 150px;
}
```

> **提示：** 多遮罩时，用逗号分隔每个遮罩的位置，顺序与 `mask-image` 中的顺序对应。

## mask-repeat（遮罩重复）

`mask-repeat` 用于控制遮罩区域是否重复平铺。

**可选值：**

| 值 | 说明 |
|---|---|
| `repeat` | 默认值，水平和垂直方向均平铺 |
| `repeat-x` | 仅水平方向平铺 |
| `repeat-y` | 仅垂直方向平铺 |
| `no-repeat` | 不平铺，只显示一个遮罩图形（位于左上角） |
| `space` | 尽可能平铺，不剪裁，图片间均匀留白 |
| `round` | 尽可能靠在一起，无间隙，不剪裁，可能产生缩放 |

```css
img {
  mask-repeat: no-repeat;
}
```

> **提示：** 使用渐变遮罩时，通常需要设置 `mask-repeat: no-repeat`，否则渐变图案会重复。

## mask-composite（遮罩合成）

`mask-composite` 用于控制多个遮罩图像之间的合成方式。这是实现复杂遮罩效果的关键属性。

**可选值：**

| 值 | 说明 |
|---|---|
| `add` | 遮罩累加（默认值），多个遮罩区域合并显示 |
| `subtract` | 遮罩相减，重合区域不显示；遮罩越多，可见区域越小 |
| `intersect` | 遮罩相交，仅重合区域显示遮罩 |
| `exclude` | 遮罩排除，重合区域变为透明（类似异或效果） |

```css
img {
  mask-image: linear-gradient(#fff 50px, #fff 100px),
    linear-gradient(#fff 50px, #fff 100px);
  mask-size: 200px 200px;
  mask-position: 0 0, 150px 150px;
  mask-composite: exclude;
}
```

上述代码通过两个渐变遮罩配合 `exclude` 合成模式，实现了"两个遮罩重叠部分被挖空"的效果。

> **提示：** `exclude` 常用于制作镂空效果，`subtract` 用于从大遮罩中"扣除"某些区域。

## mask 复合写法

可以将多个 mask 属性合并为一行简写，类似 `background` 的简写方式：

```css
img {
  mask: url(./assets/bird.png) no-repeat center / 150px;
}
```

简写格式为：

```
mask: [mask-image] [mask-repeat] [mask-position] / [mask-size];
```

**多遮罩复合写法示例：**

```css
img {
  mask:
    linear-gradient(#fff 50px, #fff 100px) no-repeat 0 0 / 200px 200px,
    linear-gradient(#fff 50px, #fff 100px) no-repeat 150px 150px / 200px 200px;
  mask-composite: exclude;
}
```

> **提示：** 复合写法中，`mask-size` 必须紧跟在 `mask-position` 后面，用 `/` 分隔。

## 案例：手电筒效果

手电筒效果是 CSS Mask 的经典应用场景——利用 `radial-gradient` 创建一个跟随鼠标移动的圆形可视区域，模拟手电筒照射效果。

### 核心思路

1. 使用 `radial-gradient` 作为遮罩图像，创建一个圆形渐变（白色到透明）
2. 通过 CSS 自定义属性（`--x`、`--y`）控制渐变圆心位置
3. 用 JavaScript 监听 `mousemove` 事件，实时更新自定义属性的值

### 关键代码

**CSS 部分：**

```css
.content {
  width: 1136px;
  height: 642px;
  margin: 0 auto;
  background-color: #000;
}

img.mask {
  --x: -150px;
  --y: -150px;
  mask-image: radial-gradient(
    circle at var(--x) var(--y),
    #fff 70px,
    transparent 100px
  );
}
```

- `--x` 和 `--y` 是自定义属性，初始值设为 `-150px`（移出可视区域）
- `radial-gradient` 创建一个以 `(--x, --y)` 为圆心的径向渐变
- `#fff 70px` 表示半径 70px 内完全可见，`transparent 100px` 表示 70px~100px 之间渐变为透明

**JavaScript 部分：**

```javascript
const mask = document.querySelector('.mask');
const content = document.querySelector('.content');

content.onmousemove = function (e) {
  const { offsetX, offsetY } = e;
  mask.style.setProperty('--x', offsetX + 'px');
  mask.style.setProperty('--y', offsetY + 'px');
};

content.onmouseleave = function () {
  mask.style.setProperty('--x', -150 + 'px');
  mask.style.setProperty('--y', -150 + 'px');
};
```

- `offsetX` / `offsetY` 获取鼠标相对于容器的坐标
- `setProperty` 实时更新 CSS 自定义属性，驱动遮罩位置变化
- 鼠标离开时，将圆心移回 `-150px`，隐藏"手电筒"光斑

### 效果说明

- 鼠标移入图片区域时，会出现一个圆形的"可见窗口"，跟随鼠标移动
- 圆形窗口外的内容被遮罩隐藏（变透明），形成手电筒照射的视觉效果
- 窗口边缘有 30px（100px - 70px）的柔和渐变过渡

> **提示：** 调整 `#fff 70px` 中的 `70px` 可以改变手电筒光圈的大小，调整 `transparent 100px` 中的 `100px` 可以改变边缘柔和程度。将两个值设为相同可以实现硬边缘效果。

## 其他 mask 相关属性补充

### mask-clip（遮罩裁剪区域）

确定遮罩在元素上的显示范围：

| 值 | 说明 |
|---|---|
| `border-box` | 遮罩裁剪到边框盒（默认值） |
| `padding-box` | 遮罩裁剪到内边距盒 |
| `content-box` | 遮罩裁剪到内容盒 |
| `no-clip` | 遮罩不裁剪，超出元素的部分也显示 |
| `fill-box` | 遮罩区域为图形填充区域的边界盒子 |
| `stroke-box` | 遮罩区域包含描边占据的区域 |
| `view-box` | 使用最近的 SVG 视口作为参考盒子 |

### mask-origin（遮罩原点位置）

设置遮罩的原点位置，取值同 `mask-clip`，决定 `mask-position` 计算的参考起点。

### mask-mode（遮罩模式）

- `match-source`（默认值）：根据资源类型自动选择遮罩模式
  - SVG `<mask>` 元素：使用 `luminance`（亮度模式）
  - 其他场景：使用 `alpha`（透明度模式）
- `luminance`：基于图像亮度判断遮罩区域
- `alpha`：基于图像透明度判断遮罩区域

## 总结

| 属性 | 作用 | 类比 |
|---|---|---|
| `mask-image` | 定义遮罩图像 | 类似 `background-image` |
| `mask-size` | 遮罩大小 | 类似 `background-size` |
| `mask-position` | 遮罩位置 | 类似 `background-position` |
| `mask-repeat` | 遮罩是否重复 | 类似 `background-repeat` |
| `mask-composite` | 多遮罩合成方式 | 无对应 background 属性 |
| `mask-clip` | 遮罩裁剪范围 | 类似 `background-clip` |
| `mask-origin` | 遮罩原点位置 | 类似 `background-origin` |
| `mask-mode` | 遮罩模式（亮度/透明度） | 无对应 background 属性 |

> CSS Mask 的各个属性与 `background` 系列属性高度相似，掌握了 `background` 就能快速上手 `mask`。
