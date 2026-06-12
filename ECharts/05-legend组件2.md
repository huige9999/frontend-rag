# 05-legend组件2

## 简介
本节课深入学习 legend 组件的 scroll（可滚动）类型，适用于图例项较多时的场景，包括翻页按钮、分页格式化、动画等滚动相关配置。

## 关键代码

### 图例基础配置（plain 类型，同 04 课）

> 与 04 课 chart1 配置一致，不再重复。

### legend 滚动类型（scroll）配置

```javascript
var myChart2 = echarts.init(document.getElementById('chart2'));
var data = getData();

myChart2.setOption({
    legend: {
        type: 'scroll',              // 滚动类型图例
        data: data.legendData,
        selected: data.selected,     // 各图例项选中状态

        scrollDataIndex: 6,          // 滚动时当前显示的起始图例索引
        pageButtonItemGap: 30,       // 翻页按钮与图例项之间的间距
        pageButtonGap: 40,           // 翻页按钮之间的间距
        pageButtonPosition: 'start', // 翻页按钮位置：'start' 或 'end'

        pageFormatter: function (obj) {
            return obj.current + '--' + obj.total;
        },
        orient: 'vertical',          // 垂直布局
        left: 'right',

        pageIcons: {                 // 自定义翻页图标
            horizontal: [
                'image://...',       // 上一页图标（图片方式）
                'path://M30.9,...'   // 下一页图标（SVG路径方式）
            ]
        },
        pageIconColor: 'red',              // 翻页按钮颜色
        pageIconInactiveColor: 'green',    // 翻页按钮不激活时的颜色
        pageIconSize: [50, 80],            // 翻页按钮尺寸 [宽, 高]
        pageTextStyle: {
            color: 'teal',
        },
        animation: true,                   // 是否开启动画
        animationDurationUpdate: 1500,     // 动画更新时长
    },
    series: [
        {
            name: '姓名',
            type: 'pie',
            data: data.seriesData,
        }
    ]
});
```

### 辅助数据生成函数

```javascript
function getData() {
    var nameList = [
        '赵', '钱', '孙', '李', '周', '吴', '郑', '王', '冯', '陈',
        '褚', '卫', '蒋', '沈', '韩', '杨', '朱', '秦', '尤', '许',
        // ... 更多姓氏
    ];

    var legendData = [];   // 图例数据
    var seriesData = [];   // 系列数据
    var selected = {};     // 选中的图例

    nameList.sort(function () {
        return Math.random() - 0.5;    // 随机打乱顺序
    });

    for (var i = 0; i < 50; i++) {
        var makeUpName = nameList[0] + nameList[1];   // 拼成双字名

        legendData.push(makeUpName);
        seriesData.push({
            name: makeUpName,
            value: Math.round(Math.random() * 100000)
        });
        selected[makeUpName] = i < 6;   // 前6个显示，其余不显示

        nameList.shift();
        nameList.shift();
    }

    return {
        legendData: legendData,
        seriesData: seriesData,
        selected: selected
    }
}
```

### 代码说明
- `type: 'scroll'` - 图例类型设为可滚动，当图例项过多时可翻页查看
- `scrollDataIndex` - 滚动视口的起始图例索引
- `pageButtonItemGap` - 翻页按钮与首个/末个图例项之间的间距
- `pageButtonGap` - 翻页前后按钮之间的间距
- `pageButtonPosition` - 翻页按钮位置，`'start'`（起始端）或 `'end'`（末尾端）
- `pageFormatter` - 翻页信息格式化，接收 `{current, total}` 对象
- `pageIcons` - 自定义翻页图标，区分 `horizontal` 和 `vertical` 方向
- `pageIconColor` / `pageIconInactiveColor` - 翻页按钮的激活/非激活颜色
- `pageIconSize` - 翻页按钮尺寸 `[宽, 高]`
- `pageTextStyle` - 翻页信息的文字样式
- `animation` / `animationDurationUpdate` - 控制翻页切换时的动画效果
