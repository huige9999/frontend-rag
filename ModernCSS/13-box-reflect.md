# 13. CSS box-reflect（倒影/镜像）

> CSS `box-reflect` 属性用于创建元素的倒影/镜像效果，常用于制作水面倒影、镜像对称等视觉效果。

> ⚠️ 注意：`box-reflect` 目前仍为 WebKit 实验性属性，需加 `-webkit-` 前缀，主要在 Chrome/Safari 中支持。

---

## 一、基本语法

```css
-webkit-box-reflect: direction offset mask-box-image;
```

| 参数 | 说明 | 可选值 |
|------|------|--------|
| **direction** | 倒影方向 | `above`、`below`、`left`、`right` |
| **offset**（可选） | 倒影与原图之间的间距 | 长度值，如 `10px`、`20px` |
| **mask-box-image**（可选） | 倒影的遮罩效果 | 渐变、图片等，如 `linear-gradient(...)` |

---

## 二、方向（direction）

```css
/* 下方倒影 */
-webkit-box-reflect: below;

/* 上方倒影 */
-webkit-box-reflect: above;

/* 左侧倒影 */
-webkit-box-reflect: left;

/* 右侧倒影 */
-webkit-box-reflect: right;
```

### 嵌套镜像案例

通过嵌套元素配合 `box-reflect`，可以创建多重镜像效果：

```css
.box {
  width: 200px;
  height: 200px;
  background-color: skyblue;
  clip-path: polygon(
    50% 0%, 61% 35%, 98% 35%, 68% 57%,
    79% 91%, 50% 70%, 21% 91%, 32% 57%,
    2% 35%, 39% 35%
  );
}

/* 四重镜像：右→下→右→下 */
.s-1 {
  width: 200px;
  height: 200px;
  -webkit-box-reflect: right;
}
.s-2 {
  -webkit-box-reflect: below;
}
.s-3 {
  width: 400px;
  height: 400px;
  -webkit-box-reflect: right;
}
.s-4 {
  -webkit-box-reflect: below;
}
```

```html
<!-- 嵌套结构实现四重镜像 -->
<div class="s-4">
  <div class="s-3">
    <div class="s-2">
      <div class="s-1">
        <div class="box"></div>
      </div>
    </div>
  </div>
</div>
```

> **Tip**：利用 `clip-path` 裁剪出不规则形状（如五角星），再配合 `box-reflect` 嵌套，可以快速生成万花筒般的对称图案。

---

## 三、偏移（offset）

```css
/* 下方倒影，间距 10px */
-webkit-box-reflect: below 10px;

/* 上方倒影，间距 20px */
-webkit-box-reflect: above 20px;
```

> **Tip**：偏移值可以制造倒影与原图之间的"空隙"，模拟水面与物体之间的距离感。

---

## 四、遮罩（mask-box-image）

使用渐变作为遮罩，可以实现倒影的渐隐效果：

```css
/* 下方倒影 + 渐变遮罩（从透明到黑色 = 逐渐消失） */
-webkit-box-reflect: below 10px linear-gradient(transparent, #000);
```

### 图片倒影案例

```css
.pic {
  width: 500px;
  position: absolute;
  left: calc(50% - 250px);
  /* 下方倒影，间距10px，渐变遮罩实现自然过渡 */
  -webkit-box-reflect: below 10px linear-gradient(transparent, #000);
}
```

```html
<img src="./photo.webp" alt="" class="pic">
```

> **原理**：`linear-gradient(transparent, #000)` 作为遮罩，倒影从上到下由透明渐变为黑色——但因为遮罩本身是渐变透明度，视觉上表现为倒影逐渐消失。

---

## 五、实用技巧

| 技巧 | 说明 |
|------|------|
| **背景色** | 倒影效果在深色背景（如 `#000`）下表现最佳 |
| **渐变遮罩** | 使用 `linear-gradient(transparent, #000)` 实现自然渐隐 |
| **偏移距离** | 合理设置 offset 模拟真实距离感 |
| **多重镜像** | 通过 DOM 嵌套 + 不同方向 `box-reflect` 实现万花筒效果 |
| **搭配 clip-path** | 结合 `clip-path` 裁剪形状，制作对称图案 |
| **兼容性** | 需加 `-webkit-` 前缀，Firefox 不支持（可用 SVG/SVG filter 替代） |

---

## 六、属性速查

```css
/* 完整写法 */
-webkit-box-reflect: [above|below|left|right] [offset] [mask-image];

/* 常用组合 */
-webkit-box-reflect: below;                                          /* 无间距无遮罩 */
-webkit-box-reflect: below 10px;                                     /* 有间距无遮罩 */
-webkit-box-reflect: below 10px linear-gradient(transparent, #000);  /* 完整效果 */
```
