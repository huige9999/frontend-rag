# 10 - Web Animation API

Web Animation API 是浏览器原生提供的动画接口，可以用 JavaScript 精确控制 CSS 动画，无需依赖第三方库。

---

## 基本用法：Element.animate()

通过 `Element.animate(关键帧, 配置对象)` 创建动画，返回一个 `Animation` 对象。

```js
const box = document.querySelector(".box");

// 随机生成16进制颜色：Math.random().toString(16).slice(2, 8)
function getColor() {
  return "#" + Math.random().toString(16).slice(2, 8);
}

// 创建动画：背景色从随机色过渡到另一个随机色
box.animate(
  [{ backgroundColor: getColor() }, { backgroundColor: getColor() }],
  {
    duration: 2000,  // 持续时间 2 秒
    fill: "forwards" // 动画结束后保持最终状态
  }
);
```

> 来源：`01. index.html`

---

## 关键帧与 offset

关键帧数组中可以用 `offset` 属性指定每帧的位置（0 ~ 1），精确控制动画节奏。

```js
const animateCtrl = box.animate([
  { transform: 'translateX(0px)' },
  { backgroundColor: 'red' },
  // 第三帧：同时变换形状、位移和颜色
  { borderRadius: '100%', transform: 'translateX(500px)', backgroundColor: 'green' }
], {
  duration: 3000,
  fill: 'forwards'
});
```

```js
// 文字雨示例：使用 offset 控制下落节奏
textEle.animate([
  { transform: `translateX(${dx}px)`, offset: 0 },        // 起始：水平偏移
  { transform: `translate(${dx}px, 290px)`, offset: 0.7 }, // 70% 时到达底部
  { transform: `translate(${dx}px, 290px)`, offset: 1 }    // 100% 停留底部
], {
  duration: 1600 ~ 3000,  // 随机时长
  easing: "linear",
  fill: "forwards"
});
```

> 来源：`02. control.html`、`demo/index.js`、`d/index.js`

---

## 配置对象（Timing 属性）

| 属性 | 说明 | 可选值 |
|------|------|--------|
| `duration` | 动画持续时间（毫秒），默认 0 | 数字 |
| `easing` | 缓动函数，控制速度曲线 | `"linear"` / `"ease-in"` / `"ease-out"` / `"ease-in-out"` / `cubic-bezier()` |
| `delay` | 动画开始前的延迟时间（毫秒），默认 0 | 数字 |
| `iterations` | 重复次数，默认 1 | 数字 或 `"infinite"` |
| `direction` | 播放方向 | `"normal"` / `"reverse"` / `"alternate"` / `"alternate-reverse"` |
| `fill` | 动画结束后是否保持状态 | `"none"` / `"forwards"` / `"backwards"` / `"both"` |

---

## 控制动画播放

`animate()` 返回的 `Animation` 对象提供了完整的播放控制方法。

```js
const animateCtrl = box.animate([...], { duration: 3000, fill: 'forwards' });

// 播放 / 继续
animateCtrl.play();

// 暂停
animateCtrl.pause();

// 取消动画（回到初始状态）
animateCtrl.cancel();

// 立即完成动画（跳到最终帧）
animateCtrl.finish();

// 反向播放
animateCtrl.reverse();

// 通过 currentTime 手动控制进度（毫秒）
// 配合 range 滑块实现拖动进度条
timeChange.onchange = function(e) {
  animateCtrl.currentTime = e.target.value;
};
```

> 来源：`02. control.html`

---

## 动画事件

Animation 对象支持以下事件回调：

```js
const animate = element.animate([...], { duration: 2000, fill: 'forwards' });

// 动画完成时触发
animate.onfinish = function() {
  console.log('动画结束了');
  element.remove(); // 常见用法：动画结束后移除元素
};

// 动画被取消时触发
animate.oncancel = function() {
  console.log('动画被取消了');
};

// 动画被移除时触发
animate.onremove = function() {
  console.log('动画被移除了');
};
```

> 来源：`文档.md`

---

## 综合实战：文字雨动画

结合 Web Animation API + requestAnimationFrame 实现持续不断的文字雨效果。

### 核心 JS 逻辑

```js
// 生成随机汉字（常用汉字 Unicode 范围：0x4E00 ~ 0x9FFF）
function getRandomChinese() {
  return String.fromCharCode(parseInt(getRandom(0x4e00, 0x9fff)));
}

// 生成随机字母（36进制转换技巧：0-9, a-z）
function getRandomAlpha() {
  return parseInt(getRandom(0, 36)).toString(36);
}

// 范围随机数
function getRandom(min, max) {
  return Math.random() * (max - min) + min;
}

const cloud = document.querySelector(".cloud");

function run() {
  // 1. 创建文字元素
  const textEle = document.createElement("div");
  cloud.appendChild(textEle);
  textEle.className = "text";
  textEle.innerText = getRandomChinese();

  // 2. 随机水平位置和动画时长
  const dx = getRandom(0, 310);

  // 3. 使用 Web Animation API 执行下落动画
  const animate = textEle.animate([
    { transform: `translateX(${dx}px)`, offset: 0 },
    { transform: `translate(${dx}px, 290px)`, offset: 0.7 },
    { transform: `translate(${dx}px, 290px)`, offset: 1 }
  ], {
    duration: getRandom(1600, 3000),
    easing: "linear",
    fill: "forwards"
  });

  // 4. 动画结束后自动移除 DOM 元素，避免内存泄漏
  animate.onfinish = function() {
    textEle.remove();
  };

  // 5. 递归调用，持续产生新的文字
  requestAnimationFrame(run);
}

requestAnimationFrame(run);
```

> 来源：`demo/index.js`（中文版）、`d/index.js`（字母版）

### 关键 CSS 样式

```css
/* 云朵形状：利用 ::before 伪元素 + box-shadow 组合 */
.cloud {
  position: relative;
  width: 320px;
  height: 100px;
  background-color: #fff;
  border-radius: 100px;
  filter: drop-shadow(0 0 30px #fff); /* 发光投影效果 */
}

.cloud::before {
  content: "";
  width: 110px;
  height: 110px;
  border-radius: 50%;
  background-color: #fff;
  position: absolute;
  top: -50px;
  left: 40px;
  box-shadow: 90px 0 0 30px #fff; /* 右侧云朵凸起 */
}

/* 文字样式：text-shadow 实现发光效果 */
.cloud .text {
  position: absolute;
  top: 40px;
  color: #fff;
  text-shadow: 0 0 5px #fff, 0 0 15px #fff, 0 0 30px #fff;
  transform-origin: bottom;
}

/* 倒影效果 */
.container {
  -webkit-box-reflect: below 1px
    linear-gradient(transparent, transparent, transparent, transparent, #0005);
}
```

> 来源：`demo/index.css`

---

## 小技巧汇总

### 随机颜色生成

```js
// 利用 Number.toString(16) 将随机数转为16进制，截取6位作为颜色值
function getColor() {
  return "#" + Math.random().toString(16).slice(2, 8);
}
```

### 随机字母生成

```js
// 36进制（0-9 + a-z），取整数后转36进制字符串
function getRandomAlpha() {
  return parseInt(Math.random() * 36).toString(36);
}
```

### 随机汉字生成

```js
// 常用汉字 Unicode 范围：0x4E00 ~ 0x9FFF
function getRandomChinese() {
  return String.fromCharCode(parseInt(Math.random() * (0x9fff - 0x4e00) + 0x4e00));
}
```
