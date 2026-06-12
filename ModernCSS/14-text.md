# 14. Text — CSS 文本相关属性

本节涵盖文字阴影、描边、渐变填充、文字强调、书写方向等现代 CSS 文本属性。

---

## 一、text-shadow（文字阴影）

### 1.1 基本语法

```css
text-shadow: 水平偏移 垂直偏移 模糊半径 阴影颜色;
```

| 参数 | 说明 |
|---|---|
| 水平偏移 | 正值向右，负值向左 |
| 垂直偏移 | 正值向下，负值向上 |
| 模糊半径 | 值越大阴影越模糊（不能为负值） |
| 阴影颜色 | 支持 `rgba()` 实现半透明效果 |

> **与 box-shadow 的区别：** `text-shadow` 不支持 `inset`（内阴影）关键字，也不支持扩展半径（第四个数字参数），仅有三个数值参数。

### 1.2 多重阴影

`text-shadow` 支持用逗号分隔的多组阴影值，叠加产生丰富的视觉效果：

```css
/* 多重 3D 立体阴影 */
text-shadow:
  1px -1px 0 #767676,
  -1px 2px 1px #737272,
  -2px 4px 1px #767474,
  -3px 6px 1px #787777,
  -4px 8px 1px #7b7a7a,
  -5px 10px 1px #7f7d7d;
```

> **Tips:** 多层阴影的偏移量递增（如 1px, 2px, 3px...），颜色逐渐变浅，可以模拟 3D 立体效果。

### 1.3 经典文字阴影效果

#### 优雅立体阴影（Elegant Shadow）

浅色背景 + 逐层加深的多重阴影，产生从暗到亮的立体渐变：

```css
.elegantshadow {
  color: #131313;
  background-color: #e7e5e4;
  letter-spacing: 0.15em;
  text-shadow:
    1px -1px 0 #767676,
    -1px 2px 1px #737272,
    -2px 4px 1px #767474,
    -3px 6px 1px #787777,
    -4px 8px 1px #7b7a7a,
    -5px 10px 1px #7f7d7d,
    -6px 12px 1px #828181,
    -7px 14px 1px #868585,
    -8px 16px 1px #8b8a89,
    -9px 18px 1px #8f8e8d,
    -10px 20px 1px #949392;
}
```

#### 深度阴影（Deep Shadow）

暗色背景 + 逐层加深的阴影 + 末端柔和投影，产生深邃的雕刻效果：

```css
.deepshadow {
  color: #e0dfdc;
  background-color: #333;
  letter-spacing: 0.1em;
  text-shadow:
    0 -1px 0 #fff,
    0 1px 0 #2e2e2e,
    0 2px 0 #2c2c2c,
    0 3px 0 #2a2a2a,
    0 4px 0 #282828,
    0 5px 0 #262626,
    0 22px 30px rgba(0, 0, 0, 0.9);
}
```

> **Tips:** 最后一层使用较大的模糊半径（`30px`）和半透明黑色，模拟环境投影。

#### 内凹阴影（Inset Shadow）

模拟 `inset` 效果——通过一层暗色阴影 + 一层亮色阴影的对比，营造凹陷感：

```css
.insetshadow {
  color: #202020;
  background-color: #2d2d2d;
  letter-spacing: 0.1em;
  text-shadow:
    -1px -1px 1px #111,
    2px 2px 1px #363636;
}
```

#### 复古阴影（Retro Shadow）

文字颜色与背景色相同的阴影 + 偏移的半透明阴影，产生复古印刷效果：

```css
.retroshadow {
  color: #2c2c2c;
  background-color: #d5d5d5;
  letter-spacing: 0.05em;
  text-shadow:
    4px 4px 0px #d5d5d5,
    7px 7px 0px rgba(0, 0, 0, 0.2);
}
```

> **Tips:** 第一层阴影颜色与背景相同，可以"遮挡"文字与第二层阴影之间的部分，形成错位视觉。

### 1.4 利用 text-shadow 实现文字描边

通过在文字四周均匀添加无模糊的阴影，可以模拟描边效果：

```css
.shadow {
  -webkit-text-fill-color: #fff;
  text-shadow:
    -5px 0, 5px 0, 0 -5px, 0 5px,
    -5px -5px, 5px 5px, -5px 5px, 5px -5px;
}
```

> **局限：** 用 `text-shadow` 实现描边在细节处会有锯齿，不推荐在生产环境使用。更优方案见下文 `-webkit-text-stroke`。

---

## 二、-webkit-text-stroke（文字描边）

### 2.1 基本语法

```css
-webkit-text-stroke: 描边宽度 描边颜色;
```

```css
.stroke {
  -webkit-text-stroke: 10px skyblue;
}
```

### 2.2 注意事项

- **居中描边：** `text-stroke` 的描边是沿字形轮廓**居中**绘制的，即一半在文字内部、一半在文字外部，因此会影响视觉字重（文字看起来变细）。
- **兼容性：** 带有 `-webkit-` 前缀，但所有主流浏览器均已支持。
- **与 color 的关系：** 描边不会替代文字填充色，需要配合 `color` 或 `-webkit-text-fill-color` 使用。

### 2.3 解决字重变细问题

利用伪元素叠加一层无描边的文字，覆盖在描边文字之上：

```css
.stroke {
  -webkit-text-stroke: 10px skyblue;
}

.weight::before {
  content: attr(data-text);
  position: absolute;
  -webkit-text-stroke: 0;
}
```

```html
<h1 class="stroke weight" data-text="文字阴影">文字阴影</h1>
```

> **原理：** 伪元素 `::before` 通过 `attr(data-text)` 获取相同文本，叠加在原文字之上，`-webkit-text-stroke: 0` 确保伪元素不描边，从而保留原始字重。

---

## 三、-webkit-text-fill-color（文字填充色）

### 3.1 基本语法

```css
-webkit-text-fill-color: 颜色值;
```

```css
h1 {
  color: red;                      /* color 被覆盖 */
  -webkit-text-fill-color: skyblue; /* 实际生效的颜色 */
}
```

### 3.2 与 color 的关系

| 属性 | 说明 |
|---|---|
| `color` | 标准文字颜色属性，可被子元素继承 |
| `-webkit-text-fill-color` | 优先级**高于** `color`，会覆盖其效果 |

> **使用技巧：** 保留 `color` 的继承特性（让子元素仍能继承），同时用 `-webkit-text-fill-color` 覆盖当前元素的文字颜色。这在配合 `text-shadow` 做描边或配合 `background-clip: text` 做渐变时特别有用。

---

## 四、background-clip: text（文字渐变）

### 4.1 基本原理

将背景裁剪到文字区域，配合渐变背景实现文字渐变效果：

```css
.gradient-text {
  background: linear-gradient(to right, #ff6b6b, #5b86e5);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

> **关键步骤：** (1) 设置渐变背景 → (2) `background-clip: text` 裁剪到文字 → (3) `text-fill-color: transparent` 让文字本身透明，露出背景渐变。

### 4.2 完整示例

```css
h1 {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

> **兼容性：** 标准属性为 `background-clip: text`（无前缀），但部分浏览器仍需 `-webkit-background-clip: text`。建议同时写两行以确保兼容。

---

## 五、text-decoration（文字装饰）

### 5.1 基本语法

```css
text-decoration: line style color thickness;
```

| 子属性 | 说明 | 常用值 |
|---|---|---|
| `text-decoration-line` | 装饰线类型 | `underline`、`overline`、`line-through`、`none` |
| `text-decoration-style` | 装饰线样式 | `solid`、`dashed`、`dotted`、`wavy`、`double` |
| `text-decoration-color` | 装饰线颜色 | 任意颜色值 |
| `text-decoration-thickness` | 装饰线粗细 | `auto`、`from-font`、具体长度值 |

### 5.2 示例

```css
/* 波浪下划线 */
a {
  text-decoration: underline wavy red 2px;
}

/* 仅下划线 */
.underline {
  text-decoration-line: underline;
  text-decoration-style: dotted;
  text-decoration-color: #999;
  text-decoration-thickness: 1px;
}
```

> **Tips:** `text-decoration` 与 `border-bottom` 的区别——前者跟随文本自动换行且不占布局空间，后者作为盒子模型的一部分参与布局。

---

## 六、text-emphasis（文字强调）

### 6.1 基本语法

```css
text-emphasis: 颜色 样式;
```

| 值 | 说明 |
|---|---|
| `dot` | 圆点标记 |
| `circle` | 空心圆标记 |
| `double-circle` | 双圆标记 |
| `triangle` | 三角形标记 |
| `sesame` | 芝麻点标记 |
| 自定义字符串 | 使用任意字符作为标记 |

### 6.2 示例

```css
span {
  text-emphasis: red dot;
  text-emphasis-position: under right;
}
```

### 6.3 text-emphasis-position

控制强调标记的位置：

```css
text-emphasis-position: over right;   /* 默认：右侧上方 */
text-emphasis-position: under right;  /* 右侧下方 */
text-emphasis-position: over left;    /* 左侧上方 */
```

> **Tips:** 横排文本默认在文字上方，竖排文本默认在文字右侧。`text-emphasis` 是简写属性，可拆分为 `text-emphasis-style` 和 `text-emphasis-color`。

---

## 七、writing-mode（书写模式）

### 7.1 基本语法

控制文本的书写方向（横排 / 竖排）：

```css
writing-mode: horizontal-tb;  /* 默认：水平，从上到下 */
writing-mode: vertical-rl;    /* 竖排，从右到左 */
writing-mode: vertical-lr;    /* 竖排，从左到右 */
```

| 值 | 说明 |
|---|---|
| `horizontal-tb` | 水平排列，行从上到下（默认） |
| `vertical-rl` | 垂直排列，行从右到左（传统中文竖排） |
| `vertical-lr` | 垂直排列，行从左到右 |

### 7.2 配合 text-orientation 使用

`text-orientation` 控制竖排模式下的文字朝向：

```css
.vertical-text {
  writing-mode: vertical-rl;
  text-orientation: mixed;
}
```

| 值 | 说明 |
|---|---|
| `mixed` | 默认值。CJK 字符自然竖排，拉丁字母顺时针旋转 90° |
| `upright` | 所有字符自然竖排（不旋转），拉丁字母也保持正立 |
| `sideways` | 所有字符整体旋转 90°，等同于横排文本旋转 |

---

## 八、word-break / word-wrap / overflow-wrap（断词与换行）

### 8.1 word-break

控制单词内部的断行规则：

```css
word-break: normal;       /* 默认：使用浏览器默认断词规则 */
word-break: break-all;    /* 允许在任意字符间断行（含英文单词中间） */
word-break: keep-all;     /* CJK 文本不在字符间断行，只在空格/连字符处断行 */
word-break: break-word;   /* 废弃值，建议用 overflow-wrap 替代 */
```

### 8.2 overflow-wrap（原 word-wrap）

控制是否允许在长单词内部断行：

```css
overflow-wrap: normal;     /* 默认：仅在允许的断点处换行 */
overflow-wrap: break-word; /* 当行内没有其他断点时，允许在任意位置断行 */
```

> **区别：** `word-break: break-all` 无条件在任何位置断行；`overflow-wrap: break-word` 只有在行内没有其他可用断点时才会在单词中间断行，优先尝试其他换行方式。

---

## 九、其他文字相关 CSS 属性

### 9.1 text-transform（文字大小写转换）

```css
text-transform: uppercase;  /* 全部大写 */
text-transform: lowercase;  /* 全部小写 */
text-transform: capitalize; /* 每个单词首字母大写 */
text-transform: none;       /* 不转换（默认） */
```

### 9.2 letter-spacing（字间距）

```css
letter-spacing: 0.1em;   /* 增加字间距 */
letter-spacing: -0.05em; /* 缩小字间距 */
letter-spacing: normal;  /* 默认 */
```

### 9.3 line-height（行高）

```css
line-height: 1.5;      /* 无单位数值，相对于 font-size 的倍数 */
line-height: 24px;     /* 固定行高 */
line-height: 1.5em;    /* 相对于当前元素 font-size */
```

> **Tips:** 推荐使用无单位数值（如 `1.5`），它会根据子元素自身的 `font-size` 动态计算，避免继承问题。

### 9.4 white-space（空白处理）

```css
white-space: normal;   /* 合并空白，正常换行（默认） */
white-space: nowrap;   /* 合并空白，不换行 */
white-space: pre;      /* 保留空白，不换行 */
white-space: pre-wrap; /* 保留空白，正常换行 */
white-space: pre-line; /* 合并空白符为空格，保留换行符 */
```

### 9.5 text-align（文本对齐）

```css
text-align: left;      /* 左对齐（默认） */
text-align: center;    /* 居中对齐 */
text-align: right;     /* 右对齐 */
text-align: justify;   /* 两端对齐 */
```

### 9.6 text-indent（首行缩进）

```css
text-indent: 2em;  /* 首行缩进两个字符宽度 */
```

---

## 总结

| 属性 | 核心用途 | 关键点 |
|---|---|---|
| `text-shadow` | 文字阴影 / 3D效果 | 多层叠加，不支持 `inset` 和扩展半径 |
| `-webkit-text-stroke` | 文字描边 | 居中描边会影响字重，可用伪元素覆盖修复 |
| `-webkit-text-fill-color` | 文字填充色 | 优先级高于 `color`，配合渐变/描边使用 |
| `background-clip: text` | 文字渐变 | 需配合 `text-fill-color: transparent` |
| `text-decoration` | 下划线/删除线等 | 支持 `wavy`、`dotted` 等样式 |
| `text-emphasis` | 文字强调标记 | 支持圆点、三角等标记样式 |
| `writing-mode` | 竖排/横排书写 | 配合 `text-orientation` 控制字符朝向 |
| `word-break` / `overflow-wrap` | 断词换行 | `break-all` 强制断行，`break-word` 仅在必要时断行 |
| `text-transform` | 大小写转换 | `uppercase` / `capitalize` / `lowercase` |
| `letter-spacing` | 字间距 | 用 `em` 单位更灵活 |
| `white-space` | 空白处理 | 控制换行和空白符保留策略 |
