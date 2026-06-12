# CSS3视觉

> CSS3视觉样式主要是指不影响盒子位置、尺寸的样式，例如文字颜色、背景颜色、背景图片、阴影、圆角、变形等。

## 学习目标

- 掌握CSS3阴影效果（盒子阴影、文字阴影）
- 掌握CSS3圆角边框
- 掌握CSS3背景渐变
- 掌握CSS3变形效果（平移、缩放、旋转）
- 掌握CSS3过渡和动画
- 了解其他CSS3新特性

## 1. 阴影效果

### 1.1 盒子阴影 (box-shadow)

通过`box-shadow`属性可以设置整个盒子的阴影。

**语法：**
```css
box-shadow: 水平偏移 垂直偏移 模糊半径 扩展半径 颜色;
```

**参数说明：**
- 水平偏移：必需，正数向右，负数向左
- 垂直偏移：必需，正数向下，负数向上
- 模糊半径：可选，值越大越模糊
- 扩展半径：可选，阴影的扩展大小
- 颜色：可选，阴影的颜色

**示例代码：**
```css
/* 基础阴影 */
.box {
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
}

/* 多重阴影 */
.box {
  box-shadow: 0 0 5px 10px #fff, 0 0 30px 15px rgb(0, 0, 0);
}

/* 内阴影 */
.box {
  box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.3);
}
```

**课堂练习 - Demo1：**
为透明盒子添加阴影效果

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>盒子阴影练习</title>
  <style>
    .box {
      width: 200px;
      height: 200px;
      margin: 1em auto;
      padding: 30px;
      /* 添加阴影效果 */
      box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
      border-radius: 10px;
    }
  </style>
</head>
<body>
  <div class="box">这是一个背景透明的盒子，请给它四周加上阴影</div>
</body>
</html>
```

### 1.2 文字阴影 (text-shadow)

通过`text-shadow`可以设置文字阴影效果。

**语法：**
```css
text-shadow: 水平偏移 垂直偏移 模糊半径 颜色;
```

**示例代码：**
```css
h1 {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

/* 立体文字效果 */
.title {
  text-shadow: 1px 1px 0 #fff, -1px -1px 0 #000;
}
```

## 2. 圆角边框 (border-radius)

通过设置`border-radius`，可以设置盒子的圆角效果。

**语法：**
```css
border-radius: 圆角半径;
```

**常用值：**
```css
/* 设置所有角的圆角，半径为10px */
border-radius: 10px;

/* 设置圆形：圆的横向半径为宽度一半，纵向半径为高度一半 */
border-radius: 50%;

/* 分别设置左上、右上、右下、左下的圆角 */
border-radius: 10px 20px 30px 40px;

/* 简写：左上右下10px，右上左下20px */
border-radius: 10px 20px;

/* 对角线设置：左上右下10px，右上左下20px 30px */
border-radius: 10px 20px 30px;

/* 单独设置每个角 */
border-top-left-radius: 10px;
border-top-right-radius: 20px;
border-bottom-right-radius: 30px;
border-bottom-left-radius: 40px;
```

**课堂练习 - Demo2：**
将图片转换为圆形

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>圆角练习</title>
  <style>
    .box {
      width: 200px;
      height: 200px;
      margin: 1em auto;
      display: block;
      /* 设置圆形 */
      border-radius: 50%;
    }
  </style>
</head>
<body>
  <img class="box" src="doge.jpeg" alt="doge" />
</body>
</html>
```

**课堂练习 - Demo3：**
为圆形图片添加双重阴影效果

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>圆角阴影练习</title>
  <style>
    .box {
      width: 200px;
      height: 200px;
      margin: 1em auto;
      display: block;
      border-radius: 50%;
      /* 双重阴影效果 */
      box-shadow: 0 0 5px 10px #fff, 0 0 30px 15px rgb(0, 0, 0);
    }
  </style>
</head>
<body>
  <img class="box" src="doge.jpeg" alt="doge" />
</body>
</html>
```

## 3. 背景渐变

### 3.1 线性渐变 (linear-gradient)

在设置背景图片时，除了可以使用`url()`加载背景图，还可以使用`linear-gradient()`设置背景渐变。

**语法：**
```css
background: linear-gradient(方向, 颜色1, 颜色2, ...);
```

**方向值：**
- `to bottom`：从上到下（默认）
- `to top`：从下到上
- `to right`：从左到右
- `to left`：从右到左
- `to bottom right`：从左上到右下
- `45deg`：45度角

**示例代码：**
```css
/* 从上到下的渐变 */
background: linear-gradient(to bottom, #e66465, #9198e5);

/* 从左到右的渐变 */
background: linear-gradient(to right, #e66465, #9198e5);

/* 多色渐变 */
background: linear-gradient(to bottom, #e66465, #9198e5, #4293fc);

/* 指定角度 */
background: linear-gradient(45deg, #e66465, #9198e5);
```

**课堂练习 - Demo4：**
制作渐变按钮

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>渐变按钮练习</title>
  <style>
    body {
      text-align: center;
      color: #666;
    }
    .btn {
      outline: none;
      border: none;
      padding: 10px 20px;
      margin-top: 50px;
      /* 渐变背景 */
      background: linear-gradient(to bottom, #acd4ff, #4293fc);
      color: #fff;
      border-radius: 5px;
      cursor: pointer;
      transition: all 0.3s;
    }
    .btn:active {
      background: #4293fc;
      /* 点击时的双重边框效果 */
      box-shadow: 0 0 0 3px #fff, 0 0 0 5px #4293fc;
    }
  </style>
</head>
<body>
  <button class="btn">Click Me</button>
  <p>渐变颜色： #acd4ff -> #4293fc</p>
  <p>点击按钮看看效果</p>
</body>
</html>
```

### 3.2 径向渐变 (radial-gradient)

```css
/* 从中心向外的径向渐变 */
background: radial-gradient(circle, #e66465, #9198e5);

/* 椭圆形径向渐变 */
background: radial-gradient(ellipse, #e66465, #9198e5);
```

## 4. 变形效果 (transform)

通过`transform`属性，可以使盒子的形态发生变化。

**重要特性：**
- 变形只是视觉效果的变化，不会影响盒子的布局
- transform不会导致浏览器reflow和rerender，因此效率极高

### 4.1 平移 (translate)

使用`translate`可以让盒子在原来位置上产生位移，类似于相对定位。

**语法：**
```css
transform: translate(X, Y);
/* 或单独设置 */
transform: translateX(值);
transform: translateY(值);
```

**示例代码：**
```css
/* 向右平移100px，向下平移50px */
transform: translate(100px, 50px);

/* 只设置X轴平移 */
transform: translateX(100px);

/* 百分比值相对于自身 */
transform: translate(-50%, -50%); /* 居中定位常用技巧 */
```

**课堂练习 - Demo6：**
使用transform实现元素居中

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>平移练习</title>
  <style>
    .box {
      padding: 40px;
      background: #fcf0f0;
      position: fixed;
      /* 使用translate实现居中 */
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }
  </style>
</head>
<body>
  <div class="box">
    <p>这是一个固定定位的盒子</p>
    <p>它的宽高是不固定的</p>
    <p>使用transform: translate(-50%, -50%)实现完美居中</p>
  </div>
</body>
</html>
```

### 4.2 缩放 (scale)

使用`scale`可以让盒子基于原来的尺寸发生缩放。

**语法：**
```css
transform: scale(X, Y);
/* 或单独设置 */
transform: scaleX(值);
transform: scaleY(值);
```

**示例代码：**
```css
/* 放大1.5倍 */
transform: scale(1.5);

/* 水平放大2倍，垂直缩小0.5倍 */
transform: scale(2, 0.5);

/* 只设置X轴缩放 */
transform: scaleX(1.5);

/* 值为1表示不缩放，小于1缩小，大于1放大 */
transform: scale(0.8); /* 缩小到80% */
```

### 4.3 旋转 (rotate)

使用`rotate`属性可以在原图基础上进行旋转。

**语法：**
```css
transform: rotate(角度);
```

**角度单位：**
- `deg`：度数
- `turn`：圈数（0.5turn = 180deg）

**示例代码：**
```css
/* 顺时针旋转45度 */
transform: rotate(45deg);

/* 顺时针旋转半圈 */
transform: rotate(0.5turn);

/* 逆时针旋转30度 */
transform: rotate(-30deg);
```

**课堂练习 - Demo5：**
旋转盒子

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>旋转练习</title>
  <style>
    .box {
      width: 200px;
      padding: 20px;
      margin: 50px auto;
      background: #fcf0f0;
      box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
      border-radius: 0 30px 0 0;
      /* 旋转10度 */
      transform: rotate(10deg);
      transition: transform 0.3s;
    }
    .box:hover {
      transform: rotate(0deg); /* 鼠标悬停时恢复 */
    }
  </style>
</head>
<body>
  <div class="box">
    <p>这是一个旋转的盒子</p>
  </div>
</body>
</html>
```

**课堂练习 - Demo7：**
使用border制作三角形并旋转

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>三角形旋转练习</title>
  <style>
    body {
      text-align: center;
      color: #666;
    }
    .box {
      margin: 0 auto;
      /* 使用边框制作三角形 */
      border: 100px solid;
      width: 0;
      height: 0;
      border-color: transparent transparent #579ef8 transparent;
      /* 设置变形原点 */
      transform-origin: center 150px;
      transition: transform 0.5s;
    }
    .box:hover {
      /* 鼠标悬停时旋转半圈 */
      transform: rotate(0.5turn);
    }
  </style>
</head>
<body>
  <div class="box"></div>
  <p>鼠标移动上去试一试</p>
  <p>颜色值：#579ef8</p>
</body>
</html>
```

### 4.4 改变变形原点 (transform-origin)

变形原点的位置会影响具体的变形行为。默认情况下，变形的原点在盒子中心。

**语法：**
```css
transform-origin: X Y;
```

**常用值：**
```css
transform-origin: center;          /* 默认值，盒子中心 */
transform-origin: left top;       /* 左上角 */
transform-origin: right bottom;   /* 右下角 */
transform-origin: 30px 60px;      /* 自定义坐标位置 */
transform-origin: 50% 50%;         /* 百分比位置 */
```

### 4.5 多重变形

可以一次性设置多种变形效果，注意顺序会影响最终结果。

**示例代码：**
```css
/* 先旋转45度，再平移(100,100) */
transform: rotate(45deg) translate(100px, 100px);

/* 先平移(100, 100)，再旋转45度 */
transform: translate(100px, 100px) rotate(45deg);

/* 先平移，再缩放，最后旋转 */
transform: translate(50px, 50px) scale(1.2) rotate(30deg);
```

**注意事项：**
- 旋转会导致坐标系也跟着旋转，从而影响后续的变形效果
- 变形的顺序很重要，不同的顺序会产生不同的结果

## 5. 过渡效果 (transition)

使用过渡，可以让CSS属性变化更加丝滑。

**重要：**
- 过渡无法对所有的CSS属性产生影响
- 能够产生影响的只有数值类属性，例如：颜色、宽高、字体大小等

**语法：**
```css
transition: 过渡属性 持续时间 过渡函数 过渡延迟;
```

**参数说明：**

1. **过渡属性**：针对哪个CSS属性应用过渡
   - 具体属性名：如`transform`、`background`、`color`等
   - `all`或不填：针对所有CSS属性

2. **持续时间**：CSS属性变化所持续的时间
   - 需要带上单位：`3s`表示3秒
   - `0.5s`或`500ms`均表示500毫秒

3. **过渡函数**：CSS属性变化的贝塞尔曲线函数
   - `ease`：默认值，平滑开始和结束
   - `ease-in-out`：平滑开始，平滑结束
   - `linear`：线性变化
   - `ease-in`：仅平滑开始
   - `ease-out`：仅平滑结束

4. **过渡延迟**：过渡效果延迟多久后触发
   - 书写规则和持续时间一样
   - 不填则无延迟

**示例代码：**
```css
.box {
  width: 100px;
  height: 100px;
  background: #f00;
  /* 设置过渡效果 */
  transition: all 0.3s ease-in-out;
}

.box:hover {
  width: 200px;
  background: #00f;
}

/* 针对特定属性设置过渡 */
.button {
  transition: background 0.3s, transform 0.2s;
}

/* 设置延迟 */
.element {
  transition: all 0.5s 0.2s; /* 持续0.5秒，延迟0.2秒开始 */
}
```

## 6. 动画 (animation)

动画的本质是预先定义的一套CSS变化规则（关键帧），然后给该规则取个名字，让元素使用这个规则。

### 6.1 定义动画规则

使用`@keyframes`定义动画规则：

```css
@keyframes 动画名称 {
  /* 关键帧定义 */
  0% {
    /* 初始状态 */
  }
  50% {
    /* 中间状态 */
  }
  100% {
    /* 结束状态 */
  }
}

/* 或使用from/to */
@keyframes 动画名称 {
  from {
    /* 初始状态 */
  }
  to {
    /* 结束状态 */
  }
}
```

### 6.2 应用动画

**语法：**
```css
animation: 规则名 持续时间 重复次数 时间函数 动画方向 延迟时间;
```

**参数说明：**

1. **规则名**：定义的@keyframes动画名称
2. **持续时间**：动画完成一次所需时间
3. **重复次数**：
   - 数字：重复次数
   - `infinite`：无限重复
4. **时间函数**：类似transition的过渡函数
5. **动画方向**：
   - `normal`：默认，正向播放
   - `reverse`：反向播放
   - `alternate`：交替反向（第1次正向，第2次反向，以此类推）
   - `alternate-reverse`：反向交替
6. **延迟时间**：动画延迟多久开始

**示例代码：**
```css
/* 定义动画 */
@keyframes move {
  0% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(200px);
  }
  100% {
    transform: translateX(0);
  }
}

/* 应用动画 */
.box {
  animation: move 2s infinite ease-in-out;
}

/* 更完整的示例 */
.element {
  animation: rotate 3s infinite linear 1s;
}
```

### 6.3 动画相关属性

```css
/* 分别设置各个属性 */
.animation-box {
  animation-name: myAnimation;           /* 动画名称 */
  animation-duration: 3s;                /* 持续时间 */
  animation-timing-function: ease-in-out; /* 时间函数 */
  animation-delay: 1s;                   /* 延迟时间 */
  animation-iteration-count: infinite;  /* 重复次数 */
  animation-direction: alternate;        /* 动画方向 */
  animation-fill-mode: forwards;         /* 动画结束后状态 */
  animation-play-state: running;         /* 播放状态 */
}

/* 暂停动画 */
.animation-box:hover {
  animation-play-state: paused;
}
```

### 6.4 JavaScript事件监听

在JS中，可以监听元素的动画事件：

```javascript
element.addEventListener('animationstart', function() {
  console.log('动画开始');
});

element.addEventListener('animationend', function() {
  console.log('动画结束');
});

element.addEventListener('animationiteration', function() {
  console.log('动画重复');
});
```

## 7. 其他CSS3新特性

### 7.1 box-sizing

控制盒模型的计算方式。

**一图胜千言：**

`content-box`（默认值）：
- width设置的是内容区域的宽度
- padding和border会额外增加盒子的总宽度

`border-box`：
- width设置的是盒子的总宽度
- padding和border会包含在width内

**示例代码：**
```css
/* 使用border-box控制尺寸更加直观 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* 或单独设置 */
.container {
  box-sizing: border-box;
}
```

### 7.2 字体图标 (@font-face)

CSS3新增了`@font-face`指令，可以加载网络字体。最常见的应用就是字体图标。

**特点：**
- 字体图标本质上是文字
- 通过`color`设置颜色
- 通过`font-size`设置尺寸
- 可以像文字一样使用CSS样式

**示例代码：**
```css
/* 定义字体 */
@font-face {
  font-family: 'iconfont';
  src: url('iconfont.ttf') format('truetype');
}

/* 使用字体图标 */
.icon {
  font-family: 'iconfont';
  font-size: 20px;
  color: #333;
}
```

**国内常用字体图标平台：**
- [阿里巴巴矢量图标库](https://www.iconfont.cn/)
- [Font Awesome](https://fontawesome.com/)

### 7.3 图像内容适应 (object-fit)

CSS3属性`object-fit`可以控制多媒体内容与元素的适应方式，通常应用在`img`或`video`元素中。

**常用值：**
```css
/* 保持比例，可能留有空白 */
object-fit: contain;

/* 保持比例，裁剪以填充 */
object-fit: cover;

/* 拉伸填充 */
object-fit: fill;

/* 无调整 */
object-fit: none;

/* 示例 */
img {
  width: 200px;
  height: 200px;
  object-fit: cover; /* 保持比例并填充整个区域 */
}
```

### 7.4 视口单位 (vw/vh)

CSS3支持使用`vw`和`vh`作为单位，分别表示`视口宽度`和`视口高度`。

**示例代码：**
```css
/* 1vh表示视口高度的1% */
height: 50vh; /* 视口高度的一半 */

/* 100vw表示视口宽度的100% */
width: 100vw; /* 视口宽度 */

/* vmin和vmax */
font-size: 10vmin; /* 视口宽高中较小值的10% */
width: 80vmax; /* 视口宽高中较大值的80% */
```

### 7.5 伪元素 (::before, ::after)

通过`::before`和`::after`选择器，可以通过CSS给元素生成两个子元素。

**示例代码：**
```css
/* 必须要有content属性 */
.box::before {
  content: '';
  display: block;
  width: 10px;
  height: 10px;
  background: #f00;
}

.box::after {
  content: '结尾内容';
  color: #333;
}
```

**重要：**
- 伪元素必须要有`content`属性，否则不能生效
- 如果不需要有元素内容，设置`content: ''`
- 使用伪元素可以避免在HTML中使用过多的空元素

### 7.6 平滑滚动 (scroll-behavior)

使用`scroll-behavior: smooth`，可以让页面滚动更加丝滑。

**示例代码：**
```css
/* 页面级别平滑滚动 */
html {
  scroll-behavior: smooth;
}

/* 容器级别平滑滚动 */
.container {
  height: 300px;
  overflow-y: auto;
  scroll-behavior: smooth;
}
```

## 8. 综合练习

### 8.1 卡片悬停效果

结合阴影、变形、过渡实现卡片悬停效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>卡片悬停效果</title>
  <style>
    .card {
      width: 300px;
      padding: 20px;
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
      transition: all 0.3s ease;
    }
    
    .card:hover {
      transform: translateY(-10px);
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    }
  </style>
</head>
<body>
  <div class="card">
    <h3>卡片标题</h3>
    <p>卡片内容，鼠标悬停查看效果</p>
  </div>
</body>
</html>
```

### 8.2 加载动画

使用CSS动画实现简单的加载效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>加载动画</title>
  <style>
    .loader {
      width: 50px;
      height: 50px;
      border: 5px solid #f3f3f3;
      border-top: 5px solid #3498db;
      border-radius: 50%;
      animation: spin 1s linear infinite;
    }
    
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
  </style>
</head>
<body>
  <div class="loader"></div>
</body>
</html>
```

### 8.3 渐变背景动画

结合渐变和动画创建动态背景：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>渐变动画</title>
  <style>
    .gradient-bg {
      width: 100%;
      height: 200px;
      background: linear-gradient(45deg, #ff6b6b, #4ecdc4, #45b7d1, #96ceb4);
      background-size: 400% 400%;
      animation: gradient 15s ease infinite;
    }
    
    @keyframes gradient {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }
  </style>
</head>
<body>
  <div class="gradient-bg"></div>
</body>
</html>
```

## 9. 学习资源

### 官方文档
- [MDN CSS文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS)
- [Can I Use](https://caniuse.com/) - 浏览器兼容性查询

### 在线工具
- [CSS Gradient Generator](https://cssgradient.io/) - 渐变生成器
- [Box Shadow Generator](https://cssgenerator.org/box-shadow-css-generator.html) - 阴影生成器
- [Border Radius Generator](https://border-radius.com/) - 圆角生成器
- [Cubic-bezier](https://cubic-bezier.com/) - 贝塞尔曲线调节工具

### 练习游戏
- [Flexbox Froggy](https://flexboxfroggy.com/) - 弹性盒学习游戏
- [Grid Garden](https://cssgridgarden.com/) - 网格布局学习游戏

## 10. 课后练习

### 练习1：个人名片
创建一个个人名片，包含：
- 圆角边框
- 阴影效果
- 悬停时的变形动画
- 渐变背景

### 练习2：导航菜单
制作一个响应式导航菜单，包含：
- 背景渐变
- 菜单项悬停效果
- 平滑的过渡动画

### 练习3：图片画廊
创建一个图片画廊，包含：
- 图片圆角
- 悬停时的阴影和缩放效果
- 图片使用object-fit适应容器

### 练习4：加载动画
设计一个独特的加载动画，使用：
- CSS关键帧动画
- 旋转、缩放等变形效果
- 无限循环播放

### 练习5：按钮样式组
制作一组不同风格的按钮，包含：
- 渐变按钮
- 幽灵按钮
- 3D按钮
- 动画按钮

## 11. 常见问题

### Q1: transform和position的区别是什么？
**A:** 
- `transform`只是视觉效果，不会影响布局，不会导致reflow，性能更好
- `position`会影响文档流，会影响其他元素的位置

### Q2: 过渡和动画什么时候使用？
**A:**
- **过渡**：用于简单的状态变化，如hover效果
- **动画**：用于复杂的多步骤动画效果

### Q3: 为什么设置了transition没有效果？
**A:**
- 确保属性值发生了变化
- 只对数值类属性有效
- 确保设置了合适的持续时间

### Q4: 如何优化动画性能？
**A:**
- 尽量使用transform和opacity
- 避免使用width、height等会触发reflow的属性
- 使用will-change提示浏览器优化

## 12. 总结

CSS3视觉特效为网页设计提供了丰富的表现力：

1. **阴影和圆角**：增强元素的立体感和美观度
2. **渐变背景**：创造丰富多彩的视觉效果
3. **变形效果**：实现各种动画和交互效果
4. **过渡和动画**：让页面变化更加流畅自然
5. **新特性**：提供更多便利的工具和属性

**重要提示：**
- 使用CSS3特效时要注意浏览器兼容性
- 过度使用会影响页面性能
- 合理使用特效可以提升用户体验

**下一步学习：**
- CSS预处理器（Sass、Less）
- CSS框架（Bootstrap、Tailwind）
- JavaScript动画库（GSAP、Anime.js）
