# 09 - requestAnimationFrame

## 什么是 requestAnimationFrame

`requestAnimationFrame`（简称 rAF）是浏览器提供的一个 API，用于在下一次重绘（repaint）之前调用指定的回调函数。它是实现流畅动画的推荐方式，会自动匹配浏览器的刷新频率（通常为 60fps）。

---

## 核心对比：setInterval vs requestAnimationFrame

### setInterval 实现动画

通过 `setInterval` 以固定时间间隔（如 1000/60 ms）来更新元素位置，模拟 60fps 的动画效果。

```javascript
// 用 setInterval 驱动动画 —— 手动控制帧率
let d = 0;
const tid = setInterval(function () {
  d++;
  box.style.left = d + "px";
  if (d >= 300) {
    clearInterval(tid); // 到达目标后清除定时器
  }
}, 1000 / 60); // 约每 16.7ms 执行一次
```

**缺点：** `setInterval` 的时间间隔是固定的，无法与浏览器实际的渲染节奏同步，可能出现掉帧或重复渲染的问题。

### requestAnimationFrame 实现动画

使用 rAF 让浏览器自己决定最佳的回调执行时机，保证与屏幕刷新同步。

```javascript
let rd = 0;

function run() {
  rd++;
  box1.style.left = rd + "px";
  if (rd === 300) {
    cancelAnimationFrame(rid); // 到达目标后取消动画帧
    return;
  }
  requestAnimationFrame(run); // 递归调用，持续驱动动画
}

const rid = requestAnimationFrame(run); // 启动动画，返回动画帧 ID
```

**优势：** 与浏览器刷新率同步、页面不可见时自动暂停、性能更优。

---

## 基本用法

### 启动动画

调用 `requestAnimationFrame(callback)` 即可注册一个在下一帧执行的回调函数，返回值是一个整数 ID，用于后续取消。

```javascript
// 启动并持续运行动画，直到超过 2000ms
function animate(time) {
  console.log(time); // time 是 DOMHighResTimeStamp，表示从页面加载到当前的时间
  if (time > 2000) {
    cancelAnimationFrame(rid);
    return;
  }
  requestAnimationFrame(animate); // 递归注册下一帧
}

const rid = requestAnimationFrame(animate);
```

### 取消动画

使用 `cancelAnimationFrame(id)` 可以取消尚未执行的动画帧回调。

```javascript
cancelAnimationFrame(rid); // 传入 rAF 返回的 ID 即可取消
```

---

## 封装通用动画函数

动画的本质：**一个数值在一段持续时间内，从起始值（from）平滑变化到目标值（to）**。

### 封装思路

- 计算变化速度：`speed = (to - from) / duration`
- 记录起始时间，每帧计算已经过的时间
- 根据已过时间计算当前值：`value = from + speed * elapsed`
- 通过回调函数将当前值交给外部使用

### 完整封装

```javascript
/**
 * 通用动画函数
 * @param {number} from     - 起始值
 * @param {number} to       - 目标值
 * @param {number} duration - 持续时间（毫秒）
 * @param {function} callback - 每帧回调，接收当前计算值
 */
function animate(from, to, duration, callback) {
  const speed = (to - from) / duration; // 每毫秒的变化量
  const startTime = Date.now();         // 记录动画开始时间

  function _run() {
    const time = Date.now() - startTime; // 已经过的时间（ms）

    if (time >= duration) {
      // 时间到达，执行最后一次回调，确保精确到达目标值
      if (typeof callback === "function") {
        callback(to);
      }
      cancelAnimationFrame(rid);
      return;
    }

    // 根据已过时间计算当前值
    let value = from + speed * time;
    if (typeof callback === "function") {
      callback(value);
    }
    requestAnimationFrame(_run); // 注册下一帧
  }

  const rid = requestAnimationFrame(_run); // 启动动画
}
```

### 多属性同时动画

由于 `animate` 函数是完全通用的（与具体属性解耦），可以同时对多个 CSS 属性执行动画：

```javascript
// 同时让元素的 left、height、top、width、opacity 都产生动画
animate(100, 200, 2000, function (val) {
  box.style.left = val + "px";
});
animate(100, 200, 2000, function (val) {
  box.style.height = val + "px";
});
animate(100, 200, 2000, function (val) {
  box.style.top = val + "px";
});
animate(100, 200, 2000, function (val) {
  box.style.width = val + "px";
});
animate(0.5, 1, 2000, function (val) {
  box.style.opacity = val; // opacity 不需要单位
});
```

---

## 关键 API 总结

| API | 说明 |
|-----|------|
| `requestAnimationFrame(callback)` | 注册下一帧回调，返回动画帧 ID |
| `cancelAnimationFrame(id)` | 取消指定的动画帧回调 |

**回调参数：** `callback` 接收一个 `DOMHighResTimeStamp` 参数（高精度时间戳），表示从页面加载到当前帧的时间。

---

## 要点回顾

1. **rAF vs setInterval：** rAF 与浏览器渲染周期同步，性能更优；setInterval 时间间隔固定，可能掉帧。
2. **递归调用模式：** 在回调函数末尾再次调用 `requestAnimationFrame`，形成持续动画循环。
3. **停止动画：** 使用 `cancelAnimationFrame(id)` 配合条件判断来终止动画。
4. **动画本质：** 数值在时间维度上的线性插值 —— `value = from + speed * elapsed`。
5. **回调设计：** 将计算逻辑与 DOM 操作分离，通过 callback 实现通用复用。
