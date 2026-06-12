# dateTimePicker

## 课程简介

本节课学习第三方日期时间选择器插件 **bootstrap-datetimepicker**，它基于 Bootstrap 3 构建，提供了丰富的日期时间选择功能，包括日期格式化、视图切换、语言设置、日期范围限制等。插件需要 jQuery 支持。

- 官网：https://www.malot.fr/bootstrap-datetimepicker/
- 中文翻译：https://www.bootcss.com/p/bootstrap-datetimepicker/

---

## 关键知识点

### 基本使用

- 引入 Bootstrap 3 CSS、jQuery、`bootstrap-datetimepicker.js`
- 中文化需要自定义语言包 `$.fn.datetimepicker.dates['zh-CN']`
- 通过 `$('.selector').datetimepicker({options})` 初始化

### 配置选项

| 选项 | 说明 |
|---|---|
| `format` | 日期格式，如 `yyyy-mm-dd`、`yyyy-mm-dd hh:ii:ss` |
| `weekStart` | 一周从哪一天开始（0=周日，6=周六） |
| `startDate` | 可选的起始日期 |
| `endDate` | 可选的结束日期 |
| `daysOfWeekDisabled` | 一周中哪些天不可选（数组） |
| `autoclose` | 选完日期后是否自动关闭 |
| `startView` | 初始视图（0=小时/1=天/2=月/3=年/4=十年） |
| `minView` | 最小可选视图级别 |
| `maxView` | 最大可选视图级别 |
| `todayBtn` | 是否显示"今天"按钮 |
| `language` | 语言设置 |
| `minuteStep` | 分钟间隔步数 |
| `pickerPosition` | 选择器弹出位置 |
| `showMeridian` | 是否显示上下午 |
| `keyboardNavigation` | 是否支持键盘选择 |

---

## 关键代码示例

### HTML 结构

```html
<!-- 需引入 Bootstrap 3 CSS -->
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
<link rel="stylesheet" href="css/bootstrap-datetimepicker.css">

<div class="form-group form-inline">
    <label for="datetime">日期：</label>
    <input type="text" class="form-control datetime" id="datetime">
</div>
```

### 中文化语言包

```javascript
$.fn.datetimepicker.dates['zh-CN'] = {
    days: ["星期日", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六", "星期日"],
    daysShort: ["周日", "周一", "周二", "周三", "周四", "周五", "周六", "周日"],
    daysMin: ["日", "一", "二", "三", "四", "五", "六", "日"],
    months: ["一月", "二月", "三月", "四月", "五月", "六月", "七月", "八月", "九月", "十月", "十一月", "十二月"],
    monthsShort: ["一月", "二月", "三月", "四月", "五月", "六月", "七月", "八月", "九月", "十月", "十一月", "十二月"],
    today: "今天",
    suffix: [],
    meridiem: ["上午", "下午"]
};
```

### 初始化与常用配置

```javascript
$('.datetime').datetimepicker({
    format: 'yyyy-mm-dd',          // 日期格式
    weekStart: 1,                  // 从周一开始
    autoclose: true,               // 选完后自动关闭
    startView: 2,                  // 从月视图开始（默认）
    minView: 0,                    // 最小可选到分钟（默认）
    todayBtn: true,                // 显示今天按钮
    keyboardNavigation: true,      // 支持键盘操作
    language: 'zh-CN',             // 中文
    minuteStep: 10,                // 分钟间隔10
    pickerPosition: 'bottom-right',// 弹出位置
    showMeridian: true,            // 显示上下午
});
```

### 日期格式示例

```javascript
format: 'yyyy-mm-dd',              // 2020-01-15
format: 'yyyy-mm-dd hh:ii',        // 2020-01-15 08:30
format: 'yyyy-mm-dd hh:ii:ss',     // 2020-01-15 08:30:00
format: 'yyyy-mm-dd hh:ii:ss P',   // 带上下午
```

### 方法调用

```javascript
$('.datetime').datetimepicker('remove');              // 移除日期选择器
$('.datetime').datetimepicker('show');                // 显示
$('.datetime').datetimepicker('hide');                // 隐藏
$('.datetime').datetimepicker('setStartDate', '2019-10-01');  // 设置起始日期
$('.datetime').datetimepicker('setEndDate', '2020-10-01');    // 设置结束日期
$('.datetime').datetimepicker('setDaysOfWeekDisabled', [0, 6]); // 设置不可选的星期
```

### 事件监听

```javascript
$('.datetime').datetimepicker()
    .on('show', function () {
        console.log('日期选择器组件显示了');
    })
    .on('hide', function () {
        console.log('日期选择器组件隐藏了');
    })
    .on('changeDate', function () {
        console.log('日期变动了');
    })
    .on('changeYear', function () {
        console.log('年份变动了');
    })
    .on('changeMonth', function () {
        console.log('月份变动了');
    })
    .on('outOfRange', function () {
        console.log('选择的日期超出范围了');
    });
```

---

## 效果说明

- 点击输入框弹出日期时间选择面板，支持年/月/日/时/分多级视图切换
- 可通过 `format` 自定义日期显示格式
- 支持限制可选日期范围、禁用指定星期、中文化等丰富配置
- 提供方法动态控制选择器状态，以及事件回调响应日期变化
