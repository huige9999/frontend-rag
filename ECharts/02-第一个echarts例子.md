# 02-第一个echarts例子

## 简介
本节课学习如何创建第一个 ECharts 图表实例，包括柱状图和饼图（玫瑰图）两种基本图表类型的初始化与配置。

## 关键代码

### 柱状图示例

```javascript
var myChart1 = echarts.init(document.getElementById('chart1'));

myChart1.setOption({
    title: {
        text: '柱状图',
    },
    legend: {    // 图例
        data: ['销量']
    },
    xAxis: {     // x轴的配置
        data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子'],
    },
    yAxis: {     // y轴的配置
    },
    series: {    // 系列列表
        name: '销量',
        type: 'bar',     // 图表的类型
        data: [5, 20, 36, 10, 19, 24]    // 图表的数据
    }
});
```

### 饼图（南丁格尔玫瑰图）示例

```javascript
var myChart2 = echarts.init(document.getElementById('chart2'));

myChart2.setOption({
    title: {
        text: '饼图',
    },
    series: {    // 系列列表
        name: '访问来源',
        type: 'pie',
        roseType: 'angle',    // 开启南丁格尔玫瑰图模式
        data: [
            {value: 235, name: '视频广告'},
            {value: 274, name: '联盟广告'},
            {value: 310, name: '邮件营销'},
            {value: 335, name: '直接访问'},
            {value: 400, name: '搜索引擎'},
        ]
    }
});
```

### 代码说明
- `echarts.init(dom)` - 初始化 ECharts 实例，传入一个 DOM 容器元素
- `setOption(option)` - 通过配置项参数来配置图表的数据和样式
- `title` - 标题组件，用于设置图表的主标题文本
- `legend` - 图例组件，`data` 数组指定图例项名称
- `xAxis` / `yAxis` - 直角坐标系的 X 轴和 Y 轴配置
- `series` - 系列列表，每个系列通过 `type` 指定图表类型（如 `bar` 柱状图、`pie` 饼图）
- `roseType` - 设为 `'angle'` 可将饼图转为南丁格尔玫瑰图，扇区半径与数据大小成正比
