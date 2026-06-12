# CSS Transition（CSS 过渡）

> 本质：从一个状态平滑过渡到另一个状态。

## 核心概念

过渡需要回答五个问题：

1. **如何切换状态？** — 利用伪类（`:hover`、`:focus` 等）或 JS 操控（修改类名、行内样式）
2. **哪些属性参与变化？** — `transition-property`
3. **过渡需要多长时间？** — `transition-duration`
4. **过渡的速度曲线是什么样的？** — `transition-timing-function`
5. **何时开始过渡？** — `transition-delay`

---

## transition-property（过渡属性）

指定参与过渡的 CSS 属性名称。

### 语法

```css
/* 过渡所有可动画属性 */
transition-property: all;

/* 过渡指定属性，多个属性用逗号分隔 */
transition-property: background-color, width;
```

### 注意事项

- 绝大多数 CSS 属性都支持过渡（如 `width`、`height`、`opacity`、`transform`、`background-color` 等）
- **不可过渡的属性**：`display`、`background-image`、`content`、`position` 等
- 多个属性与 `transition-duration` 一一对应，用逗号分隔

```css
/* 不同属性设置不同的过渡时间 */
transition-property: background-color, width;
transition-duration: 5s, 10s;
/* background-color 过渡 5s，width 过渡 10s */
```

---

## transition-duration（过渡时间）

设置过渡动画持续的时间。

### 语法

```css
transition-duration: 1s;       /* 1 秒 */
transition-duration: 500ms;    /* 500 毫秒 */
```

- 单位可以是 `s`（秒）或 `ms`（毫秒）
- 多个属性时可分别设置不同的持续时间：

```css
transition-property: background-color, width;
transition-duration: 5s, 10s;
```

---

## transition-timing-function（过渡函数）

控制过渡过程中的速度变化曲线。支持三种形式：**关键字**、**贝塞尔曲线**、**定格动画**。

### 关键字

| 关键字      | 效果描述               | 对应贝塞尔曲线                |
| ----------- | ---------------------- | ----------------------------- |
| `linear`    | 匀速变化               | `cubic-bezier(0, 0, 1, 1)`   |
| `ease`      | 两头慢，中间快（默认） | `cubic-bezier(0.25, 0.1, 0.25, 1)` |
| `ease-in`   | 先慢后快（加速）       | `cubic-bezier(0.42, 0, 1, 1)` |
| `ease-out`  | 先快后慢（减速）       | `cubic-bezier(0, 0, 0.58, 1)` |
| `ease-in-out` | 两头慢，中间快（匀变速） | `cubic-bezier(0.42, 0, 0.58, 1)` |

### 贝塞尔曲线 cubic-bezier()

```css
transition-timing-function: cubic-bezier(0.68, -0.55, 0.27, 1.55);
```

- 接受四个参数 `cubic-bezier(x1, y1, x2, y2)`，定义一条自定义的速度曲线
- x 值范围 `[0, 1]`，y 值可以超出 `[0, 1]`（实现弹跳/回弹效果）

### 定格动画 steps()

```css
transition-timing-function: steps(10);
transition-timing-function: steps(4, start);
transition-timing-function: steps(4, end);
```

- **参数一（步数）**：整数，将过渡分成多少个离散步进
- **参数二（方向）**：`start` 或 `end`，默认 `end`
  - `start`：在每个时间间隔的**开始**时改变属性值
  - `end`：在每个时间间隔的**结束**时改变属性值
- 适用场景：模拟机械运动、电子钟秒针跳动、精灵图逐帧动画

---

## transition-delay（过渡延迟）

控制过渡开始前等待的时间。

### 语法

```css
transition-delay: 2s;   /* 延迟 2 秒后开始过渡 */
transition-delay: -2s;  /* 提前 2 秒开始（相当于从中间位置开始） */
```

- **正值**：延后开始过渡
- **负值**：提前开始过渡（动画直接从某个中间状态开始）

---

## transition 复合写法

将所有过渡属性合并为一行简写。

### 语法

```css
/* 完整格式 */
transition: property duration timing-function delay;

/* 示例 */
transition: background-color 1s ease 0.5s;

/* 只设置时间和延迟（属性默认 all，函数默认 ease） */
transition: 2s 2s;

/* 使用定格动画的简写 */
transition: 2s steps(10);
```

### 简写规则

- `transition: 2s 2s;` → `property: all`，`duration: 2s`，`delay: 2s`
- 多个属性过渡用逗号分隔：

```css
transition: background-color 5s steps(10) -2s, width 10s linear;
```

---

## 过渡事件（transitionend）

过渡过程中会触发一系列事件，可通过 JavaScript 监听。

### 事件列表

| 事件名                | 触发时机           |
| --------------------- | ------------------ |
| `transitionrun`       | 过渡被创建时触发   |
| `transitionstart`     | 过渡真正开始执行时 |
| `transitionend`       | 过渡完成时触发     |
| `transitioncancel`    | 过渡被取消时触发   |

### 代码示例

```javascript
const box = document.querySelector(".box");

box.addEventListener("transitionrun", function () {
  console.log("过渡创建时触发");
});

box.addEventListener("transitionstart", function () {
  console.log("过渡真正触发了");
});

box.addEventListener("transitionend", function () {
  console.log("过渡结束");
});

box.addEventListener("transitioncancel", function () {
  console.log("过渡被取消");
});
```

### 事件触发顺序

```
transitionrun → transitionstart → (过渡执行中) → transitionend
                                          ↘ transitioncancel（若中途取消）
```

> `transitionrun` 和 `transitionstart` 的区别：当设置了 `transition-delay` 时，`transitionrun` 在延迟开始前就触发，而 `transitionstart` 在延迟结束后、动画真正开始时才触发。

---

## 实战案例

### 案例1：定格动画（精灵图逐帧动画）

利用 `steps()` 实现精灵图的逐帧播放效果。

```css
.box {
  width: 50px;
  height: 72px;
  background-image: url(./assets/01.jpg);
  background-position-x: 0px;
}

.box:hover {
  transition: 2s steps(10);
  background-position: -500px;
}
```

**原理分析**：
- 精灵图包含 10 帧，总宽度 500px，每帧 50px
- `background-position` 从 `0` 过渡到 `-500px`，配合 `steps(10)` 将位移分成 10 步
- 每一步恰好移动 50px（一帧的宽度），实现逐帧切换而非平滑位移

---

### 案例2：3D 正方体过渡旋转

结合 `transform` 与 `transition` 实现 3D 正方体的交互旋转。

**核心 CSS**：

```css
/* 透视容器 */
.wrapper {
  perspective: 800px;
}

/* 正方体容器 — 开启 3D 变换 + 过渡 */
.content {
  transform-style: preserve-3d;
  width: 400px;
  height: 400px;
  margin: 200px auto 0;
  position: relative;
  transition: 1s linear;
}

/* 六个面 — 使用 transform 定位 */
.front  { transform: translateZ(200px); }
.back   { transform: rotateY(180deg) translateZ(200px); }
.left   { transform: rotateY(90deg) translateZ(200px); }
.right  { transform: rotateY(-90deg) translateZ(200px); }
.top    { transform: rotateX(90deg) translateZ(200px); }
.bottom { transform: rotateX(-90deg) translateZ(200px); }
```

**JS 控制旋转**：

```javascript
const contentDom = document.querySelector(".content");
const ROTATE_DEG = 90;
let x_deg = 0;
let y_deg = 0;

// 左转
function rotateLeft() {
  y_deg -= ROTATE_DEG;
  contentDom.style.transform = `rotateX(${x_deg}deg) rotateY(${y_deg}deg)`;
}

// 右转
function rotateRight() {
  y_deg += ROTATE_DEG;
  contentDom.style.transform = `rotateX(${x_deg}deg) rotateY(${y_deg}deg)`;
}

// 上翻
function rotateUp() {
  x_deg += ROTATE_DEG;
  contentDom.style.transform = `rotateX(${x_deg}deg) rotateY(${y_deg}deg)`;
}

// 下翻
function rotateDown() {
  x_deg -= ROTATE_DEG;
  contentDom.style.transform = `rotateX(${x_deg}deg) rotateY(${y_deg}deg)`;
}
```

**原理分析**：
- 容器设置了 `transition: 1s linear`，当 JS 修改 `transform` 值时自动触发平滑过渡
- 使用累积角度（`x_deg`、`y_deg`）而非绝对角度，确保每次旋转都从当前位置开始
- `transform-style: preserve-3d` 让子元素保持 3D 空间关系

---

## 过渡使用技巧总结

1. **不要过渡 `display`**：`display: none` → `block` 无法过渡，应使用 `opacity` + `visibility` 组合代替
2. **性能优先**：优先过渡 `transform` 和 `opacity`，它们由 GPU 加速，性能最佳；避免过渡 `width`、`height`、`top`、`left` 等会触发重排的属性
3. **复合写法推荐**：日常开发中推荐使用 `transition` 简写，代码更简洁
4. **`all` 的利弊**：`transition-property: all` 方便但可能带来性能问题，建议明确指定需要过渡的属性
5. **`transitionend` 事件处理**：监听 `transitionend` 时注意每个过渡属性都会触发一次事件，需要通过 `event.propertyName` 判断具体属性
