# GSAP

## 1. 目录结构整理

```
GSAP
├── 02. GSAP基础入门        （核心 API / 语法 / 配置项）
├── 03. GSAP插件使用        （官方插件生态：滚动/拖拽/路径/文字/SVG 等）
├── 04. GSAP动效案例        （动效案例代码包）
└── 05. GSAP整页实战        （整页落地实战项目）
```

> 注：本课章节编号从 02 起（无 01），保留原编号不重排。02、03 为理论与 API 章节（带配套 md 文档）；04、05 为实战章节，仅有代码案例包（zip），无独立说明文档。

---

## 2. 章节逻辑说明

这门课是**「GSAP 动画库从语法到落地」**的完整学习路径，主线是**「核心 API（02）→ 插件生态（03）→ 动效案例（04）→ 整页实战（05）」**，先建立 GSAP 本身的动画能力，再接入官方插件扩展场景，最后用案例和整页项目收口。

### 先讲什么：核心 API 与语法（02 基础入门）

讲解 GSAP 本体（不依赖任何插件）能做什么，是整门课的地基。涵盖：

- **概述**：GSAP 是什么、官网在哪、定位（高性能、跨浏览器的网页动画库）。
- **动画方法**：`gsap.to()` / `gsap.from()` / `gsap.fromTo()` / `gsap.set()` 四个核心 tween。
- **动画配置项**：duration、ease、stagger、repeat、yoyo、delay、overwrite、回调等通用 vars。
- **控制方法**：拿到动画引用后的 play / pause / reverse / restart / timeScale / seek / kill 等。
- **实用工具方法**：clamp / random / wrap / interpolate / mapRange 等辅助函数。
- **缓动配置项**：`power*` / `circ` / `expo` / `sine` / `elastic` / `back` / `bounce` / `steps` 及 `.in/.out/.inOut`。
- **钩子函数**：onStart / onComplete / onUpdate 及其 Params。
- **默认值与全局配置**：`gsap.defaults()` / `gsap.config()`。
- **时间线 timeline**：创建、链式编排、`defaults` 继承、位置参数（position parameter，如 `'-=0.7'` / `'<'` / `'<25%'`）。
- **对象属性变化**：对 JS 对象做数值插值（如计数器动画）。
- **Media 响应式**：`gsap.matchMedia()` 按视口条件启用/回收动画。
- **Stagger 高级配置**：amount / from / grid / axis 等交错参数。

**为什么先讲这些**：上面这些都是 GSAP 的「无插件基础」，插件全部建立在 tween + timeline + 配置项之上。不掌握本体 API，插件根本用不动。

### 再讲什么：官方插件生态（03 插件使用）

在掌握本体后，逐个介绍 GSAP 官方插件如何扩展特定场景。涵盖：

- **GSDevTools 调试**：可视化调试时间线。
- **ScrollTrigger 滚动**：滚动驱动动画（trigger / start / toggleActions / scrub / pin / markers），是日常用得最多的插件。
- **ScrollSmoother 平滑滚动**：配合 ScrollTrigger 做丝滑滚动 + 视差（speed / lag / scrollTo）。
- **Flip 结构转换**：记录 DOM 状态后让布局变化平滑过渡（First-Last-Invert-Play）。
- **TextPlugin / SplitText 文字**：文字逐字/逐词打字、分行 + 掩膜动画。
- **Observer 监听**：统一监听 wheel / pointer / touch 等输入（onDown / onUp / onChangeX/Y）。
- **Draggable / InertiaPlugin 拖放**：拖拽 + 惯性 + 吸附（snap）。
- **DrawSVGPlugin / MorphSVGPlugin SVG**：路径描边绘制 + 形态变形（圆变河马等）。
- **MotionPathPlugin / MotionPathHelper 路径**：沿自定义路径运动 + 可视化调参。

**为什么这么安排**：插件是「能力扩展层」，每个插件对应一类高阶场景（滚动、拖拽、路径、文字、SVG……）。放在基础 API 之后，是因为它们都用 tween/timeline 的写法，只是动画对象和 vars 不同；放在一起讲也便于横向对比「同一需求用哪个插件」。

### 然后讲什么：动效案例（04）

把前两章的 API + 插件组合成一个个独立的动效案例（代码包），从「会写 API」过渡到「能拼出常见效果」。

### 最后讲什么：整页实战（05）

用一整个页面级别的项目把所有知识串起来落地，作为整门课的收口。

### 主线一句话总结

**核心 API（02）→ 官方插件生态（03）→ 动效案例（04）→ 整页实战（05）**，目标是从「能写单个 tween」进阶到「能做出一个滚动叙事/视差/交互的整页动效网站」。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：落地页 / 官网首屏滚动叙事、产品展示视差、数字打字计数、卡片列表进场 stagger、拖拽交互、SVG 描边与变形、整页丝滑滚动效果。
- **和实际项目的关系**：ScrollTrigger + ScrollSmoother 是现代高端官网的标配组合；Flip 适合做「列表筛选/排序后」的平滑过渡；Draggable + Inertia 适合做轮盘、滑块类交互。
- **和其他课程的关系**：本课是动画方向；和《第三方库》里的 Animate.css 互补（Animate.css 偏现成 CSS 动画，GSAP 偏可编程、可编排、可滚动驱动的高阶动效）。
- **面试/教学高频场景**：GSAP 与 CSS 动画的取舍、ScrollTrigger 的 start/scrub/pin 概念、timeline 的 position parameter、stagger 交错进场，是动效相关场景的高频点。

---

## 4. 临时补充

- 02、03 两章的配套 md 都是「代码片段速查表」风格（每个小节给一段最小可运行示例），**非常适合当 API 速查表**，复习时直接按需查小节即可。
- 04、05 章只有 zip 代码包、没有配套 md，复习时需要直接看代码（解压 zip 查看源码）。如果后续要补文档，可考虑把案例拆成「效果截图 + 关键代码 + 用到的插件」三件套。
- GSAP 部分插件（ScrollSmoother / MorphSVG / DrawSVG / SplitText / Inertia / CustomEase 等）是 **Club GSAP 付费插件**，免费版只含核心包 + ScrollTrigger / Flip / Observer / Draggable 等。实际项目用到付费插件时注意授权。
- 官网 Ease Visualizer（greensock.com/ease-visualizer）是挑缓动函数最好的工具，缓动那一节可配合它使用。
