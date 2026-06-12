# CSS Houdini

CSS Houdini 是一组底层浏览器 API 的统称，它允许开发者直接介入浏览器的 CSS 渲染引擎，扩展 CSS 的能力。通过 Houdini，开发者可以创建自定义的 CSS 属性、绘制逻辑、布局算法和动画效果，而无需等待浏览器厂商实现。

传统上，开发者提出新的 CSS 特性需要经过 W3C 标准化流程，等待数年才能被浏览器支持。Houdini 打破了这一限制，让开发者能够以 JavaScript 的方式"教"浏览器理解新的样式规则。

### Houdini API 全景

| API | 说明 | 状态 |
|-----|------|------|
| **Paint API** | 自定义绘制逻辑，用于 `background-image`、`border-image` 等 | 可用 |
| **Properties & Values API** | 注册自定义 CSS 属性，定义类型和初始值 | 可用 |
| **Typed OM** | CSS 值的类型化操作接口，替代字符串操作 | 可用 |
| **Layout API** | 自定义布局算法（如瀑布流） | 实验性 |
| **Animation Worklet** | 高性能自定义动画，脱离主线程运行 | 实验性 |
| **Parser API** | 自定义 CSS 语法解析 | 草案阶段 |

---

## 一、Paint API（CSS.paintWorklet）

### 1.1 概述

Paint API 允许开发者通过 JavaScript 的 Canvas 2D API 在 CSS 渲染阶段自定义绘制内容。绘制结果可以作为 `background-image`、`border-image`、`mask-image` 等属性的值使用。

**核心流程：**

1. 使用 `registerPaint()` 注册一个绘制工作单元（Worklet）
2. 在主线程通过 `CSS.paintWorklet.addModule()` 加载 Worklet 脚本
3. 在 CSS 中使用 `paint(workletName)` 引用

### 1.2 注册 Paint Worklet

```js
// paint.js — Worklet 脚本（独立文件）
class Mypaint {
  // 声明需要访问的 CSS 自定义属性
  static get inputProperties() {
    return ["--gap"];
  }

  // paint() 方法：绘制逻辑
  // ctx — Canvas 2D 绘图上下文
  // msg — 绘制区域的尺寸 { width, height }
  // props — 可访问 inputProperties 中声明的自定义属性
  paint(ctx, msg, props) {
    const colors = ["#f00", "#ff0", "#0f0", "#f0f", "#00f", "#0ff"];
    const size = 25;
    // 通过 props.get() 获取自定义属性值
    const gap = +props.get("--gap").toString();

    for (let x = 0; x < msg.width / size; x++) {
      for (let y = 0; y < msg.height / size; y++) {
        ctx.fillStyle = colors[(x + y) % colors.length];
        ctx.fillRect(x * (size + gap), y * (size + gap), size, size);
      }
    }
  }
}

// 注册 Paint Worklet，参数为名称和类
registerPaint("mypaint", Mypaint);
```

### 1.3 主线程加载与 CSS 使用

```html
<style>
  .box {
    width: 500px;
    height: 500px;
    background-image: paint(mypaint); /* 使用 paint() 函数 */
    --gap: 10; /* 传递给 Worklet 的自定义属性 */
  }
</style>

<script>
  if (window.CSS) {
    CSS.paintWorklet.addModule("./paint.js");
  }
</script>
```

### 1.4 关键要点

- `registerPaint()` 必须在独立的 JS 文件中调用（Worklet 运行在独立线程）
- `inputProperties` 让 Worklet 能够响应 CSS 自定义属性的变化
- 修改 `--gap` 的值会自动触发重绘，无需手动干预
- `ctx` 是标准 Canvas 2D 上下文，支持所有绑定绘制 API

### 1.5 浏览器兼容性

Paint API 目前仅 **Chromium 内核浏览器**（Chrome、Edge、Opera）支持。Firefox 和 Safari 尚未实现。建议使用 `if (window.CSS && CSS.paintWorklet)` 做特性检测，并提供回退方案。

---

## 二、CSS Typed OM（类型化对象模型）

### 2.1 概述

CSS Typed OM 是对传统 CSSOM 的升级。传统方式中，CSS 值都是字符串（如 `"20px"`、`"0.5"`），进行数值运算时需要手动解析和拼接字符串，容易出错。

Typed OM 将 CSS 值封装为 **类型化的对象**，支持直接进行数学运算，避免了字符串操作的隐患。

### 2.2 传统方式 vs Typed OM

```js
const box = document.querySelector(".box");

// ❌ 传统方式 — 字符串操作，容易出错
// box.style.opacity = 0.5;
// box.style.opacity += 0.1; // 字符串拼接，结果为 "0.50.1"，出错！

// ✅ Typed OM — 类型化操作
const styleMap = box.attributeStyleMap;
styleMap.set("opacity", "0.5");
```

### 2.3 获取计算样式

```js
// ❌ 传统方式：返回字符串
// const width = getComputedStyle(box).width; // "200px"

// ✅ Typed OM：返回类型化对象
const map = box.computedStyleMap();
// map.get('width') 返回 CSSUnitValue 对象
```

### 2.4 CSS 值的构造与运算

Typed OM 提供了 `CSS` 全局对象上的工厂方法来创建类型化的值：

```js
// 创建 CSS 单位值
const px20 = CSS.px(20);       // CSSUnitValue { value: 20, unit: "px" }
const pct40 = CSS.percent(40); // CSSUnitValue { value: 40, unit: "percent" }

// 值运算 — 返回 CSSMathValue 对象
const result = CSS.px(20).add(CSS.percent(40));
```

### 2.5 CSS 数学表达式

Typed OM 提供了一系列数学构造函数，用于表达复杂的 CSS 计算值：

```js
// CSSMathSum — 对应 CSS calc() 中的加法
const sum = new CSSMathSum(CSS.px(100), CSS.px(100)); // calc(100px + 100px)

// CSSMathProduct — 乘法
const product = new CSSMathProduct(sum, factor);

// CSSMathInvert — 取倒数（除法的实现方式）
const invert = new CSSMathInvert(CSS.number(10)); // 1 / 10

// CSSMathNegate — 取负值
const negate = new CSSMathNegate(CSS.px(100)); // -100px
```

### 2.6 CSS 变换操作

Typed OM 对 CSS Transform 提供了专门的类，可以直接操作变换的各个分量：

```js
// 创建旋转对象
const rotate = new CSSRotate(CSS.deg(0));

// 组合为 transform 值
const transform = new CSSTransformValue([rotate]);
// 等价于 CSS: transform: rotate(0deg);

// 通过 attributeStyleMap 设置
box.attributeStyleMap.set("transform", transform);
```

### 2.7 实战：Typed OM 驱动的旋转动画

```js
const box = document.querySelector(".box");
const rotate = new CSSRotate(CSS.deg(0));
const transform = new CSSTransformValue([rotate]);

box.onmouseenter = function () {
  draw();
};

box.onmouseleave = function () {
  clearInterval(rid);
};

let rid = null;

function draw() {
  rid = setInterval(function () {
    rotate.angle.value += 5; // 直接修改角度值，无需字符串解析
    box.attributeStyleMap.set("transform", transform);
  }, 1000 / 60);
}
```

> **优势**：`rotate.angle.value += 5` 直接对数值进行操作，避免了传统 `element.style.transform = \`rotate(${angle}deg)\`` 的字符串拼接方式。

### 2.8 浏览器兼容性

CSS Typed OM 在 **Chrome 66+、Edge 79+** 中可用。Safari 从 16.4 开始部分支持。Firefox 尚未完全实现。核心方法如 `attributeStyleMap` 和 `computedStyleMap()` 需注意兼容性检测。

---

## 三、@property — 自定义属性注册

### 3.1 概述

`@property` 是 Properties & Values API 的 CSS 声明方式，用于注册 CSS 自定义属性。默认情况下，CSS 自定义属性（`--var`）只是字符串，浏览器不识别其类型，因此无法参与过渡（transition）和动画（animation）。

通过 `@property` 注册后，浏览器可以：
- 识别自定义属性的类型（如 `<length>`、`<color>`、`<percentage>`）
- 为自定义属性设置初始值
- 使自定义属性支持过渡和动画

### 3.2 @property 语法

```css
@property --property-name {
  syntax: "<类型>";
  inherits: true | false;
  initial-value: 初始值;
}
```

| 字段 | 说明 |
|------|------|
| `syntax` | 值的类型定义，如 `<length>`、`<color>`、`<percentage>` 等 |
| `inherits` | 是否允许子元素继承 |
| `initial-value` | 属性的初始默认值 |

### 3.3 syntax 类型符号

| 符号 | 含义 | 示例 |
|------|------|------|
| `+` | 空格分隔的多个同类型值 | `<length>+` → `10px 20px 30px` |
| `#` | 逗号分隔的多个同类型值 | `<color>#` → `red, blue, green` |
| `\|` | 多种类型选其一 | `<length> \| <percentage> \| auto` |

### 3.4 示例：注册多种自定义属性

```css
/* 百分比类型 — 用于过渡动画 */
@property --per {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 15%;
}

/* 空格分隔的长度列表 */
@property --margin-list {
  syntax: "<length>+";
  inherits: false;
  initial-value: 20px 20px 20px 20px;
}

/* 逗号分隔的颜色列表 */
@property --gradient-list {
  syntax: "<color>#";
  inherits: false;
  initial-value: red, blue, green;
}

/* 多类型选择 */
@property --width {
  syntax: "<length> | <percentage> | auto";
  inherits: false;
  initial-value: auto;
}
```

### 3.5 实战：圆环进度条过渡效果

自定义属性 `--per` 注册为 `<percentage>` 类型后，可以对它使用 `transition`：

```css
@property --per {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 15%;
}

.box {
  width: 200px;
  height: 200px;
  background-image: conic-gradient(skyblue var(--per), transparent 15%);
  border-radius: 50%;
  transition-duration: 2s;
  transition-property: --per; /* 对自定义属性启用过渡 */
}

.box:hover {
  --per: 90%; /* hover 时平滑过渡 */
}
```

> **原理**：未注册的自定义属性作为字符串处理，浏览器无法对字符串进行插值计算，因此无法过渡。注册后浏览器知道 `--per` 是百分比，就能正确进行数值插值。

### 3.6 实战：抛物线动画

利用 `@property` 注册 `--x` 和 `--y` 为 `<length>` 类型，配合 `@keyframes` 实现物理运动效果：

```css
@property --x {
  syntax: "<length>";
  inherits: false;
  initial-value: 0px;
}
@property --y {
  syntax: "<length>";
  inherits: false;
  initial-value: 0px;
}

.box {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background-color: skyblue;
  transform: translate(var(--x), var(--y));
  animation: move 1s linear forwards;
}

/* 模拟抛物线：y = 5t² 自由落体 */
@keyframes move {
  10%  { --x: 30px;  --y: 5px;   }
  20%  { --x: 60px;  --y: 20px;  }
  30%  { --x: 90px;  --y: 45px;  }
  40%  { --x: 120px; --y: 80px;  }
  50%  { --x: 150px; --y: 125px; }
  60%  { --x: 180px; --y: 180px; }
  70%  { --x: 210px; --y: 245px; }
  80%  { --x: 240px; --y: 320px; }
  90%  { --x: 270px; --y: 405px; }
  100% { --x: 300px; --y: 500px; }
}
```

> **原理**：`--x` 匀速增长（水平匀速），`--y` 按 5t² 增长（垂直加速），两者组合形成抛物线轨迹。浏览器在关键帧之间对 `<length>` 类型值进行线性插值，实现了平滑的物理运动。

### 3.7 JavaScript 方式注册：CSS.registerProperty()

除了 CSS 的 `@property` 规则，也可以使用 JavaScript 注册：

```js
CSS.registerProperty({
  name: "--width",
  syntax: "<length> | <percentage> | auto",
  inherits: false,
  initialValue: "auto",
});
```

两种方式功能等价，`@property` 更声明式，`CSS.registerProperty()` 更适合动态场景。

### 3.8 浏览器兼容性

| 特性 | Chrome | Edge | Safari | Firefox |
|------|--------|------|--------|---------|
| `@property` | 85+ | 85+ | 15.4+ | 不支持（实验性Flag） |
| `CSS.registerProperty()` | 85+ | 85+ | 不支持 | 不支持 |

`@property` 是目前 Houdini 各 API 中 **兼容性最好** 的一个，Safari 从 15.4 开始支持，Chrome/Edge 全面支持。Firefox 仍在实验阶段。

---

## 四、总结

| API | 核心价值 | 使用场景 |
|-----|---------|---------|
| **Paint API** | 用 Canvas 2D 自定义 CSS 绘制 | 自定义背景图案、边框装饰、动态背景 |
| **Typed OM** | CSS 值的类型化操作 | 动画开发、样式计算、避免字符串错误 |
| **@property** | 注册带类型的自定义属性 | 自定义属性过渡动画、渐变动画、物理运动 |

> **实践建议**：`@property` 是目前最值得在生产中使用的 Houdini API，它解决了 CSS 自定义属性无法参与过渡和动画的核心痛点。Paint API 和 Typed OM 在 Chromium 内核浏览器中表现稳定，可根据项目需求渐进式使用。
