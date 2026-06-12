# 03-title组件

## 简介
本节课详细讲解 ECharts 的 title（标题）组件的完整配置项，包括主标题和副标题的样式、边框、阴影等属性设置。

## 关键代码

### title 组件完整配置

```javascript
var myChart1 = echarts.init(document.getElementById('chart1'));

myChart1.setOption({
    title: {
        id: '',
        show: true,                         // 是否显示标题组件
        text: '当月销售业绩清单',             // 主标题文本
        link: 'http://www.baidu.com/',       // 主标题超链接
        target: 'self',                      // 跳转方式
        textStyle: {
            color: 'gold',
            fontStyle: 'italic',
            fontWeight: 'normal',
            fontFamily: 'Microsoft YaHei',
            fontSize: 20,
            lineHeight: 30,
            textBorderColor: 'red',          // 文字描边颜色
            textBorderWidth: 2,              // 文字描边宽度
            textShadowColor: '#000',         // 文字阴影颜色
            textShadowBlur: 2,               // 文字阴影长度
            textShadowOffsetX: 10,           // 文字阴影 X 偏移
            textShadowOffsetY: 10,           // 文字阴影 Y 偏移
        },

        // 副标题
        subtext: '陈学辉',
        sublink: 'http://www.qq.com/',
        subtarget: 'self',
        subtextStyle: {},

        textAlign: 'left',                   // 整体（包括副标题）水平对齐
        padding: [10, 5, 20, 30],            // 标题内边距 [上, 右, 下, 左]
        itemGap: 20,                         // 主标题和副标题之间的间距

        left: 10,
        top: 10,

        backgroundColor: '#ccc',
        borderColor: '#000',
        borderWidth: 2,
        borderRadius: [5, 10, 20, 30],       // 圆角半径 [左上, 右上, 右下, 左下]

        shadowColor: 'rgba(0, 0, 0, 0.5)',
        shadowBlur: 10,
        shadowOffsetX: 5,
        shadowOffsetY: 5,
    },
    legend: {
        data: ['销量']
    },
    xAxis: {
        data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子'],
    },
    yAxis: {},
    series: {
        name: '销量',
        type: 'bar',
        data: [5, 20, 36, 10, 19, 24]
    }
});
```

### 代码说明
- `show` - 控制标题组件是否显示
- `text` / `subtext` - 分别设置主标题和副标题文本
- `link` / `sublink` - 分别设置主标题和副标题的点击超链接
- `target` / `subtarget` - 超链接打开方式（`'self'` 当前窗口，`'blank'` 新窗口）
- `textStyle` - 主标题文字样式，包括颜色、字体、大小、描边和阴影等
- `textAlign` - 主副标题整体水平对齐方式
- `padding` - 标题内边距，数组格式 `[上, 右, 下, 左]`
- `itemGap` - 主标题与副标题之间的间距
- `left` / `top` - 标题组件的定位
- `backgroundColor` / `borderColor` / `borderWidth` / `borderRadius` - 标题背景和边框样式
- `shadowColor` / `shadowBlur` / `shadowOffsetX` / `shadowOffsetY` - 标题阴影效果
