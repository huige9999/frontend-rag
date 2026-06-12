# 06-03 Animate.css 动画库

## 📚 章节概述

Animate.css 是一个跨浏览器的动画库，提供了丰富的开箱即用的 CSS 动画效果。它可以轻松地为网页元素添加各种动画效果，无需编写复杂的 CSS 动画代码。

## 🎯 学习目标

- 理解 Animate.css 的基本概念和使用场景
- 掌握 Animate.css 的引入方式
- 学会使用基础动画类名
- 掌握动画延时、重复等高级配置
- 了解如何通过 JavaScript 监听动画事件
- 能够独立完成动画效果的综合应用

## 💡 核心知识点

### 1. 什么是 Animate.css

Animate.css 是一个开源的 CSS 动画库，提供了100+种预定义的动画效果，包括：
- **Attention Seekers**（吸引注意）：bounce、flash、pulse、shake 等
- **Entrance**（进入动画）：fadeIn、slideIn、zoomIn 等
- **Exit**（退出动画）：fadeOut、slideOut、zoomOut 等
- **Flippers**（翻转）：flip、flipInX、flipInY 等
- **Lightspeed**（光速）：lightSpeedInRight、lightSpeedOutLeft 等

### 2. 引入方式

#### CDN 引入（推荐用于学习）
```html
<link
  rel="stylesheet"
  href="https://cdn.bootcdn.net/ajax/libs/animate.css/4.1.1/animate.min.css"
/>
```

#### NPM 安装（推荐用于项目）
```bash
npm install animate.css
```

```javascript
// 在 JavaScript 中引入
import 'animate.css';
```

### 3. 基础使用方法

使用 Animate.css 的核心是添加 CSS 类名：

```html
<!-- 基础语法 -->
<div class="animate__animated animate__动画名称">
  内容
</div>

<!-- 必须包含 animate__animated 类名 -->
<h1 class="animate__animated animate__bounce">
  弹跳效果
</h1>
```

**关键类名说明：**
- `animate__animated`：基础动画类，必须添加
- `animate__动画名称`：具体的动画效果

### 4. 高级配置

#### 动画延时（delay）
```html
<!-- 延时 1-5 秒 -->
<div class="animate__animated animate__bounce animate__delay-1s">
  延时1秒
</div>
<div class="animate__animated animate__bounce animate__delay-2s">
  延时2秒
</div>
<div class="animate__animated animate__bounce animate__delay-3s">
  延时3秒
</div>
<div class="animate__animated animate__bounce animate__delay-4s">
  延时4秒
</div>
<div class="animate__animated animate__bounce animate__delay-5s">
  延时5秒
</div>
```

#### 动画速度（duration）
```html
<!-- 默认速度（1s） -->
<div class="animate__animated animate__bounce">
  正常速度
</div>

<!-- 更快（0.5s） -->
<div class="animate__animated animate__bounce animate__faster">
  更快
</div>

<!-- 快速（0.8s） -->
<div class="animate__animated animate__bounce animate__fast">
  快速
</div>

<!-- 慢速（2s） -->
<div class="animate__animated animate__bounce animate__slow">
  慢速
</div>

<!-- 更慢（3s） -->
<div class="animate__animated animate__bounce animate__slower">
  更慢
</div>
```

#### 动画重复（repeat）
```html
<!-- 无限重复 -->
<div class="animate__animated animate__bounce animate__infinite">
  无限重复
</div>

<!-- 重复1-3次（注意：需要通过 JavaScript 或自定义 CSS 实现） -->
<!-- Animate.css 原生只支持 animate__infinite -->
```

### 5. 常用动画效果示例

```html
<!-- 进入动画 -->
<div class="animate__animated animate__fadeIn">淡入</div>
<div class="animate__animated animate__slideInDown">从下滑入</div>
<div class="animate__animated animate__zoomIn">缩放进入</div>

<!-- 退出动画 -->
<div class="animate__animated animate__fadeOut">淡出</div>
<div class="animate__animated animate__slideOutUp">向上滑出</div>

<!-- 特效动画 -->
<div class="animate__animated animate__jello">果冻效果</div>
<div class="animate__animated animate__bounce">弹跳效果</div>
<div class="animate__animated animate__shake">震动效果</div>
<div class="animate__animated animate__hinge">合页效果</div>
```

### 6. JavaScript 动画控制

#### 监听动画事件
```javascript
// 获取动画元素
const element = document.querySelector('.animate__animated');

// 监听动画结束事件
element.addEventListener('animationend', function() {
  console.log('动画结束！');
  
  // 移除动画类
  element.classList.remove('animate__动画名称');
  
  // 添加新的动画类
  element.classList.add('animate__新动画');
});

// 监听动画开始事件
element.addEventListener('animationstart', function() {
  console.log('动画开始！');
});
```

#### 动态添加动画
```javascript
// 获取元素
const box = document.querySelector('.box');

// 添加动画
function addAnimation(animationName) {
  // 移除之前的动画类（保留基础类）
  box.classList.remove('animate__fadeIn', 'animate__bounce');
  
  // 添加新动画
  box.classList.add('animate__animated', `animate__${animationName}`);
}

// 使用示例
addAnimation('fadeIn');
```

### 7. 实际应用案例

#### 案例1：基础动画配置
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>基础动画示例</title>
  <!-- 引入 Animate.css -->
  <link
    rel="stylesheet"
    href="https://cdn.bootcdn.net/ajax/libs/animate.css/4.1.1/animate.min.css"
  />
  <style>
    h1 {
      background: lightblue;
      padding: 20px;
      text-align: center;
    }
  </style>
</head>
<body>
  <!-- 
    核心配置说明：
    - animate__animated：必须的基础动画类
    - animate__jello：果冻弹跳效果
    - animate__delay-2s：延时2秒开始
    - animate__infinite：无限循环
    - animate__faster：动画速度更快（0.5秒）
  -->
  <h1
    class="animate__animated animate__jello animate__delay-2s animate__infinite animate__faster"
  >
    Hello Animate.css
  </h1>
</body>
</html>
```

#### 案例2：文字动画组合
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>文字动画组合</title>
  <link
    rel="stylesheet"
    href="https://cdn.bootcdn.net/ajax/libs/animate.css/4.1.1/animate.min.css"
  />
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    
    /* 使用 Flexbox 布局居中显示 */
    .container {
      display: flex;
      font-size: 3em;
      height: 100vh;
      justify-content: center;
      align-items: center;
    }
    
    /* 为每个字母设置间距 */
    .container span {
      margin: 0 10px;
    }
  </style>
</head>
<body>
  <!-- 
    为 "ANIMATE" 的每个字母应用不同的进入动画
    展示 Animate.css 的多样化动画效果
  -->
  <div class="container">
    <span class="animate__animated animate__rotateIn">A</span>
    <span class="animate__animated animate__zoomIn">N</span>
    <span class="animate__animated animate__slideInDown">I</span>
    <span class="animate__animated animate__flipInY">M</span>
    <span class="animate__animated animate__bounceIn">A</span>
    <span class="animate__animated animate__flipInX">T</span>
    <!-- 延时1秒的光速进入效果 -->
    <span class="animate__animated animate__lightSpeedInRight animate__delay-1s">E</span>
  </div>

  <script>
    // 获取首字母和尾字母元素
    const A = document.querySelector('.animate__rotateIn');
    const E = document.querySelector('.animate__lightSpeedInRight');
    
    // 监听尾字母动画结束事件
    E.addEventListener('animationend', function () {
      // 移除首字母的旋转进入动画
      A.classList.remove('animate__rotateIn');
      
      // 添加合页效果（特殊的退出动画）
      A.classList.add('animate__hinge');
    });
  </script>
</body>
</html>
```

## 🛠️ 实用技巧

### 1. 自定义动画速度
```css
/* 覆盖默认动画速度 */
:root {
  --animate-duration: 0.5s;
  --animate-delay: 0.5s;
}

/* 或者为特定元素设置 */
.custom-speed {
  animation-duration: 2s;
}
```

### 2. 组合多个动画
```javascript
// 通过 JavaScript 动画链实现多个动画
const element = document.querySelector('.box');

element.addEventListener('animationend', function handler() {
  element.classList.remove('animate__fadeIn');
  element.classList.add('animate__bounce');
  
  // 移除监听器避免重复触发
  element.removeEventListener('animationend', handler);
});
```

### 3. 性能优化建议
- **避免过度使用**：不要为所有元素添加动画
- **使用 GPU 加速**：Animate.css 默认使用 transform，性能较好
- **控制动画数量**：同时播放的动画过多会影响性能
- **合理使用 infinite**：无限循环动画会持续消耗资源

## 📝 练习题

### 基础题
1. 为一个按钮添加点击时的弹跳动画效果
2. 创建一个页面加载时的淡入动画
3. 为图片添加鼠标悬停时的缩放动画

### 进阶题
1. 实现一个轮播图的切换动画效果
2. 创建表单验证时的震动动画反馈
3. 制作一个加载动画，使用多个不同动画效果

### 挑战题
1. 制作一个打字机效果的文字动画
2. 实现卡片翻转效果的 3D 动画
3. 创建一个完整的页面转场动画系统

## 🎓 学习建议

1. **循序渐进**：从简单的进入动画开始，逐步掌握复杂效果
2. **实践为主**：多动手尝试不同的动画组合
3. **参考文档**：访问 [Animate.css 官网](https://animate.style/) 查看所有可用动画
4. **注意性能**：在移动设备上测试动画性能表现
5. **创意组合**：尝试将不同动画组合创造独特效果

## 🔗 相关资源

- [Animate.css 官方网站](https://animate.style/)
- [Animate.css GitHub](https://github.com/animate-css/animate.css)
- [所有动画效果演示](https://animate.style/#attention-seekers)
- [CDN 引入地址](https://www.bootcdn.cn/animate.css/)

## ⚠️ 注意事项

1. **类名前缀**：Animate.css 4.x 版本使用 `animate__` 前缀，注意版本兼容性
2. **浏览器兼容性**：现代浏览器都支持，IE 需要考虑降级方案
3. **动画触发**：动画在元素加载时自动开始，可通过 JavaScript 控制
4. **性能影响**：大量动画会影响页面性能，合理使用
5. **可访问性**：考虑为动画敏感用户提供 `prefers-reduced-motion` 选项

```css
/* 尊重用户的动画偏好设置 */
@media (prefers-reduced-motion: reduce) {
  .animate__animated {
    animation: none !important;
  }
}
```
