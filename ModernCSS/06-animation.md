# 06. CSS Animation（动画）

> CSS `animation` 属性可以实现复杂的动画效果，无需依赖 JavaScript 定时器，性能更优。与 `transition` 不同，animation 可以自动触发、循环播放、支持多关键帧。

---

## 一、核心概念

动画的两个核心组成部分：

1. **`animation`** — CSS 属性，控制动画的执行方式
2. **`@keyframes`** — 定义动画的关键帧规则

```css
.box {
  animation: change 2s linear 2s;
}

@keyframes change {
  0%   { transform: translateX(0) translateY(0); }
  50%  { transform: translateX(300px) translateY(0); }
  100% { transform: translateX(300px) translateY(300px); }
}
```

---

## 二、@keyframes 关键帧

### 基本语法

```css
@keyframes 动画名称 {
  关键帧选择器 { css样式 }
}
```

### 命名规则

- 任意字母（a～z 或 A～Z）
- 数字（0～9）、短横线（-）、下划线（_）
- 不能是 CSS 关键字：`none`、`initial`、`unset`、`inherit` 等
- 不能以十进制数字开头

### from/to 与 百分比

```css
/* from/to 写法 */
@keyframes a1 {
  from { color: red; }
  to   { color: blue; }
}

/* 等价的百分比写法 */
@keyframes a2 {
  0%   { color: red; }
  100% { color: blue; }
}
```

### 关键帧注意事项

| 要点 | 说明 |
|------|------|
| **顺序无关** | 关键帧的定义顺序不影响动画执行 |
| **初始帧可省略** | 不设置 0% 时，以元素默认样式为基础 |
| **!important 无效** | 关键帧中的 `!important` 不生效 |
| **合并相同帧** | 逗号分隔，如 `0%, 100% { ... }` |
| **重复定义覆盖** | 同百分比重复定义会覆盖相同属性 |

```css
/* 合并关键帧 — 钟摆运动 */
@keyframes pendulum {
  0%, 100% { transform: rotate(45deg); }
  50%      { transform: rotate(-45deg); }
}
```

---

## 三、animation 子属性

### 3.1 animation-name

指定 `@keyframes` 定义的动画名称。

```css
animation-name: change;
```

### 3.2 animation-duration

动画持续时间，同 `transition-duration`。

```css
animation-duration: 2s;
```

### 3.3 animation-timing-function

时间函数，同 `transition-timing-function`。支持 `linear`、`ease`、`ease-in`、`ease-out`、`ease-in-out`、`cubic-bezier()`、`steps()` 等。

```css
animation-timing-function: linear;
animation-timing-function: cubic-bezier(0.68, -0.55, 0.27, 1.55);
```

### 3.4 animation-delay

动画延迟时间。**支持负值**——负值表示动画跳过前 N 秒直接从中间开始。

```css
animation-delay: 2s;   /* 延迟 2 秒开始 */
animation-delay: -0.5s; /* 从动画 0.5 秒处开始 */
```

### 3.5 animation-iteration-count

动画播放次数，默认 `1`。支持小数。

```css
animation-iteration-count: 3;    /* 播放 3 次 */
animation-iteration-count: 1.2;  /* 播放 1.2 次（到 20% 处停止） */
animation-iteration-count: infinite; /* 无限循环 */
```

### 3.6 animation-direction

动画播放方向：

| 值 | 说明 |
|------|------|
| `normal` | 每轮 0% → 100%（默认） |
| `reverse` | 每轮 100% → 0% |
| `alternate` | 奇数轮 0% → 100%，偶数轮 100% → 0%（往返） |
| `alternate-reverse` | 奇数轮 100% → 0%，偶数轮 0% → 100% |

```css
/* 正向旋转 vs 反向旋转 */
.box1 { animation: rotate1 2s linear; }
.box2 { animation: rotate1 2s linear; animation-direction: reverse; }

@keyframes rotate1 {
  from { transform: rotate(0); }
  to   { transform: rotate(360deg); }
}
```

### 3.7 animation-fill-mode

动画填充模式——控制动画开始前和结束后的样式：

| 值 | 说明 |
|------|------|
| `none` | 默认，动画前后回到初始状态 |
| `forwards` | 动画结束后保留最后一帧样式 |
| `backwards` | 延迟期间应用第一帧样式 |
| `both` | 同时遵循 forwards 和 backwards |

```css
/* 钟摆效果：2次播放，结束后保留最后一帧 */
animation: rotate 2s .5s ease-in-out 2 both;
```

### 3.8 animation-play-state

动画播放状态：

| 值 | 说明 |
|------|------|
| `running` | 播放中（默认） |
| `paused` | 暂停 |

```css
/* hover 时才播放 */
.box {
  animation: colorChange 2s linear infinite paused;
}
.box:hover {
  animation-play-state: running;
}
```

---

## 四、animation 复合写法

```css
/* 完整语法 */
animation: name duration timing-function delay iteration-count direction fill-mode play-state;

/* 示例 */
animation: change 2s linear 1s infinite alternate both;
```

> **Tip**：`duration` 和 `delay` 都是时间值，第一个被解析为 `duration`，第二个为 `delay`。

---

## 五、多动画

一个元素可以同时应用多个动画，用逗号分隔：

```css
.emo-animate {
  animation-name: emo-y, emo-opacity, emo-scale, move-x_1;
  animation-duration: 2s;
  animation-timing-function: linear;
}
```

每个 `@keyframes` 控制一个维度的运动，组合实现复杂效果：

```css
@keyframes emo-y {
  to { top: -400px; }
}
@keyframes emo-opacity {
  0%, 30% { opacity: 1; }
  to { opacity: 0; }
}
@keyframes emo-scale {
  0%, 10% { transform: scale(0.3); }
  to { transform: scale(1.2); }
}
@keyframes emo-x {
  from { left: 0; }
  20%  { left: 15px; }
  50%  { left: -5px; }
  100% { left: 20px; }
}
```

---

## 六、动画事件

JavaScript 可以监听动画的生命周期事件：

| 事件 | 触发时机 |
|------|----------|
| `animationstart` | 动画开始 |
| `animationend` | 动画结束 |
| `animationiteration` | 一次迭代完成（新一轮开始时） |
| `animationcancel` | 动画被取消 |

```javascript
const box = document.querySelector(".box");

box.onanimationstart = function () {
  console.log("动画开始了");
};
box.onanimationend = function () {
  console.log("动画结束了");
};
box.onanimationcancel = function () {
  console.log("动画被取消");
};
box.onanimationiteration = function () {
  console.log("完成一次动画迭代");
};
```

---

## 七、实战案例

### 7.1 钟摆效果

利用 `transform-origin` 将旋转中心上移，实现钟摆运动：

```css
.box {
  width: 200px;
  height: 200px;
  margin: 400px auto;
  border-radius: 50%;
  background-color: #f00;
  transform-origin: center -800px; /* 旋转中心上移 800px */
  animation: rotate 2s 0.5s ease-in-out 2 both;
}

@keyframes rotate {
  0%, 100% { transform: rotate(30deg); }
  50%      { transform: rotate(-30deg); }
}
```

> **原理**：`transform-origin: center -800px` 将变换原点设在元素上方 800px 处，配合 `rotate(±30deg)` 形成钟摆。

### 7.2 录音条（声音线条）

多个竖条使用 `scaleY` 动画，通过不同的负 `delay` 错开节奏：

```css
li {
  width: 10px;
  height: 50px;
  float: left;
  background-color: skyblue;
  margin-left: 10px;
  animation: scale 1s linear;
  animation-iteration-count: 1.2;
}

@keyframes scale {
  0%, 100% { transform: scaleY(0.1); }
  50%      { transform: scaleY(1); }
}

/* 每根条使用不同的负 delay，形成错落效果 */
li:nth-of-type(1) { animation-delay: -0.2s; }
li:nth-of-type(2) { animation-delay: -0.5s; }
li:nth-of-type(3) { animation-delay: -0.1s; }
/* ... */
```

> **Tip**：负 `animation-delay` 让动画从中间某帧开始，从而无需等待就产生错落效果。

### 7.3 直播点赞效果

完整的直播点赞气泡动画，综合运用多动画、JS 动态创建、animationend 事件清理：

**核心思路**：
1. 点击按钮 → 创建表情元素 → 随机分配水平移动路径
2. 多动画同时执行：上移 + 透明度 + 缩放 + 左右摇摆
3. 动画结束自动移除 DOM

```css
/* 表情基础样式 */
.emo {
  position: absolute;
  left: 0;
  top: 0;
  font-size: 32px;
}
.emo-animate {
  animation-duration: 2s;
  animation-timing-function: linear;
}
```

```javascript
const likeBtn = document.querySelector(".like-btn");
const addEmoji = document.querySelector(".add-emoji");
const X_TYPE = 11;

likeBtn.addEventListener("click", function () {
  // 按钮抖动
  likeBtn.classList.add("btn-scale");

  // 创建表情气泡
  const ele = document.createElement("div");
  ele.classList.add("emo", "emo-animate");
  ele.innerText = getRandomEmoji();
  // 随机选择水平移动路径
  ele.style.animationName =
    `emo-y, emo-opacity, emo-scale, move-x_${Math.ceil(Math.random() * X_TYPE)}`;
  addEmoji.appendChild(ele);
});

// 按钮动画结束后移除 class
likeBtn.addEventListener("animationend", function () {
  likeBtn.classList.remove("btn-scale");
});

// 表情动画结束后移除 DOM
addEmoji.addEventListener("animationend", function (e) {
  e.target.remove();
});
```

**水平移动**：预定义 11 条不同的左右摇摆路径（`move-x_1` ~ `move-x_11`），随机分配给每个气泡，让它们看起来各不相同：

```css
/* 示例：move-x_1 */
@keyframes move-x_1 {
  0%  { }
  10% { left: 0; }
  50% { left: 8px; }
  75% { left: -15px; }
  100% { left: 15px; }
}

/* 示例：move-x_2 */
@keyframes move-x_2 {
  0%  { }
  10% { left: 0; }
  50% { left: 25px; }
  75% { left: 10px; }
  100% { left: 5px; }
}
```

**自动气泡区域**（背景持续飘动的气泡）：

```javascript
for (let i = 0; i < 50; i++) {
  const ele = document.createElement("div");
  ele.classList.add("emo", "emo-animate");
  ele.innerText = getRandomEmoji();
  ele.style.opacity = 0.2;
  ele.style.animationIterationCount = "infinite";
  ele.style.animationDelay = i * 0.1 + -25 + "s"; // 负 delay 错开
  ele.style.animationName = `emo-y, emo-scale, move-x_${Math.ceil(Math.random() * 11)}`;
  autoEmoji.appendChild(ele);
}
```

---

## 八、属性速查表

| 属性 | 说明 | 默认值 |
|------|------|--------|
| `animation-name` | 动画名称 | `none` |
| `animation-duration` | 持续时间 | `0s` |
| `animation-timing-function` | 时间函数 | `ease` |
| `animation-delay` | 延迟时间 | `0s` |
| `animation-iteration-count` | 播放次数 | `1` |
| `animation-direction` | 播放方向 | `normal` |
| `animation-fill-mode` | 填充模式 | `none` |
| `animation-play-state` | 播放状态 | `running` |

---

## 九、实用技巧

| 技巧 | 说明 |
|------|------|
| **负 delay 错开** | 用负 `animation-delay` 让多个元素动画错落有致 |
| **多动画组合** | 同时使用多个 `@keyframes` 控制不同维度（位移、缩放、透明度等） |
| **animationend 清理** | 监听 `animationend` 事件移除 DOM，防止内存泄漏 |
| **fill-mode: both** | 配合 delay 使用，确保延迟期间和结束后都有正确样式 |
| **GPU 加速** | 优先动画 `transform` 和 `opacity`，触发 GPU 合成 |
| **will-change** | 对频繁动画的元素设置 `will-change: transform` 提示浏览器优化 |
