# 06-07 ECharts 数据可视化

## 章节概述

ECharts 是一个由百度开源的、基于 JavaScript 的数据可视化图表库，可以流畅的运行在 PC 和移动设备上，兼容当前绝大部分浏览器。本章节将介绍 ECharts 的基础用法和常见图表类型。

## 学习目标

- 理解 ECharts 的基本概念和核心配置
- 掌握常见图表类型的使用方法
- 学会处理远程数据和地图数据

## ECharts 简介

### 什么是 ECharts

ECharts（Enterprise Charts）是一个功能强大的数据可视化库，具有以下特点：

- **丰富的图表类型**：支持折线图、柱状图、饼图、散点图、K线图、地图等多种图表
- **交互性强**：支持数据缩放、拖拽、数据区域选择等交互功能
- **移动端适配**：完美支持移动端触摸操作
- **高性能渲染**：基于 Canvas 渲染，支持千万级数据量的渲染
- **灵活的配置**：提供详细的配置项，可满足各种定制需求

### 引入方式

#### CDN 引入

```html
<!-- 使用 ECharts 5.x 版本 -->
<script src="https://cdn.bootcdn.net/ajax/libs/echarts/5.1.1/echarts.min.js"></script>
```

#### NPM 安装

```bash
npm install echarts --save
```

```javascript
// 在 ES6 模块中引入
import * as echarts from 'echarts';
```

## ECharts 基础用法

### 初始化图表

```javascript
// 1. 获取 DOM 容器
const domElement = document.querySelector('.chart-container');

// 2. 初始化 ECharts 实例
const myChart = echarts.init(domElement);

// 3. 设置图表配置项
myChart.setOption({
  // 配置项将在后面详细讲解
});

// 4. 响应式处理（可选）
window.addEventListener('resize', function() {
  myChart.resize();
});
```

### 核心配置结构

ECharts 的配置项是一个对象，主要包含以下部分：

- **title**：图表标题配置
- **tooltip**：提示框配置
- **legend**：图例配置
- **xAxis**：X 轴配置
- **yAxis**：Y 轴配置
- **series**：系列列表，每个系列通过 type 决定图表类型
- **其他配置**：grid、dataZoom、toolbox 等

## 常见图表类型

### 1. 折线图（Line Chart）

折线图用于显示数据在一个连续时间间隔或时间跨度上的变化趋势。

```javascript
const myChart = echarts.init(getDom(0)); // 初始化，获得echart实例

// 给当前图表实例添加配置
myChart.setOption({
  title: {
    text: 'ECharts 折线图', // 图表标题
  },
  tooltip: {}, // 配置了该项，则鼠标移动上去后会有提示
  legend: {
    // 配置图例
    data: ['手机销量', '平板销量'],
  },
  xAxis: {
    // 配置横坐标
    data: Array(12)
      .fill(0)
      .map((it, i) => `${i + 1}月`),
  },
  yAxis: {}, // 纵坐标让其自动生成
  series: [
    {
      name: '手机销量', // 对应到图例
      type: 'line', // 类型：线图
      // 配置数据
      data: Array(12)
        .fill(0)
        .map(() => Math.floor(Math.random() * 1000 + 800)),
      // smooth: true, // 添加此项可以让曲线变得平滑
    },
    {
      name: '平板销量', // 对应到图例
      type: 'line', // 类型：线图
      // 配置数据
      data: Array(12)
        .fill(0)
        .map(() => Math.floor(Math.random() * 1000 + 800)),
      smooth: true, // 添加此项可以让曲线变得平滑
    },
  ],
});
```

**折线图特点**：
- 适合显示时间序列数据
- 可以显示多个系列进行对比
- 支持平滑曲线（smooth 属性）
- 支持数据点标记和区域填充

### 2. 柱状图（Bar Chart）

柱状图通过柱状的高度来表现数据的大小，用于比较不同类别之间的数据。

```javascript
const myChart = echarts.init(getDom(1)); // 初始化，获得echart实例

// 给当前图表实例添加配置
myChart.setOption({
  title: {
    text: 'ECharts 柱状图', // 图表标题
  },
  tooltip: {}, // 配置了该项，则鼠标移动上去后会有提示
  legend: {
    // 配置图例
    data: ['手机销量', '平板销量'],
  },
  xAxis: {
    // 配置横坐标
    data: Array(12)
      .fill(0)
      .map((it, i) => `${i + 1}月`),
  },
  yAxis: {}, // 纵坐标让其自动生成
  series: [
    {
      name: '手机销量', // 对应到图例
      type: 'bar', // 类型：柱状图
      // 配置数据
      data: Array(12)
        .fill(0)
        .map(() => Math.floor(Math.random() * 1000 + 800)),
    },
    {
      name: '平板销量', // 对应到图例
      type: 'bar', // 类型：柱状图
      // 配置数据
      data: Array(12)
        .fill(0)
        .map(() => Math.floor(Math.random() * 1000 + 800)),
    },
  ],
});
```

**柱状图特点**：
- 适合分类数据对比
- 支持横向和纵向显示
- 可以堆叠显示（stack 属性）
- 支持自定义柱状样式

### 3. 饼图（Pie Chart）

饼图用于显示不同类别在整体中的占比情况。

```javascript
const myChart = echarts.init(getDom(2)); // 初始化，获得echart实例

// 给当前图表实例添加配置
myChart.setOption({
  title: {
    text: 'ECharts 饼图', // 图表标题
  },
  tooltip: {}, // 配置了该项，则鼠标移动上去后会有提示
  series: [
    {
      name: '访问来源', // 图表名称
      type: 'pie', // 图标类型：饼图
      radius: '50%', // 圆半径：最大半径的50%
      roseType: 'radius', // 南丁格尔图的类型，若不设置此项，则为普通饼图
      data: [
        // 饼图数据
        { value: 235, name: '视频广告' },
        { value: 274, name: '联盟广告' },
        { value: 310, name: '邮件营销' },
        { value: 335, name: '直接访问' },
        { value: 400, name: '搜索引擎' },
      ],
    },
  ],
});
```

**饼图特点**：
- 适合展示占比数据
- 支持环形图（radius 设置为数组）
- 支持南丁格尔图（roseType 属性）
- 可以高亮选中扇区

### 4. K线图（Candlestick Chart）

K线图（蜡烛图）主要用于金融领域，显示股票、期货等金融产品的价格变动。

```javascript
const myChart = echarts.init(getDom(3)); // 初始化，获得echart实例

// 给当前图表实例添加配置
myChart.setOption({
  title: {
    text: 'ECharts K线图（蜡烛图）', // 图表标题
  },
  xAxis: {
    // 配置横坐标
    data: Array(12)
      .fill(0)
      .map((it, i) => `${i + 1}月`),
  },
  yAxis: {}, // 纵坐标让其自动生成
  tooltip: {}, // 配置了该项，则鼠标移动上去后会有提示
  series: [
    {
      type: 'k', // 图表类型：k线图
      data: [
        // 每个数组为一根k线，数值含义为[开盘价, 收盘价, 最高价, 最低价]
        [20, 34, 38, 10],
        [34, 35, 50, 30],
        [35, 38, 44, 33],
        [38, 33, 40, 30],
        [33, 27, 32, 22],
        [27, 26, 29, 22],
        [26, 27, 28, 25],
        [27, 33, 34, 32],
        [33, 37, 44, 32],
        [37, 30, 39, 28],
        [30, 26, 33, 22],
        [26, 18, 28, 13],
      ],
      encode: {
        // 设置提示对应的dimensions索引
        tooltip: [1, 2, 3, 4],
      },
      // 设置五个维度的名称
      // 含义分别为：日期、开盘价, 收盘价, 最高价, 最低价
      dimensions: [null, '开盘价', '收盘价', '最高价', '最低价'],
    },
  ],
});
```

**K线图特点**：
- 专门用于金融数据展示
- 需要四维数据：[开盘价, 收盘价, 最高价, 最低价]
- 红色表示上涨，绿色表示下跌
- 支持与均线等技术指标结合

## 高级功能

### 1. 远程数据加载

在实际应用中，图表数据通常需要从服务器获取。

```javascript
// 模拟远程数据
Mock.mock('/api/pie-datas', 'get', {
  datas: [
    { value: 235, name: '视频广告' },
    { value: 274, name: '联盟广告' },
    { value: 310, name: '邮件营销' },
    { value: 335, name: '直接访问' },
    { value: 400, name: '搜索引擎' },
  ],
});
Mock.setup({
  timeout: 2000, // 模拟2秒钟网络通信
});

const myChart = echarts.init(getDom(4)); // 初始化，获得echart实例

// 先设置标题
myChart.setOption({
  title: {
    text: 'ECharts 饼图，远程数据加载中...', // 图表标题
  },
});

myChart.showLoading(); // 显示加载效果

// 获取远程数据
const resp = await axios.get('/api/pie-datas');

// 给当前图表实例添加配置
myChart.setOption({
  title: {
    text: 'ECharts 饼图', // 图表标题
  },
  tooltip: {}, // 配置了该项，则鼠标移动上去后会有提示
  series: [
    {
      name: '访问来源', // 图标名称
      type: 'pie', // 图标类型：饼图
      radius: '50%', // 圆半径：最大半径的50%
      roseType: 'radius', // 南丁格尔图的类型，若不设置此项，则为普通饼图
      data: resp.data.datas, // 数据来自于服务器响应
    },
  ],
});

// 隐藏加载效果
myChart.hideLoading();
```

**远程数据加载要点**：
- 使用 `showLoading()` 显示加载动画
- 异步获取数据后更新图表配置
- 使用 `hideLoading()` 隐藏加载动画
- 可以结合数据模拟工具进行开发测试

### 2. 地图数据可视化

ECharts 支持地图数据可视化，需要两部分数据：
1. GEOJSON 格式的地理信息（地图背景板）
2. 在地图上要显示或交互的数据

```javascript
/**
 * 使用地图需要两部分数据：
 * 1. GEOJSON格式的地理信息，该信息将作为地图背景板
 * 2. 在地图背景板上要进行显示或交互的数据
 */
const myChart = echarts.init(getDom(5)); // 初始化，获得echart实例

myChart.showLoading();

// 获取中国GEOJSON数据和用户数据
const resp = await axios.get('./china.geojson.json');
const users = await axios.get('./user.json');

// 注册地图数据
echarts.registerMap('China', resp.data);

myChart.setOption({
  title: {
    text: 'ECharts 用户地图', // 图表标题
  },
  // 配置了该项，则鼠标移动上去后会有提示
  // formatter 为文字提示格式，在此例中，{b}表示数据名，{c}表示数据值
  tooltip: { formatter: '{b} 注册用户 {c}人' },
  visualMap: {
    // 虚拟地图，一般用户设置不同颜色来展现数据的差异
    left: 'right', // 虚拟地图显示的位置
    min: 0, // 区间的最小值
    max: 10000, // 区间数据的最大值
    text: ['高', '低'], // 文本，默认为数值文本
    calculable: true, // 是否允许控制区间
  },
  series: [
    {
      type: 'map', // 图表类型：地图
      map: 'China', // 使用注册的地图
      roam: true, // 是否开启鼠标缩放和平移漫游
      scaleLimit: {
        // 缩放比例控制
        min: 0.7, // 最小缩放到0.7倍
        max: 3, // 最大缩放到3倍
      },
      data: users.data,
    },
  ],
});

myChart.hideLoading();
```

**用户数据示例格式**：
```json
[
  { "name": "北京市", "value": 6039 },
  { "name": "天津市", "value": 6194 },
  { "name": "河北省", "value": 1846 },
  { "name": "山西省", "value": 1068 },
  { "name": "内蒙古自治区", "value": 8389 },
  { "name": "辽宁省", "value": 9876 },
  { "name": "吉林省", "value": 2709 },
  { "name": "黑龙江省", "value": 4131 },
  { "name": "上海市", "value": 8092 },
  { "name": "江苏省", "value": 8750 }
  // ... 更多省份数据
]
```

**地图可视化特点**：
- 支持世界地图、中国地图、各省地图
- 需要先注册地图数据（`registerMap`）
- 支持 `visualMap` 进行数据映射
- 支持地图交互（缩放、平移、点击等）

## 常用配置项详解

### title 配置

```javascript
title: {
  text: '图表标题',              // 主标题文本
  subtext: '副标题',            // 副标题文本
  left: 'center',               // 标题位置：'left', 'center', 'right'
  top: 'top',                   // 垂直位置
  textStyle: {
    color: '#333',              // 标题颜色
    fontSize: 18,               // 字体大小
    fontWeight: 'bold'          // 字粗细
  }
}
```

### tooltip 配置

```javascript
tooltip: {
  trigger: 'item',              // 触发类型：'item', 'axis'
  formatter: '{b}: {c}',        // 格式化函数
  backgroundColor: 'rgba(0,0,0,0.7)',
  borderColor: '#333',
  borderWidth: 1
}
```

### legend 配置

```javascript
legend: {
  data: ['系列1', '系列2'],     // 图例数据
  orient: 'horizontal',         // 布局方向：'horizontal', 'vertical'
  left: 'center',               // 位置
  top: 'top',
  selectedMode: 'single'        // 选择模式：'single', 'multiple', false
}
```

### xAxis/yAxis 配置

```javascript
xAxis: {
  type: 'category',             // 坐标轴类型：'value', 'category', 'time'
  data: ['一月', '二月', '三月'],
  name: '月份',                 // 坐标轴名称
  axisLabel: {
    rotate: 45,                 // 标签旋转角度
    formatter: '{value}'       // 标签格式化
  }
},
yAxis: {
  type: 'value',               // 数值轴
  name: '数值',
  min: 0,                       // 最小值
  max: 1000                     // 最大值
}
```

## 响应式设计

```javascript
// 监听窗口大小变化，自适应调整图表大小
window.addEventListener('resize', function() {
  myChart.resize();
});

// 或使用防抖优化
let resizeTimer;
window.addEventListener('resize', function() {
  clearTimeout(resizeTimer);
  resizeTimer = setTimeout(function() {
    myChart.resize();
  }, 200);
});
```

## 实用技巧

### 1. 配置项合并

```javascript
// setOption 默认会合并配置项
myChart.setOption({...});

// 如果需要完全替换，设置 notMerge 为 true
myChart.setOption({...}, true);
```

### 2. 动态数据更新

```javascript
// 定时更新数据
setInterval(function() {
  const newData = getNewData();
  myChart.setOption({
    series: [{
      data: newData
    }]
  });
}, 2000);
```

### 3. 事件处理

```javascript
// 点击事件
myChart.on('click', function(params) {
  console.log(params.name, params.value);
  // 根据点击的元素进行相应操作
});

// 鼠标移入事件
myChart.on('mouseover', function(params) {
  console.log('鼠标移入', params);
});
```

### 4. 图表销毁

```javascript
// 销毁图表实例，释放资源
myChart.dispose();

// 或者清除配置
myChart.clear();
```

## 完整示例代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>ECharts常见图表</title>
  <style>
    .item {
      background: #eee;
      width: 500px;
      height: 500px;
      margin: 50px auto;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
    <div class="item"></div>
  </div>
  <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js"></script>
  <script src="https://cdn.bootcdn.net/ajax/libs/echarts/5.1.1/echarts.min.js"></script>
  <script src="https://cdn.bootcdn.net/ajax/libs/Mock.js/1.0.0/mock-min.js"></script>
  <script src="./demo.js"></script>
</body>
</html>
```

## 学习资源

- [ECharts 官方文档](https://echarts.apache.org/zh/index.html)
- [ECharts 示例库](https://echarts.apache.org/examples/zh/index.html)
- [ECharts 配置项手册](https://echarts.apache.org/zh/option.html)

## 实践练习

1. 创建一个包含折线图和柱状图的混合图表
2. 从 API 获取实时数据并更新图表
3. 实现一个带有地图可视化的数据大屏
4. 添加交互功能，实现图表之间的联动

## 总结

ECharts 是一个功能强大的数据可视化库，通过本章节的学习，你应该掌握了：

- ECharts 的基础配置和使用方法
- 常见图表类型的应用场景
- 远程数据的加载和处理
- 地图数据可视化的实现

在实际开发中，建议根据具体需求选择合适的图表类型，并通过配置项进行精细化调整，以达到最佳的视觉效果和用户体验。
