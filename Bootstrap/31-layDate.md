# layDate

## 课程简介

本节课学习 layui 日期时间选择器 **layDate**，它是一个独立的时间选择组件，不依赖 Bootstrap，功能强大且灵活。支持多种选择类型、自定义格式、日期范围限制、主题切换等。

- 官网：https://www.layui.com/laydate/

---

## 关键知识点

### 基本使用

- 引入 `laydate/laydate.js`
- 通过 `laydate.render({options})` 初始化，返回实例对象
- `elem` 指定绑定的元素选择器

### 控件选择类型（type）

| type值 | 说明 |
|---|---|
| `year` | 选择年 |
| `month` | 选择月 |
| `date` | 选择日期（默认） |
| `time` | 选择时间 |
| `datetime` | 选择日期时间 |

### 常用配置选项

| 选项 | 说明 |
|---|---|
| `elem` | 绑定元素 |
| `type` | 控件选择类型 |
| `format` | 自定义日期格式 |
| `value` | 初始值 |
| `min` / `max` | 最小/最大可选日期 |
| `trigger` | 弹出触发事件，默认 `focus` |
| `show` | 默认是否显示 |
| `position` | 定位方式 |
| `zIndex` | 层叠顺序 |
| `showBottom` | 是否显示底部栏 |
| `btns` | 底部工具按钮 |
| `lang` | 语言（`cn`/`en`） |
| `theme` | 主题 |
| `calendar` | 是否显示公历节日 |
| `mark` | 标注重要日子 |
| `range` | 开启范围选择 |

---

## 关键代码示例

### HTML 结构

```html
<div class="form-group form-inline">
    <label for="date">日期：</label>
    <div type="text" class="form-control w-50" placeholder="请选择日期" id="date"></div>
</div>
```

### 初始化与核心配置

```javascript
var layDemo = laydate.render({
    elem: '#date',              // 绑定元素
    type: 'date',               // 选择类型：year/month/date/time/datetime
    format: 'yyyy-MM-dd',       // 自定义格式
    trigger: 'focus',           // 触发方式
    show: false,                // 默认不显示
    zIndex: 66666666,           // 层叠顺序
    showBottom: true,           // 显示底部栏
    btns: ['now', 'clear', 'confirm'],  // 底部按钮
    lang: 'cn',                 // 中文
    theme: 'default',           // 主题：default/molv/#f00/grid
    calendar: true,             // 显示公历节日
});
```

### 自定义日期格式

```javascript
format: 'yyyy-MM-dd HH:mm:ss',               // 标准格式
format: 'yyyy年MM月dd日 HH时mm分ss秒',         // 中文格式
format: 'yyyyMMdd',                            // 紧凑格式
format: 'dd/MM/yyyy',                          // 日/月/年
format: '北京时间：HH点mm分',                    // 自定义文字
format: '亲，还记得yyyy年的M月d号那一天么？',     // 混合文字
```

### 日期范围限制

```javascript
// 固定日期范围
min: '2019-1-1',
max: '2019-12-31',

// 精确到时间
min: '2019-12-11 12:30:00',
max: '2019-12-18 12:30:00',

// 相对天数（前后7天）
min: -7,
max: 7,

// 时间范围
min: '09:30:00',
max: '17:30:00',
```

### 标注重要日子

```javascript
mark: {
    '0-5-3': '生日',         // 每年5月3日
    '0-9-1': '开学',         // 每年9月1日
    '0-0-10': '工资',        // 每月10日
    '2019-12-6': '入职',     // 特定日期
},
```

### 回调函数

```javascript
var layDemo = laydate.render({
    elem: '#date',

    // 控件初始打开的回调
    ready: function(date){
        console.log(date);
    },

    // 日期时间被切换后的回调
    change: function(value, date, endDate){
        console.log(value);    // 当前值
        console.log(date);     // 日期对象
        layDemo.hint(value);   // 提示
    },

    // 控件选择完毕后的回调
    done: function(value, date, endDate){
        console.log(value);    // 选择结果
        console.log(date);     // 日期对象
        console.log(endDate);  // 结束日期（范围选择时）
    },
});
```

### 全局方法

```javascript
// 获取某月最后一天
var endDay = laydate.getEndDate();           // 当月
var endDay = laydate.getEndDate(2, 2035);    // 2035年2月
console.log(endDay);
```

---

## 效果说明

- layDate 是一款轻量独立的日期选择器，不依赖任何CSS框架
- 支持年/月/日/时/分多种粒度选择，灵活度极高
- 日期格式支持混合中文文字，自定义能力极强
- 内置多种主题，支持标注重要日子和公历节日
- 通过 `ready`、`change`、`done` 三个回调覆盖完整交互生命周期
