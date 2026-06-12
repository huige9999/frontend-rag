# CSS3布局

## 学习目标

- 掌握Flexbox弹性盒子的使用方法和常见布局场景
- 掌握Grid网格布局的使用方法和常见布局场景
- 理解两种布局方式的适用场景和区别

## 一、Flexbox弹性盒子布局

### 1.1 基本概念

Flexbox（弹性盒子）是CSS3中的一种一维布局模型，主要用于在一个方向上（行或列）对元素进行布局和对齐。

**核心概念：**
- 容器（Container）：采用flex布局的父元素
- 项目（Item）：容器内的直接子元素
- 主轴（Main Axis）：默认水平方向，从左到右
- 交叉轴（Cross Axis）：默认垂直方向，从上到下

### 1.2 容器属性

#### display: flex

启用弹性盒布局的最基本属性：

```css
.container {
  display: flex; /* 启用弹性盒布局 */
}
```

**示例代码：**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>Flexbox基础示例</title>
  <style>
    .container {
      width: 100%;
      height: 200px;
      background: rgb(238, 240, 119);
      display: flex; /* 启用弹性盒布局 */
    }
    .item {
      width: 50px;
      background: #f40;
      border: 1px solid #ccc;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <div class="item">5</div>
  </div>
</body>
</html>
```

#### justify-content - 主轴对齐

控制项目在主轴上的对齐方式：

```css
.container {
  justify-content: flex-start;    /* 默认值，从起点开始排列 */
  justify-content: flex-end;      /* 从终点开始排列 */
  justify-content: center;        /* 居中对齐 */
  justify-content: space-between; /* 两端对齐，项目之间间隔相等 */
  justify-content: space-around;  /* 项目两侧间隔相等 */
  justify-content: space-evenly;  /* 所有间隔完全相等 */
}
```

**示例：两端对齐**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>justify-content示例</title>
  <style>
    .container {
      width: 500px;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: flex;
      justify-content: space-between; /* 两端对齐 */
    }
    .item {
      width: 100px;
      height: 100px;
      background: #f19752;
      border: 2px solid #ee772f;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
  </div>
</body>
</html>
```

#### align-items - 交叉轴对齐

控制项目在交叉轴上的对齐方式：

```css
.container {
  align-items: stretch;    /* 默认值，拉伸填充 */
  align-items: flex-start;  /* 起点对齐 */
  align-items: flex-end;    /* 终点对齐 */
  align-items: center;      /* 居中对齐 */
  align-items: baseline;    /* 基线对齐 */
}
```

**示例：完全居中**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>align-items示例</title>
  <style>
    .container {
      width: 500px;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: flex;
      justify-content: center; /* 主轴居中 */
      align-items: center;     /* 交叉轴居中 */
    }
    .item {
      width: 100px;
      height: 100px;
      background: #f19752;
      border: 2px solid #ee772f;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item"></div>
  </div>
</body>
</html>
```

### 1.3 项目属性

#### flex-grow - 扩展比例

定义项目的扩展比例，默认为0（不扩展）：

```css
.item {
  flex-grow: 0; /* 不扩展，默认值 */
  flex-grow: 1; /* 扩展，占据剩余空间 */
}
```

**示例：单个项目扩展**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>flex-grow示例</title>
  <style>
    .container {
      width: 80%;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: flex;
    }
    .item {
      width: 100px;
      height: 100px;
      background: #f19752;
      border: 2px solid #ee772f;
    }
    .grow {
      flex-grow: 1; /* 占据剩余空间 */
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item"></div>
    <div class="item grow"></div> <!-- 这个会扩展 -->
    <div class="item"></div>
  </div>
</body>
</html>
```

#### flex 简写属性

`flex`是`flex-grow`、`flex-shrink`、`flex-basis`的简写：

```css
.item {
  flex: 1 1 auto; /* flex-grow: 1, flex-shrink: 1, flex-basis: auto */
  flex: 1 0 auto; /* flex-grow: 1, flex-shrink: 0, flex-basis: auto */
}
```

**示例：侧边栏布局**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>侧边栏布局</title>
  <style>
    .container {
      width: 80%;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: flex;
    }
    .aside {
      width: 250px;
      background: #f19752;
      border: 2px solid #ee772f;
    }
    .main {
      background: #ec5149;
      border: 2px solid #eb3223;
      flex: 1 1 auto; /* 自动占据剩余空间 */
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="aside"></div>
    <div class="main"></div>
  </div>
</body>
</html>
```

### 1.4 实际应用案例

#### 导航栏布局

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>导航栏布局</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    a {
      text-decoration: none;
      color: inherit;
    }
    .container {
      background: #000;
      color: #fff;
      display: flex;
      padding: 0 20px;
      height: 50px;
      line-height: 50px;
    }
    .nav {
      flex-grow: 1; /* 占据剩余空间 */
      margin-left: 20px;
      display: flex;
    }
    .nav a, .personal a {
      margin: 0 10px;
    }
    .active {
      color: #f8db38;
    }
    .personal {
      display: flex;
    }
  </style>
</head>
<body>
  <div class="container">
    <a href=""><h1>Logo</h1></a>
    <div class="nav">
      <a href="" class="active">首页</a>
      <a href="">新闻</a>
      <a href="">活动专区</a>
      <a href="">游戏数据库</a>
      <a href="">比赛</a>
    </div>
    <div class="personal">
      <a href="">登录</a>
      <a href="">注册</a>
    </div>
  </div>
</body>
</html>
```

#### 分页器布局

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>分页器布局</title>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .pager {
      display: flex;
      height: 60px;
      line-height: 60px;
      background: #ccc;
      justify-content: center;
    }
    .pager span {
      margin: 0 8px;
      color: #5d96ff;
    }
    .pager span.active {
      color: #fdd85c;
    }
  </style>
</head>
<body>
  <div class="pager">
    <span>1</span>
    <span class="active">2</span>
    <span>3</span>
    <span>4</span>
    <span>5</span>
    <span>6</span>
    <span>7</span>
    <span>8</span>
    <span>9</span>
    <span>10</span>
  </div>
</body>
</html>
```

#### 嵌套居中

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>嵌套居中</title>
  <style>
    .container, .inner, .item {
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .container {
      width: 80%;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
    }
    .inner {
      width: 80%;
      height: 80%;
      background: #f19752;
      border: 2px solid #ee772f;
    }
    .item {
      width: 50px;
      height: 50px;
      background: #ec5149;
      border: 2px solid #eb3223;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="inner">
      <div class="item">
        <div class="inner">
          <div class="item">
            <div class="inner">
              <div class="item"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</body>
</html>
```

#### 底部对齐

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>底部对齐</title>
  <style>
    .container {
      width: 80%;
      height: 500px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .inner {
      width: 80%;
      height: 80%;
      background: #f19752;
      border: 2px solid #ee772f;
      display: flex;
      align-items: flex-end; /* 底部对齐 */
    }
    .item {
      width: 50px;
      height: 50px;
      background: #ec5149;
      border: 2px solid #eb3223;
      flex-grow: 1;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="inner">
      <div class="item"></div>
      <div class="item"></div>
      <div class="item"></div>
    </div>
  </div>
</body>
</html>
```

## 二、Grid网格布局

### 2.1 基本概念

Grid（网格）布局是CSS3中的二维布局系统，可以同时处理行和列的布局，适合复杂的页面布局。

**核心概念：**
- 容器（Container）：采用grid布局的父元素
- 项目（Item）：容器内的直接子元素
- 网格线（Grid Line）：构成网格结构的分界线
- 网格轨道（Grid Track）：两条相邻网格线之间的空间
- 网格单元（Grid Cell）：最小的网格单位
- 网格区域（Grid Area）：由任意四条网格线组成的矩形区域

### 2.2 容器属性

#### display: grid

启用网格布局：

```css
.container {
  display: grid;
}
```

#### grid-template-columns 和 grid-template-rows

定义网格的列和行：

```css
.container {
  /* 固定尺寸 */
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px;
  
  /* 重复函数 */
  grid-template-columns: repeat(5, 100px); /* 重复5次，每次100px */
  grid-template-rows: repeat(4, 100px);
  
  /* 比例单位 */
  grid-template-columns: 1fr 2fr 1fr; /* 1:2:1比例 */
  grid-template-columns: repeat(4, 1fr); /* 等宽4列 */
}
```

**示例：基础网格**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>Grid基础示例</title>
  <style>
    .container {
      border: 1px solid #000;
      display: grid;
      grid-template-columns: repeat(5, 100px); /* 5列，每列100px */
      grid-template-rows: repeat(4, 100px);    /* 4行，每行100px */
      justify-items: center; /* 水平居中 */
      align-items: center;   /* 垂直居中 */
    }
    .item {
      background: #f40;
      border: 1px solid #ccc;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <div class="item">5</div>
    <!-- ... 更多项目 -->
  </div>
</body>
</html>
```

#### gap 网格间距

设置网格之间的间距：

```css
.container {
  gap: 10px 20px; /* 行间距10px，列间距20px */
  row-gap: 10px;  /* 行间距 */
  column-gap: 20px; /* 列间距 */
}
```

#### justify-items 和 align-items

控制项目在网格单元中的对齐方式：

```css
.container {
  justify-items: stretch | start | end | center; /* 水平对齐 */
  align-items: stretch | start | end | center;   /* 垂直对齐 */
}
```

### 2.3 项目属性

#### grid-area 网格区域

指定项目占据的网格区域：

```css
.item {
  grid-area: 行开始 / 列开始 / 行结束 / 列结束;
  /* 或使用命名区域 */
}
```

**示例：指定区域**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>grid-area示例</title>
  <style>
    .container {
      border: 1px solid #000;
      display: grid;
      grid-template-columns: repeat(5, 100px);
      grid-template-rows: repeat(5, 100px);
    }
    .item {
      background: #f40;
      border: 1px solid #ccc;
      grid-area: 2/3/4/5; /* 从第2行第3列开始，到第4行第5列结束 */
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">1</div>
  </div>
</body>
</html>
```

### 2.4 实际应用案例

#### 等宽网格布局

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>等宽网格布局</title>
  <style>
    .container {
      width: 500px;
      padding: 20px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: grid;
      grid-template-columns: repeat(4, 1fr); /* 4等宽列 */
      row-gap: 20px;
      justify-items: center;
    }
    .item {
      width: 100px;
      height: 100px;
      background: #f19752;
      border: 2px solid #ee772f;
      text-align: center;
      line-height: 100px;
      font-size: 2em;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <!-- ... 更多项目 -->
  </div>
</body>
</html>
```

#### 纵向网格布局

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>纵向网格布局</title>
  <style>
    .container {
      width: 500px;
      padding: 20px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: grid;
      grid-template-rows: repeat(13, 1fr); /* 13等高行 */
      grid-auto-flow: column; /* 自动填充到列 */
      row-gap: 20px;
      justify-items: center;
    }
    .item {
      width: 100px;
      height: 100px;
      background: #f19752;
      border: 2px solid #ee772f;
      text-align: center;
      line-height: 100px;
      font-size: 2em;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item">1</div>
    <div class="item">2</div>
    <!-- ... 30个项目 -->
  </div>
</body>
</html>
```

#### 复杂区域布局

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>复杂区域布局</title>
  <style>
    .container {
      width: 600px;
      height: 300px;
      padding: 20px;
      margin: 0 auto;
      border: 2px solid #ccc;
      display: grid;
      gap: 10px;
      grid-template-columns: repeat(5, 1fr); /* 5等宽列 */
      grid-template-rows: repeat(4, 1fr);    /* 4等高行 */
    }
    .item {
      background: #f19752;
      border: 2px solid #ee772f;
      text-align: center;
      line-height: 100px;
      font-size: 2em;
    }
    /* 不同项目占据不同区域 */
    .item1 {
      grid-area: 1/1/1/6; /* 第1行，从第1列到第6列（整行） */
    }
    .item2 {
      grid-area: 2/1/4/4; /* 第2-4行，第1-4列 */
    }
    .item3 {
      grid-area: 2/4/3/6; /* 第2-3行，第4-6列 */
    }
    .item4 {
      grid-area: 3/4/4/6; /* 第3-4行，第4-6列 */
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item item1"></div>
    <div class="item item2"></div>
    <div class="item item3"></div>
    <div class="item item4"></div>
    <div class="item item5"></div>
    <!-- ... 更多项目 -->
  </div>
</body>
</html>
```

## 三、Flexbox vs Grid

### 3.1 使用场景对比

| 特性 | Flexbox | Grid |
|------|---------|------|
| 维度 | 一维（行或列） | 二维（行和列） |
| 适用场景 | 组件内部布局、导航栏、工具栏 | 整体页面布局、复杂网格系统 |
| 对齐方式 | 主轴和交叉轴对齐 | 行和列独立对齐 |
| 浏览器支持 | 更好的浏览器支持 | 较新的浏览器支持 |

### 3.2 选择建议

**使用Flexbox的场景：**
- 导航栏和菜单
- 按钮组
- 表单元素对齐
- 组件内部布局
- 一维排列的内容

**使用Grid的场景：**
- 整体页面布局
- 复杂的二维网格系统
- 图片画廊
- 仪表盘布局
- 需要精确控制位置的场景

## 四、实战练习

### 4.1 Flexbox练习

1. 创建一个响应式导航栏
2. 实现卡片组件的居中对齐
3. 制作一个三栏布局（侧边栏+主内容+侧边栏）
4. 实现登录表单的垂直居中

### 4.2 Grid练习

1. 创建一个响应式的图片画廊
2. 实现杂志风格的布局
3. 制作一个仪表盘界面
4. 实现复杂的拼图式布局

## 五、总结

CSS3的Flexbox和Grid布局为我们提供了强大的布局能力：

1. **Flexbox**：适合一维布局，简单直观，适合组件级别的布局
2. **Grid**：适合二维布局，功能强大，适合页面级别的复杂布局
3. **结合使用**：可以混合使用Flexbox和Grid，发挥各自优势
4. **响应式设计**：两种布局方式都很好地支持响应式设计

掌握这些现代布局技术，可以让我们的页面布局更加灵活、简洁和易于维护。

## 六、参考资料

- [Flexbox指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Grid布局指南](https://css-tricks.com/snippets/css/complete-guide-grid/)
- [MDN Flexbox文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout)
- [MDN Grid文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)
