# 11 - IntersectionObserver API

## 概述

IntersectionObserver API 提供了一种异步检测目标元素与祖先元素或顶级视口交叉状态的方式。常用于**懒加载、无限滚动、曝光统计**等场景，比监听 scroll 事件 + getBoundingClientRect 性能更优。

---

## 一、基础用法

### 1.1 创建观察者

```js
// 创建 IntersectionObserver 实例
// 参数1: 回调函数，交叉状态变化时触发
// 参数2: 配置选项（可选）
const ob = new IntersectionObserver(
  (entries) => {
    console.log(entries); // entries 是一个数组，包含所有被观察目标的状态
  },
  {
    root: content,       // 指定根元素（视口），默认为浏览器视口
    rootMargin: '20px',  // 根元素的边距，用于扩展/缩小触发区域
    threshold: 1,        // 交叉比例阈值，1 表示目标 100% 可见时才触发
  }
);
```

### 1.2 观察目标元素

```js
// 开始观察指定元素
ob.observe(box);
```

### 1.3 回调中判断交叉状态

```js
const ob = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      // 目标元素进入可视区域
      console.log("满足交叉条件");
      ob.unobserve(entry.target); // 停止观察该元素（一次性触发）
    } else {
      // 目标元素离开可视区域
      console.log("不满足交叉条件");
    }
  });
});
```

### 1.4 配置选项说明

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `root` | 用作视口的祖先元素 | `null`（浏览器视口） |
| `rootMargin` | 根元素的外边距，可提前或延后触发 | `'0px'` |
| `threshold` | 触发回调的交叉比例，可以是数字或数组 | `0` |

**threshold 值的含义：**
- `0`：目标刚进入/刚离开视口就触发
- `0.5`：目标 50% 可见时触发
- `1`：目标 100% 可见时触发
- `[0, 0.25, 0.5, 0.75, 1]`：在每个比例变化点都触发

### 1.5 常用方法

| 方法 | 说明 |
|------|------|
| `observe(target)` | 开始观察目标元素 |
| `unobserve(target)` | 停止观察目标元素 |
| `disconnect()` | 停止观察所有目标元素 |

### 1.6 entry 对象关键属性

| 属性 | 说明 |
|------|------|
| `target` | 被观察的目标元素 |
| `isIntersecting` | 布尔值，目标是否与视口交叉 |
| `intersectionRatio` | 目标元素的可见比例（0~1） |
| `boundingClientRect` | 目标元素的边界矩形 |
| `rootBounds` | 根元素的边界矩形 |

---

## 二、实际案例：图片懒加载

### 2.1 原理说明

页面初始加载时，所有图片使用同一个占位图（`default.png`），真实图片地址存在 `data-src` 自定义属性中。当图片滚动进入视口时，通过 IntersectionObserver 检测到交叉，将 `data-src` 赋值给 `src`，实现按需加载。

### 2.2 HTML 结构

```html
<!-- 所有 img 初始 src 为占位图，真实地址存在 data-src 中 -->
<img src="./default.png" alt="" data-src="https://picsum.photos/400/600?r=1" />
<img src="./default.png" alt="" data-src="https://picsum.photos/400/600?r=2" />
<!-- 更多图片... -->
```

### 2.3 样式布局（CSS Grid 网格排列）

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #f5f5f5;
}

/* 使用 CSS Grid 实现 4 列等宽布局 */
.card-list {
  width: 80%;
  margin: 50px auto;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
}

.item {
  overflow: hidden;
}

/* 图片自适应容器，保持比例 */
.item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
```

### 2.4 核心 JS 逻辑

```js
// 创建观察者，threshold 为 0 表示目标刚进入视口即触发
const ob = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      // 将 data-src 的真实地址赋给 src，触发图片加载
      img.src = img.dataset.src;
      // 加载完成后停止观察，避免重复触发
      ob.unobserve(img);
    }
  });
}, {
  threshold: 0,
});

// 选取所有带有 data-src 的图片
const imgs = document.querySelectorAll("img[data-src]");

// 逐一加入观察
imgs.forEach((img) => {
  ob.observe(img);
});
```

### 2.5 懒加载流程总结

1. 页面加载时，所有图片显示占位图 `default.png`
2. IntersectionObserver 观察所有 `img[data-src]` 元素
3. 当某个图片进入视口（`isIntersecting === true`）
4. 读取 `data-src`，赋值给 `src`，浏览器开始下载真实图片
5. 调用 `unobserve()` 停止观察该图片，防止重复加载
