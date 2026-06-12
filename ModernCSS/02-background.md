# 02. CSS Background（背景属性）

CSS 背景属性用于为元素设置背景样式，包括背景颜色、背景图片及其重复方式、定位、尺寸、裁剪和附着行为。掌握这些属性是实现丰富视觉效果的基础。

---

## background-color（背景颜色）

设置元素的背景颜色。

```css
.box {
  background-color: #f00;
}
```

> **提示**：背景颜色绘制在背景图片之下，当背景图片不可用或透明时，背景颜色会显示出来。

---

## background-image（背景图片）

为元素引入一个或多个背景图片，使用 `url()` 指定图片地址。多张图片用逗号分隔。

```css
/* 单张背景图 */
.box {
  background-image: url(./assets/chai.webp);
}

/* 多张背景图 */
.box {
  background-image: url(./assets/chai.webp), url(./assets/chai2.webp);
}
```

> **提示**：多张背景图时，排在前面的图片层级越高（显示在上面）。

---

## background-repeat（背景重复）

控制背景图片的重复方式。默认情况下，背景图会在水平和垂直方向上重复铺满整个元素。

### 可选值

| 值 | 说明 |
| --- | --- |
| `repeat` | **默认值**，默认大小重复，超出部分会被裁剪 |
| `no-repeat` | 不重复，只显示一次 |
| `repeat-x` | 仅水平方向重复 |
| `repeat-y` | 仅垂直方向重复 |
| `round` | 图像会被缩放以完整地重复铺满，不会被裁剪 |
| `space` | 图像以原始大小尽可能多地重复，多余空间均匀分配为间距，不会裁剪 |

```css
.box {
  background-image: url(./assets/chai.webp);
  background-repeat: space;
}
```

### 双值语法

`background-repeat` 支持分别设置水平和垂直方向的重复方式：

| 单值 | 等价双值 |
| --- | --- |
| `repeat-x` | `repeat no-repeat` |
| `repeat-y` | `no-repeat repeat` |
| `repeat` | `repeat repeat` |
| `space` | `space space` |
| `round` | `round round` |
| `no-repeat` | `no-repeat no-repeat` |

```css
/* 水平重复，垂直不重复 */
.box {
  background-repeat: repeat no-repeat;
}
```

> **提示**：`background-repeat` 也可拆分为 `background-repeat-x` 和 `background-repeat-y` 两个独立属性。

---

## background-position（背景定位）

设置背景图片的初始位置。常用于雪碧图（CSS Sprite）技术中精确定位背景图。

### 语法

#### 1 个值

| 单值 | 等价双值 |
| --- | --- |
| `20px` | `20px center` |
| `20%` | `20% center` |
| `top` | `top center` |
| `right` | `right center` |
| `bottom` | `bottom center` |
| `left` | `left center` |
| `center` | `center center` |

#### 2 个值

格式为：`水平偏移量 垂直偏移量`

```css
.box {
  background-position: 20px 30px;    /* 水平偏移20px，垂直偏移30px */
}
```

> **注意**：不能同时使用两个同方向的值，如 `20px left` 是无效的（两个都是水平方向）。

#### 3 个值与 4 个值

格式为：`方向 偏移量 方向 偏移量`

偏移量省略时默认为 `0`。

```css
background-position: left 10px top 15px;   /* 10px, 15px */
background-position: left      top     ;   /*  0px,  0px */
background-position:      10px     15px;   /* 10px, 15px */
background-position: left          15px;   /*  0px, 15px */
background-position:      10px top     ;   /* 10px,  0px */
background-position: left      top 15px;   /*  0px, 15px */
background-position: left 10px top     ;   /* 10px,  0px */
```

```css
/* 实际使用示例 */
.box {
  background-image: url(./assets/chai.webp);
  background-repeat: no-repeat;
  background-position: right 10px top;
}
```

### 百分比计算规则

百分比定位是基于**剩余空间**计算的：

```
(容器宽度 - 图像宽度) * x% = 水平偏移量
(容器高度 - 图像高度) * y% = 垂直偏移量
```

> **提示**：当 `background-position` 使用百分比值时，`0% 0%` 表示左上角，`100% 100%` 表示右下角，`50% 50%` 等价于 `center center`。

---

## background-origin（背景定位参照原点）

设置 `background-position` 定位的参考原点，决定了背景图片从哪个区域开始定位。

### 可选值

| 值 | 说明 |
| --- | --- |
| `padding-box` | **默认值**，背景定位以内边距区域为参考 |
| `border-box` | 背景定位以边框区域为参考 |
| `content-box` | 背景定位以内容区域为参考 |

```css
.box {
  background-image: url(./assets/chai.webp);
  background-repeat: no-repeat;
  background-position: right 10px top;
  background-origin: content-box;
}
```

> **提示**：`background-origin` 影响的是定位参考点，与背景绘制区域（`background-clip`）是不同的概念。

---

## background-size（背景尺寸）

设置背景图片的大小。默认值为 `auto auto`，即展示图片的原始尺寸。

### 语法

```css
/* 指定宽高 */
background-size: 200px 100px;

/* 指定宽度，高度自动按比例计算 */
background-size: 200px auto;

/* 指定高度，宽度自动按比例计算 */
background-size: auto 100px;
```

### 关键字

| 值 | 说明 |
| --- | --- |
| `cover` | **覆盖** -- 图片等比缩放，完全覆盖元素，不留空白，图片可能被裁剪 |
| `contain` | **包含** -- 图片等比缩放，完整显示在元素内，可能出现留白 |

```css
.box {
  background-image: url(./assets/big-chai.jpeg);
  background-repeat: no-repeat;
  background-size: cover;
}
```

> **提示**：在实际开发中，`cover` 使用最频繁，适合用于铺满整个容器的场景（如 banner 背景）。`contain` 则常用于需要完整展示图片内容的场景。

---

## background-clip（背景裁剪）

设置元素的背景（颜色或图片）的绘制（展示）区域。

### 可选值

| 值 | 说明 |
| --- | --- |
| `border-box` | **默认值**，背景延伸至边框外边缘 |
| `padding-box` | 背景延伸至内边距外边缘（不包含边框） |
| `content-box` | 背景仅绘制在内容区域 |
| `text` | 背景被裁剪为文字的前景色（实现文字渐变等效果） |

```css
/* 基本用法 */
.box {
  background-clip: content-box;
}
```

### text 值 -- 文字背景裁剪

将背景裁剪到文字形状，配合 `color: transparent` 实现文字背景/渐变效果：

```css
.box {
  background-image: url(./assets/cxk.gif);
  background-origin: border-box;
  color: transparent;
  -webkit-background-clip: text;
}
```

> **提示**：`text` 值目前需要加 `-webkit-` 前缀（`-webkit-background-clip: text`）以保证浏览器兼容性。使用时务必将文字颜色设为 `transparent`，否则背景会被文字颜色遮挡。

---

## background-attachment（背景附着）

决定背景图像的位置是在视口内固定，还是随着包含它的区块滚动。

### 可选值

| 值 | 说明 |
| --- | --- |
| `scroll` | **默认值**，背景相对于元素本身固定，元素内容滚动时背景不动 |
| `fixed` | 背景相对于视口固定，即使页面滚动背景也不会移动 |
| `local` | 背景随元素内容一起滚动 |

```css
.bg {
  background-image: url(./assets/attachment1.png);
  background-size: 630px;
  background-position: top center;
  background-repeat: no-repeat;
  background-attachment: fixed;
}
```

> **提示**：`fixed` 常用于实现视差滚动效果（Parallax Scrolling），让背景图在滚动时保持固定，营造层次感。

---

## background 复合写法

可以将所有背景相关属性合并为一行简写，语法格式如下：

```css
background: [background-color] [background-image] [background-position] / [background-size] [background-repeat] [background-attachment] [background-origin] [background-clip];
```

### 示例

```css
/* 简写示例 */
.box {
  background: #f00 url(./assets/chai.webp) center / cover no-repeat fixed content-box;
}
```

```css
/* 等价于以下完整写法 */
.box {
  background-color: #f00;
  background-image: url(./assets/chai.webp);
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-origin: content-box;
  background-clip: content-box;
}
```

### 注意事项

1. **`background-size` 必须紧跟在 `background-position` 后面，用 `/` 分隔**，否则会被当作 `background-position` 的第二个值。
2. 如果同时指定 `background-origin` 和 `background-clip`，可以用 `box` 值的形式：第一个值为 `origin`，第二个值为 `clip`。如果只写一个值，则 `origin` 和 `clip` 都使用该值。
3. 简写中省略的属性会**重置为默认值**，因此要注意简写的顺序，避免覆盖之前设置的值。

```css
/* origin 和 clip 分别指定 */
background: url(bg.png) center / cover no-repeat padding-box content-box;
/* origin = padding-box, clip = content-box */

/* 只写一个值，origin 和 clip 相同 */
background: url(bg.png) center / cover no-repeat content-box;
/* origin = content-box, clip = content-box */
```

---

## 属性总览对照表

| 属性 | 默认值 | 作用 |
| --- | --- | --- |
| `background-color` | `transparent` | 设置背景颜色 |
| `background-image` | `none` | 设置背景图片（可多张） |
| `background-repeat` | `repeat` | 设置背景图片重复方式 |
| `background-position` | `0% 0%` | 设置背景图片初始位置 |
| `background-size` | `auto auto` | 设置背景图片尺寸 |
| `background-origin` | `padding-box` | 设置定位参考原点 |
| `background-clip` | `border-box` | 设置背景绘制区域 |
| `background-attachment` | `scroll` | 设置背景滚动行为 |
