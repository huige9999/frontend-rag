# CSS3过渡和动画

## 章节介绍

CSS3过渡(Transition)和动画(Animation)是现代Web开发中创建动态效果的重要技术。通过这些特性，我们可以为网站添加流畅的视觉效果，提升用户体验。

## 学习目标

- 理解CSS3过渡的概念和应用场景
- 掌握transition属性的使用方法
- 理解CSS3动画的概念和应用场景
- 掌握animation和@keyframes的使用方法
- 能够运用过渡和动画创建常见的交互效果

---

## 1. CSS3过渡(Transition)

### 1.1 过渡的概念

CSS过渡允许CSS属性值在一定的时间区间内平滑地过渡。这种效果可以在不使用Flash或JavaScript的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

### 1.2 transition属性

#### 语法结构

```css
transition: property duration timing-function delay;
```

#### 参数说明

- **property**: 要过渡的CSS属性名
- **duration**: 过渡持续时间，单位为秒(s)或毫秒(ms)
- **timing-function**: 过渡的时间函数
- **delay**: 过渡延迟时间，单位为秒(s)或毫秒(ms)

#### 简写示例

```css
.box {
  width: 100px;
  height: 100px;
  background: #f40;
  /* 过渡所有属性，持续500ms，延迟1s开始 */
  transition: 500ms 1s;
}

.box:hover {
  width: 200px;
  height: 200px;
  background: #008c8c;
}
```

#### 分开写法

```css
.box {
  /* 过渡属性：width */
  transition-property: width;
  /* 过渡时间：2秒 */
  transition-duration: 2s;
  /* 时间函数：ease-in-out */
  transition-timing-function: ease-in-out;
  /* 延迟时间：1秒 */
  transition-delay: 1s;
}
```

#### 时间函数(timing-function)的可选值

- `ease`: 默认值，开始慢，中间快，结束慢
- `linear`: 匀速
- `ease-in`: 开始慢
- `ease-out`: 结束慢
- `ease-in-out`: 开始和结束都慢
- `cubic-bezier(n,n,n,n)`: 自定义贝塞尔曲线

### 1.3 过渡的应用场景

#### 示例1：图片悬停效果

```css
.dog {
  width: 200px;
  height: 200px;
  margin: 50px auto;
  cursor: pointer;
  opacity: 0.5;
  /* 添加过渡效果 */
  border-radius: 50%;
  transition: 0.5s;
}

.dog:hover {
  box-shadow: 0 0 3px rgb(0, 0, 0);
  opacity: 1;
  /* 组合变换：放大1.5倍并旋转一圈 */
  transform: scale(1.5) rotate(1turn);
}
```

#### 示例2：下拉菜单动画

```css
.menu {
  width: 200px;
  height: 50px;
  background: #409eff;
  color: #fff;
  line-height: 50px;
  margin: 0 auto;
  margin-top: 50px;
  text-align: center;
  cursor: pointer;
  position: relative;
  border-radius: 5px;
}

.sub-menu {
  background: #ddebfd;
  font-size: 14px;
  line-height: 35px;
  color: #333;
  padding: 10px 0;
  position: absolute;
  width: 100%;
  border-radius: 5px;
  /* 初始状态：Y轴缩放为0（隐藏） */
  transform: scaleY(0);
  /* 变换原点：顶部中心 */
  transform-origin: center top;
  /* 过渡效果：0.3秒 */
  transition: 0.3s;
}

.menu:hover .sub-menu {
  /* 鼠标悬停时：Y轴缩放为1（显示） */
  transform: scaleY(1);
}
```

#### 示例3：小球移动动画

```css
.box {
  width: 30px;
  height: 30px;
  background: #f40;
  position: fixed;
  left: 0;
  top: 0;
  /* 圆形 */
  border-radius: 50%;
  /* 平滑过渡效果 */
  transition: 0.5s;
}

/* 通过JavaScript改变transform属性来移动小球 */
/* box.style.transform = 'translate(' + x + 'px, ' + y + 'px)'; */
```

---

## 2. CSS3动画(Animation)

### 2.1 动画的概念

CSS动画使元素从一种样式逐渐变化为另一种样式。你可以随意改变任意多的样式任意多的次数。使用百分比来规定变化发生的时间，或用关键词"from"和"to"，等同于0%和100%。

### 2.2 @keyframes规则

@keyframes规则用于创建动画。在@keyframes中规定某项CSS样式，就能创建由当前样式逐渐改为新样式的动画效果。

#### 基本语法

```css
@keyframes animation-name {
  from {
    /* 起始状态样式 */
  }
  to {
    /* 结束状态样式 */
  }
}
```

#### 使用百分比

```css
@keyframes animation-name {
  0% {
    /* 起始状态样式 */
  }
  50% {
    /* 中间状态样式 */
  }
  100% {
    /* 结束状态样式 */
  }
}
```

### 2.3 animation属性

#### 语法结构

```css
animation: name duration timing-function delay iteration-count direction fill-mode;
```

#### 参数说明

- **name**: keyframes的名称
- **duration**: 动画持续时间，单位为秒(s)或毫秒(ms)
- **timing-function**: 动画的时间函数
- **delay**: 动画延迟时间
- **iteration-count**: 动画迭代次数（数值或infinite无限循环）
- **direction**: 动画方向（normal正常，reverse反向，alternate交替）
- **fill-mode**: 动画填充模式（forwards保持结束状态，backwards保持开始状态）

### 2.4 动画的应用场景

#### 示例1：简单的形状变化动画

```css
.box {
  width: 100px;
  height: 100px;
  background: #f40;
}

.box:hover {
  /* 动画名称: test, 时间: 2s, 无限循环, 缓动, 交替播放, 延迟1s */
  animation: test 2s infinite ease-in-out alternate 1s;
}

@keyframes test {
  0% {
    width: 100px;
    height: 100px;
    background: #f40;
  }
  50% {
    width: 200px;
    height: 100px;
    background: lightblue;
  }
  100% {
    width: 200px;
    height: 200px;
    background: #008c8c;
  }
}
```

#### 示例2：弹跳球动画

```css
.ball {
  width: 50px;
  height: 50px;
  background: #f40;
  margin: 0 auto;
  margin-top: 300px;
  border-radius: 50%;
  /* 应用弹跳动画 */
  animation: bounce 1s infinite;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-100px);
  }
}
```

#### 示例3：加载动画

```css
.loading {
  text-align: center;
  margin-top: 100px;
}

.dot {
  display: inline-block;
  width: 8px;
  height: 20px;
  background: lightgreen;
  /* 应用波浪动画 */
  animation: wave 1s ease-in-out infinite;
}

/* 为每个点添加不同的延迟，形成波浪效果 */
.dot:nth-child(2) {
  animation-delay: 0.1s;
}
.dot:nth-child(3) {
  animation-delay: 0.2s;
}
.dot:nth-child(4) {
  animation-delay: 0.3s;
}
.dot:nth-child(5) {
  animation-delay: 0.4s;
}

@keyframes wave {
  0%, 100% {
    transform: scaleY(1);
  }
  50% {
    transform: scaleY(2);
  }
}
```

#### 示例4：铃铛摇动动画

```css
.bell {
  width: 59px;
  height: 107px;
  background: url(./bell.png);
  margin: 0 auto;
  /* 应用摇动动画 */
  animation: ring 1s ease-in-out infinite;
  transform-origin: top center;
}

@keyframes ring {
  0% {
    transform: rotate(0deg);
  }
  10%, 30% {
    transform: rotate(-10deg);
  }
  20%, 40% {
    transform: rotate(10deg);
  }
  50% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(0deg);
  }
}
```

---

## 3. 过渡vs动画

### 3.1 使用场景对比

| 特性 | 过渡(Transition) | 动画(Animation) |
|------|------------------|-----------------|
| 触发方式 | 需要触发条件（如:hover） | 可以自动播放 |
| 复杂度 | 简单的A到B变化 | 可以创建复杂的多阶段效果 |
| 循环 | 不支持循环 | 支持循环播放 |
| 控制力 | 有限 | 高度可控 |
| 性能 | 较好 | 需要注意性能优化 |

### 3.2 选择建议

**使用过渡的场景：**
- 简单的状态变化（悬停效果）
- A到B的单一变化
- 用户交互反馈
- 性能要求高的场景

**使用动画的场景：**
- 复杂的多阶段效果
- 需要自动播放的动画
- 循环播放的效果
- 需要精细控制的动画

---

## 4. 性能优化建议

### 4.1 使用GPU加速

优先使用以下属性，它们可以触发GPU加速：
- `transform`
- `opacity`
- `filter`

避免使用以下属性，它们会触发重排：
- `width`
- `height`
- `left`
- `top`

### 4.2 使用will-change

```css
.element {
  /* 提前告知浏览器将要变化的属性 */
  will-change: transform, opacity;
}
```

### 4.3 减少重绘

- 使用`transform: translate()`代替改变`left/top`
- 使用`transform: scale()`代替改变`width/height`

---

## 5. 实践练习

### 练习1：图片悬停效果
- 为图片添加悬停时的放大效果
- 添加透明度变化
- 添加旋转效果

### 练习2：下拉菜单
- 创建下拉菜单结构
- 为菜单添加展开/收起动画
- 使用transform实现平滑效果

### 练习3：加载动画
- 创建多个点元素
- 使用animation创建波浪效果
- 为每个元素添加不同的延迟

### 练习4：弹跳球
- 创建球体元素
- 使用动画创建上下弹跳效果
- 调整缓动函数使效果更自然

### 练习5：铃铛摇动
- 使用图片作为铃铛
- 创建左右摇动的动画
- 使用多个关键帧创建摇动节奏

---

## 6. 常见问题

### Q1: 为什么我的过渡效果不生效？

**可能原因：**
1. 没有指定过渡属性：确保设置了`transition-property`
2. 属性不支持过渡：不是所有CSS属性都支持过渡
3. 开始和结束值相同：确保属性值有变化

### Q2: 如何让动画停在最后一帧？

**解决方案：**
使用`animation-fill-mode: forwards;`

```css
.element {
  animation: myAnimation 2s forwards;
}
```

### Q3: 动画性能不好怎么办？

**优化建议：**
1. 使用`transform`和`opacity`
2. 减少`box-shadow`等昂贵属性的使用
3. 使用`will-change`提示浏览器
4. 考虑使用CSS动画库

---

## 7. 浏览器兼容性

### 7.1 前缀支持

为了更好的浏览器兼容性，可能需要添加厂商前缀：

```css
.element {
  -webkit-transition: all 0.3s;
  -moz-transition: all 0.3s;
  -ms-transition: all 0.3s;
  -o-transition: all 0.3s;
  transition: all 0.3s;
}
```

### 7.2 现代浏览器支持

- Chrome 26+
- Firefox 16+
- Safari 6.1+
- IE 10+
- Edge所有版本

---

## 8. 总结

### 8.1 核心概念

1. **过渡(Transition)**：用于简单的A到B的状态变化
2. **动画(Animation)**：用于创建复杂的多阶段动画效果
3. **@keyframes**：定义动画的关键帧
4. **性能优化**：优先使用transform和opacity

### 8.2 最佳实践

1. 优先使用过渡处理简单的交互效果
2. 使用动画处理复杂的多阶段效果
3. 注意性能优化，避免触发重排
4. 合理使用缓动函数提升用户体验
5. 考虑浏览器兼容性，必要时添加前缀

### 8.3 下一步学习

- 学习CSS 3D变换
- 探索CSS动画库（如Animate.css）
- 了解Web Animations API
- 学习SVG动画

---

## 代码文件说明

本章节包含以下代码文件供学习参考：

### 示例文件
- `demo1.html` - 过渡效果基础示例
- `demo2.html` - 动画效果基础示例

### 练习文件
- `练习题/demo1.html` - 图片悬停效果练习
- `练习题/demo2.html` - 小球移动练习
- `练习题/demo3.html` - 下拉菜单动画练习
- `练习题/demo4.html` - 手风琴菜单练习
- `练习题/demo5.html` - 弹跳球动画练习
- `练习题/demo6.html` - 加载动画练习
- `练习题/demo7.html` - 铃铛摇动动画练习

### 参考答案
- `做题/` 目录下包含各练习题的参考代码
- `练习题/效果/` 目录下包含完成后的效果展示

建议先尝试自己完成练习，再参考答案进行对比学习。
