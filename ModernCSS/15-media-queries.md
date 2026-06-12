# 15. CSS 媒体查询（Media Queries）

> CSS 媒体查询（`@media`）是响应式设计的核心技术，允许根据设备特性（如屏幕宽度、分辨率、方向等）应用不同的样式规则。

---

## 一、基本语法

```css
@media mediatype and (mediafeature: value) {
  /* 符合条件时应用的样式 */
}
```

### 示例

```css
/* 屏幕宽度 ≤ 768px 时生效 */
@media screen and (max-width: 768px) {
  .container {
    flex-direction: column;
  }
}
```

---

## 二、媒体类型（Media Type）

| 类型 | 说明 |
|------|------|
| `all` | 所有设备（默认值） |
| `screen` | 屏幕设备（电脑、手机、平板） |
| `print` | 打印机 / 打印预览 |
| `speech` | 屏幕阅读器 |

```css
/* 仅打印时生效 */
@media print {
  body {
    color: black;
  }
  .no-print {
    display: none;
  }
}
```

---

## 三、逻辑操作符

| 操作符 | 说明 | 示例 |
|--------|------|------|
| `and` | 同时满足所有条件 | `@media screen and (min-width: 768px)` |
| `,`（逗号） | 满足任一条件（或） | `@media screen, print` |
| `not` | 取反（对整个媒体查询取反） | `@media not print` |
| `only` | 防止旧浏览器忽略媒体类型 | `@media only screen and (min-width: 768px)` |

```css
/* and：同时满足 */
@media screen and (min-width: 768px) and (max-width: 1024px) {
  /* 平板样式 */
}

/* 逗号：满足其一 */
@media screen and (max-width: 480px), print {
  /* 手机或打印 */
}

/* not：取反 */
@media not print and (max-width: 500px) {
  /* 非打印且宽度 ≤ 500px */
}
```

---

## 四、常用媒体特性（Media Feature）

### 4.1 视口尺寸

| 特性 | 说明 |
|------|------|
| `width` / `height` | 视口宽度/高度 |
| `min-width` / `max-width` | 最小/最大视口宽度 |
| `min-height` / `max-height` | 最小/最大视口高度 |

```css
@media (max-width: 768px) {
  .sidebar {
    display: none;
  }
}
```

### 4.2 屏幕方向

| 特性 | 说明 |
|------|------|
| `orientation: portrait` | 竖屏 |
| `orientation: landscape` | 横屏 |

```css
@media (orientation: landscape) {
  .video {
    width: 100vw;
    height: 100vh;
  }
}
```

### 4.3 分辨率

| 特性 | 说明 |
|------|------|
| `resolution` | 设备像素密度 |
| `min-resolution` | 最小分辨率 |

```css
/* Retina 屏幕（2倍像素密度） */
@media (-webkit-min-device-pixel-ratio: 2),
       (min-resolution: 192dpi) {
  .logo {
    background-image: url('logo@2x.png');
  }
}
```

### 4.4 其他特性

| 特性 | 说明 |
|------|------|
| `prefers-color-scheme: dark` | 用户偏好暗色模式 |
| `prefers-reduced-motion: reduce` | 用户偏好减少动画 |
| `hover: hover` | 设备支持 hover |
| `pointer: fine` | 精确指针（鼠标）vs `coarse`（触摸） |

```css
/* 暗色模式 */
@media (prefers-color-scheme: dark) {
  body {
    background-color: #1a1a1a;
    color: #e0e0e0;
  }
}

/* 减少动画 */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 五、视口单位

| 单位 | 说明 |
|------|------|
| `vh` | 视口高度的 1%（viewport height） |
| `vw` | 视口宽度的 1%（viewport width） |
| `vmin` | `vh` 和 `vw` 中较小的那个 |
| `vmax` | `vh` 和 `vw` 中较大的那个 |

```css
body {
  min-height: 100vh; /* 最小高度 = 整个视口高度 */
}

.fullscreen {
  width: 100vw;
  height: 100vh;
}
```

> **Tip**：`vmin` 在移动端自适应时特别有用，保证在横竖屏切换时元素大小一致。

---

## 六、常见断点（Breakpoints）

| 断点 | 设备 | 说明 |
|------|------|------|
| `< 480px` | 手机（竖屏） | 小屏移动设备 |
| `480px - 768px` | 手机（横屏）/小平板 | 中等移动设备 |
| `768px - 1024px` | 平板 | 大屏移动 / 小屏桌面 |
| `1024px - 1280px` | 笔记本 | 普通桌面显示器 |
| `> 1280px` | 桌面 / 大屏 | 大屏桌面显示器 |

---

## 七、移动优先 vs 桌面优先

### 移动优先（推荐）

```css
/* 默认样式（手机） */
.container {
  width: 100%;
  padding: 10px;
}

/* 平板 */
@media (min-width: 768px) {
  .container {
    width: 750px;
    padding: 20px;
  }
}

/* 桌面 */
@media (min-width: 1024px) {
  .container {
    width: 980px;
  }
}
```

### 桌面优先

```css
/* 默认样式（桌面） */
.container {
  width: 1200px;
  margin: 0 auto;
}

/* 平板 */
@media (max-width: 1024px) {
  .container {
    width: 100%;
    padding: 0 20px;
  }
}

/* 手机 */
@media (max-width: 768px) {
  .container {
    padding: 0 10px;
  }
}
```

> **Tip**：移动优先（`min-width`）是更推荐的做法，因为移动端通常样式更简单，渐进增强比优雅降级更可靠。

---

## 八、实战技巧

| 技巧 | 说明 |
|------|------|
| **不要只针对特定设备** | 使用断点范围而非固定设备宽度 |
| **内容驱动断点** | 当内容看起来不对时添加断点，而非预设设备尺寸 |
| **移动优先** | 先写手机样式，再用 `min-width` 逐步增强 |
| **视口单位** | 用 `vh`/`vw` 替代 `%` 实现视口相关的尺寸 |
| **CSS 变量 + 媒体查询** | 在媒体查询中改变 CSS 变量值，一处修改全局生效 |
| **`prefers-*`** | 尊重用户的系统偏好设置（暗色模式、减少动画等） |

---

## 九、速查表

```css
/* 基本写法 */
@media screen and (min-width: 768px) { }

/* 多条件 */
@media screen and (min-width: 768px) and (max-width: 1024px) { }

/* 多媒体类型 */
@media screen, print { }

/* 嵌套写法（支持嵌套在 CSS 规则内） */
.container {
  width: 100%;
  @media (min-width: 768px) {
    width: 750px;
  }
}
```
