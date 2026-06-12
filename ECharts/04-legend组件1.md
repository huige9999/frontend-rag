# 04-legend组件1

## 简介
本节课学习 legend（图例）组件的基础配置，包括图例类型（plain）、自定义图标、formatter 格式化、选中模式等常用属性的设置。

## 关键代码

### legend 组件基础配置（plain 类型）

```javascript
var myChart1 = echarts.init(document.getElementById('chart1'));

myChart1.setOption({
    title: {
        text: '当月销售业绩',
    },
    legend: {
        data: ['今日销量', '昨日销量', {
            name: '明日销量',
            icon: 'image://data:image/gif;base64,...',   // 自定义图例图标
            textStyle: {}
        }],
        type: 'plain',              // 图例类型：普通图例
        left: 'right',              // 图例位置
        orient: 'horizontal',       // 图例布局朝向：水平
        padding: [10, 20],
        itemGap: 20,                // 各图例项之间的间距
        itemWidth: 35,              // 图例标记的图形宽度
        itemHeight: 24,             // 图例标记的图形高度
        formatter: function (name) {
            return "你说 " + name + " 高么？";
        },
        selectedMode: 'multiple',   // 选中模式：多选
        inactiveColor: '#ccc',      // 图例关闭时的颜色
        selected: {                 // 各图例项的选中状态
            '今日销量': true,
            '昨日销量': true,
            '明日销量': true,
        },
        textStyle: {
            color: '#f00'
        },
        backgroundColor: 'gray',
    },
    xAxis: {
        data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
    },
    yAxis: {},
    series: [
        {
            name: '今日销量',
            type: 'bar',
            data: [5, 20, 36, 10, 19, 24]
        },
        {
            name: '昨日销量',
            type: 'bar',
            data: [5, 20, 36, 10, 19, 24]
        },
        {
            name: '明日销量',
            type: 'bar',
            data: [5, 20, 36, 10, 19, 24]
        }
    ]
});
```

### 代码说明
- `type: 'plain'` - 图例类型为普通图例（另一种是 `'scroll'` 滚动图例）
- `data` - 图例数据数组，可以是字符串或对象（对象可单独配置图标和样式）
- `icon` - 自定义图例标记图标，支持 `'image://'` 图片和 `'path://'` SVG 路径
- `orient` - 图例布局方向：`'horizontal'` 水平或 `'vertical'` 垂直
- `itemGap` - 各图例项之间的间距
- `itemWidth` / `itemHeight` - 图例标记图形的宽高
- `formatter` - 图例文本格式化，支持字符串模板 `'{name}'` 和函数回调
- `selectedMode` - 选中模式：`'single'` 单选、`'multiple'` 多选
- `inactiveColor` - 图例关闭（未选中）时的颜色
- `selected` - 对象形式配置各图例项的初始选中状态
- `backgroundColor` - 图例背景色
