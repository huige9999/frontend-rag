# Buttons库

## 课程简介

本节课学习第三方 CSS 按钮库 **Buttons**，它提供了丰富的按钮样式，包括多种形状、尺寸、颜色、3D效果、光晕效果等。Buttons 库是对 Bootstrap 按钮的有力补充。

---

## 关键知识点

### 形状类

| class | 说明 |
|---|---|
| `.button` | 基础按钮 |
| `.button-rounded` | 长形圆角 |
| `.button-pill` | 胶囊形 |
| `.button-square` | 方形 |
| `.button-box` | 圆角方形 |
| `.button-circle` | 圆形 |

### 尺寸类

| class | 说明 |
|---|---|
| `.button-tiny` | 微小 |
| `.button-small` | 小 |
| （默认） | 正常 |
| `.button-large` | 大 |
| `.button-jumbo` | 特大 |
| `.button-giant` | 巨大 |

### 颜色类

| class | 说明 |
|---|---|
| （无） | 默认灰色 |
| `.button-primary` | 蓝色 |
| `.button-action` | 绿色 |
| `.button-highlight` | 黄色 |
| `.button-caution` | 红色 |
| `.button-royal` | 深蓝色 |

### 特效类

| class | 说明 |
|---|---|
| `.button-border` | 边框按钮 |
| `.button-3d` | 3D立体效果 |
| `.button-raised` | 突起样式 |
| `.button-longshadow-right` | 长阴影 |
| `.button-glow` | 光晕效果 |

### 其他

| class | 说明 |
|---|---|
| `.button-block` | 块级按钮（撑满容器宽度） |
| `.button-wrap` | 额外环绕效果 |
| `.button-uppercase` | 文字转大写 |
| `.button-lowercase` | 文字转小写 |
| `.button-capitalize` | 首字母大写 |
| `.button-small-caps` | 小型大写字母 |

---

## 关键代码示例

### 引入资源

```html
<link rel="stylesheet" href="css/buttons.css">
<script src="js/buttons.js"></script>
```

### 基本形状

```html
<button class="button">默认形状</button>
<button class="button button-rounded">长形圆角</button>
<button class="button button-pill">胶囊</button>
<button class="button button-square">方形</button>
<button class="button button-box">圆角</button>
<button class="button button-circle">圆形</button>
```

### 尺寸与颜色组合

```html
<!-- 微小尺寸，灰色 -->
<button class="button button-tiny button-rounded">Go</button>

<!-- 小尺寸，蓝色 -->
<button class="button button-small button-primary button-pill">Go</button>

<!-- 正常尺寸，绿色 -->
<button class="button button-action button-box">Go</button>

<!-- 大尺寸，黄色 -->
<button class="button button-large button-highlight button-rounded">Go</button>

<!-- 特大尺寸，红色 -->
<button class="button button-jumbo button-caution">Go</button>

<!-- 巨大尺寸，深蓝色 -->
<button class="button button-giant button-royal button-circle">Go</button>
```

### 边框按钮

```html
<button class="button button-circle button-large button-border button-primary">Go</button>
<button class="button button-box button-large button-border button-primary">Go</button>
```

### 3D 按钮

```html
<button class="button button-3d">Go</button>
<button class="button button-3d button-primary button-rounded">Go</button>
<button class="button button-3d button-action button-pill">Go</button>
<button class="button button-3d button-highlight button-square">Go</button>
<button class="button button-3d button-caution button-box">Go</button>
<button class="button button-3d button-royal button-circle">Go</button>
```

### 突起与长阴影按钮

```html
<!-- 突起样式 -->
<button class="button button-raised button-primary button-rounded">Go</button>

<!-- 长阴影 -->
<button class="button button-longshadow-right button-caution button-box">Go</button>
```

### 光晕效果按钮

```html
<button class="button button-glow">Go</button>
<button class="button button-glow button-primary button-rounded">Go</button>
<button class="button button-glow button-royal button-circle">Go</button>
```

### 下拉菜单按钮

```html
<div class="button-dropdown" data-buttons="dropdown">
    <button class="button button-rounded button-royal button-giant">城市</button>
    <ul class="button-dropdown-list">
        <li><a href="#">北京</a></li>
        <li><a href="#">上海</a></li>
        <li class="button-dropdown-divider"><a href="#">广州</a></li>
    </ul>
</div>
```

### 按钮组

```html
<div class="button-group">
    <button class="button button-primary">首页</button>
    <button class="button button-primary">关于我们</button>
    <button class="button button-primary">联系我们</button>
</div>
```

### 堆叠块级按钮

```html
<a href="#" class="button button-block mt-1 button-large button-rounded">Go</a>
<a href="#" class="button button-block mt-1 button-large button-rounded button-primary">Go</a>
<a href="#" class="button button-block mt-1 button-large button-rounded button-action">Go</a>
```

### 文字样式

```html
<a href="#" class="button button-primary button-uppercase">uppercase</a>
<a href="#" class="button button-primary button-lowercase">LOWERCASE</a>
<a href="#" class="button button-primary button-capitalize">capitalize</a>
<a href="#" class="button button-primary button-small-caps">small caps</a>
```

---

## 效果说明

- Buttons 库提供了远超 Bootstrap 的丰富按钮样式，组合性极强
- 形状、尺寸、颜色、特效可以自由组合
- 支持下拉菜单、按钮组、块级按钮等复合用法
- 适合需要视觉冲击力的页面场景
