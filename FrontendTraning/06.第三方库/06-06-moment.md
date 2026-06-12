# Moment.js 日期时间处理库

## 1. Moment.js 简介

### 1.1 什么是 Moment.js？

Moment.js 是一个优秀的 JavaScript 日期处理库，用于解析、验证、操作和显示日期和时间。

### 1.2 主要特性

- **日期解析**：支持多种格式的日期字符串解析
- **日期操作**：方便地进行日期的加减运算
- **日期格式化**：灵活的日期格式化输出
- **国际化支持**：支持多语言和时区处理
- **链式调用**：支持流畅的链式操作

### 1.3 引入方式

```html
<!-- CDN 引入 -->
<script src="https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/moment.min.js"></script>
<!-- 中文语言包 -->
<script src="https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/locale/zh-cn.js"></script>
```

```bash
# npm 安装
npm install moment
```

## 2. 基本使用

### 2.1 创建 Moment 对象

```javascript
// 获取当前时间
const now = moment();

// 通过日期字符串创建
const date1 = moment('2026-06-06');
const date2 = moment('2026-06-06 14:30:00');

// 通过日期对象创建
const date3 = moment(new Date());

// 通过时间戳创建
const date4 = moment(1622937600000);

// 通过数组创建 [年, 月, 日, 时, 分, 秒]
const date5 = moment([2026, 5, 6, 14, 30, 0]);
// 注意：月份从0开始，5代表六月
```

### 2.2 日期格式化

```javascript
const now = moment();

// 常用格式
console.log(now.format('YYYY-MM-DD'));              // 2026-06-06
console.log(now.format('YYYY-MM-DD HH:mm:ss'));     // 2026-06-06 14:30:00
console.log(now.format('YYYY年MM月DD日'));          // 2026年06月06日

// 格式化占位符
// YYYY：四位年份
// MM：两位月份（01-12）
// DD：两位日期（01-31）
// HH：两位小时（00-23）
// mm：两位分钟（00-59）
// ss：两位秒数（00-59）
// Do：日期的序数表示（1st, 2nd, 3rd...）
// dddd：星期几（中文环境下：星期一）
// M：月份（1-12）
// D：日期（1-31）
```

### 2.3 日期验证

```javascript
// 验证日期是否有效
const date1 = moment('2026-02-30');  // 无效日期（2月没有30号）
const date2 = moment('2026-06-06');  // 有效日期

console.log(date1.isValid());  // false
console.log(date2.isValid());  // true
```

## 3. 日期操作

### 3.1 日期获取

```javascript
const date = moment('2026-06-06 14:30:00');

// 获取各个时间单位的值
console.log(date.year());        // 2026 - 年份
console.log(date.month());       // 5 - 月份（0-11，5代表六月）
console.log(date.date());        // 6 - 日期（1-31）
console.log(date.day());         // 6 - 星期几（0-6，0代表周日）
console.log(date.hour());        // 14 - 小时（0-23）
console.log(date.minute());      // 30 - 分钟（0-59）
console.log(date.second());      // 0 - 秒数（0-59）
```

### 3.2 日期设置

```javascript
const date = moment();

// 设置各个时间单位的值
date.year(2026);           // 设置年份
date.month(5);             // 设置月份（0-11）
date.date(6);              // 设置日期
date.hour(14);             // 设置小时
date.minute(30);           // 设置分钟
date.second(0);            // 设置秒数

// 链式设置
moment().year(2026).month(5).date(6);

// 设置到时间单位的开始/结束
moment().startOf('year');    // 设置到今年的1月1日 00:00:00
moment().endOf('month');     // 设置到本月最后一天的 23:59:59
moment().startOf('day');     // 设置到今天的 00:00:00
```

### 3.3 日期运算

```javascript
const date = moment('2026-06-06');

// 日期加法
date.add(1, 'years');       // 加1年
date.add(2, 'months');      // 加2月
date.add(7, 'days');        // 加7天
date.add(1, 'weeks');       // 加1周
date.add(3, 'hours');       // 加3小时

// 日期减法
date.subtract(1, 'years');  // 减1年
date.subtract(30, 'days');  // 减30天

// 链式运算
moment().add(1, 'years').subtract(6, 'months');
```

### 3.4 日期比较

```javascript
const date1 = moment('2026-06-06');
const date2 = moment('2026-06-07');

// 比较先后
console.log(date1.isBefore(date2));   // true - date1 是否在 date2 之前
console.log(date1.isAfter(date2));    // false - date1 是否在 date2 之后
console.log(date1.isSame(date2));     // false - 两个日期是否相同

// 日期差值计算
const diff = date2.diff(date1, 'days');    // 1 - 相差天数
const diff2 = date2.diff(date1, 'months'); // 0 - 相差月数
const diff3 = date2.diff(date1, 'years');  // 0 - 相差年数

// 默认返回毫秒差值
const ms = date2.diff(date1);  // 86400000
```

## 4. 时区处理

### 4.1 UTC 偏移量

```javascript
// 设置 UTC 偏移量（单位：小时）
const chinaTime = moment().utcOffset(8);      // 中国时间 UTC+8
const londonTime = moment().utcOffset(0);     // 伦敦时间 UTC+0
const newYorkTime = moment().utcOffset(-5);   // 纽约时间 UTC-5

// 获取当前时间的偏移量
const offset = moment().utcOffset();  // 返回当前时区的偏移量（分钟）
```

### 4.2 全球时区示例

```javascript
/**
 * 设置各个时区的时间文本
 */
function setGlobalTime() {
  // 遍历所有带有 zone 属性的元素
  $('[zone]').each(function(i, ele) {
    const zone = +$(ele).attr('zone');  // 获取时区偏移量
    const time = moment()
      .utcOffset(zone)  // 设置时区偏移
      .format('YYYY-MM-DD HH:mm:ss');  // 格式化输出
    $(ele).html(time);
  });
}

// 每秒更新一次时间
setInterval(setGlobalTime, 1000);
```

## 5. 相对时间

### 5.1 Calendar 方法

```javascript
const date = moment('2026-06-06');

// 使用 calendar 方法显示相对时间
const calendarText = date.calendar(null, {
  sameDay: '[今天]',           // 当天显示"今天"
  nextDay: '[明天]',           // 明天显示"明天"
  nextWeek: '下个dddd',        // 下周显示"下个星期X"
  lastDay: '[昨天]',           // 昨天显示"昨天"
  lastWeek: '[上个]dddd',      // 上周显示"上个星期X"
  sameElse: 'YYYY-MM-DD'       // 其他情况显示日期
});

console.log(calendarText);
```

### 5.2 相对时间显示

```javascript
// fromNow：距离现在的时间
moment('2026-06-05').fromNow();     // "1天前"
moment('2026-06-07').fromNow();     // "1天内"

// toNow：距离现在的显示
moment('2026-06-05').toNow();       // "1天内"
moment('2026-06-07').toNow();       // "1天前"

// from：两个日期之间的相对时间
const date1 = moment('2026-06-06');
const date2 = moment('2026-06-07');
date1.from(date2);  // "1天前"
date2.from(date1);  // "1天后"
```

## 6. 实战案例

### 6.1 全球时钟应用

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>全球时钟</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      font-family: Arial, sans-serif;
    }
    .container {
      margin-top: 2em;
    }
    .form-group {
      display: flex;
      margin: 1em 0;
    }
    .form-group label {
      margin-right: 1em;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="form-group">
      <label>中国</label>
      <div zone="8"></div>
    </div>
    <div class="form-group">
      <label>英国</label>
      <div zone="0"></div>
    </div>
    <div class="form-group">
      <label>纽约</label>
      <div zone="-5"></div>
    </div>
    <div class="form-group">
      <label>悉尼</label>
      <div zone="10"></div>
    </div>
  </div>
  
  <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
  <script src="https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/moment.min.js"></script>
  <script src="https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/locale/zh-cn.js"></script>
  <script>
    /**
     * 设置全球各地区时间
     * 遍历所有带zone属性的元素，根据时区显示对应时间
     */
    function setGlobalTime() {
      $('[zone]').each(function(i, ele) {
        const zone = +$(ele).attr('zone');  // 获取时区偏移量
        const time = moment()
          .utcOffset(zone)  // 设置时区偏移
          .format('YYYY-MM-DD HH:mm:ss');  // 格式化时间字符串
        $(ele).html(time);
      });
    }
    
    // 立即执行一次
    setGlobalTime();
    
    // 每秒更新一次时间
    setInterval(setGlobalTime, 1000);
  </script>
</body>
</html>
```

### 6.2 生日计算器

```javascript
/**
 * 根据生日计算相关信息
 * 包含：年龄、生存秒数、距离下个生日天数等
 */
function setBirthInfo() {
  const birthTxt = $('#birthInput').val();
  
  // 如果没有输入生日，清空信息区域
  if (!birthTxt) {
    $('#birthInfo').empty();
    return;
  }
  
  const birthday = moment(birthTxt);
  const now = moment();
  
  // 验证日期有效性
  if (birthday > now || !birthday.isValid()) {
    $('#birthInfo').html('生日不正确！');
    return;
  }
  
  // 1. 显示出生日期
  const p1 = `<p>
    <strong>出生日期：</strong>
    <span>${birthday.format('YYYY年MM月DD日')}</span>
  </p>`;
  
  // 2. 计算年龄（年数差值）
  const age = now.diff(birthday, 'years');
  const p2 = `<p>
    <strong>年龄：</strong>
    <span>${age}</span>
  </p>`;
  
  // 3. 计算生存秒数
  const seconds = now.diff(birthday, 'seconds');
  const p3 = `<p>
    你在这个世界上已存在了
    <strong>${seconds}</strong>
    秒钟
  </p>`;
  
  // 4. 计算距离下个生日的天数
  let targetDate;
  const thisYearBirth = birthday.years(now.years());  // 今年的生日
  
  if (thisYearBirth < now) {
    // 今年的生日已过，计算明年的生日
    targetDate = moment(thisYearBirth).add(1, 'years');
  } else {
    // 今年的生日未过，使用今年的生日
    targetDate = thisYearBirth;
  }
  
  const days = targetDate.diff(now, 'days');
  const p4 = `<p>
    你还有
    <strong>${days}</strong>
    天就会迎来你 ${age + 1} 岁的生日
  </p>`;
  
  // 5. 显示生日相对时间（昨天、今天、明天等）
  let p5;
  const cal = thisYearBirth.calendar(null, {
    sameDay: '今天',
    nextDay: '明天',
    nextWeek: '下个 dddd',
    lastDay: '昨天',
    lastWeek: '上个 dddd',
    sameElse: 'YYYY年MM月DD日',
  });
  
  if (thisYearBirth < now) {
    // 今年生日已过
    p5 = `<p>你已在
      <strong>${cal}</strong>
      过了生日</p>`;
  } else {
    // 今年生日未过
    p5 = `<p>你将在
      <strong>${cal}</strong>
      迎来下一个生日</p>`;
  }
  
  // 组装所有信息并显示
  $('#birthInfo').html(p1 + p2 + p3 + p4 + p5);
}

// 监听生日输入框的变化
$('#birthInput').on('input', setBirthInfo);
```

## 7. 常用方法汇总

### 7.1 创建与解析

```javascript
moment()                    // 当前时间
moment('2026-06-06')       // 解析字符串
moment(new Date())          // 解析 Date 对象
moment(1622937600000)       // 解析时间戳
moment([2026, 5, 6])        // 解析数组
```

### 7.2 格式化与验证

```javascript
moment().format('YYYY-MM-DD')           // 格式化日期
moment().isValid()                       // 验证有效性
moment().fromNow()                       // 相对时间
moment().calendar()                      // 日历显示
```

### 7.3 获取与设置

```javascript
moment().year()                          // 获取年份
moment().month()                         // 获取月份
moment().date()                          // 获取日期
moment().year(2026)                      // 设置年份
moment().startOf('day')                  // 设置到当天开始
moment().endOf('month')                  // 设置到月末
```

### 7.4 运算与比较

```javascript
moment().add(1, 'days')                  // 加1天
moment().subtract(1, 'months')           // 减1月
moment().diff(moment(), 'days')          // 日期差值
moment().isBefore(moment())              // 比较先后
moment().isAfter(moment())               // 比较先后
```

### 7.5 时区处理

```javascript
moment().utcOffset(8)                    // 设置时区偏移
moment().utcOffset()                     // 获取时区偏移
```

## 8. 注意事项

1. **月份从0开始**：Moment.js 中月份从 0 开始计数（0-11，0代表一月）
2. **不可变性**：Moment 对象的操作返回新对象，不修改原对象
3. **有效性检查**：解析用户输入时要检查 `isValid()`
4. **时区处理**：使用 `utcOffset()` 处理时区，而不是 `timezone()`
5. **性能考虑**：频繁操作时考虑复用 Moment 对象
6. **国际化**：使用语言包实现多语言支持

## 9. 练习题

### 9.1 基础练习

1. 创建一个 Moment 对象表示当前时间，并以 "YYYY年MM月DD日 HH:mm:ss" 格式输出
2. 计算距离2027年元旦还有多少天
3. 判断一个日期字符串是否有效

### 9.2 进阶练习

1. 实现一个倒计时功能，显示距离目标时间的天、小时、分钟、秒
2. 创建一个日期选择器，显示选中日期的详细信息
3. 实现不同时区时间的实时转换和显示

### 9.3 综合练习

1. 完善全球时钟应用，添加更多时区支持
2. 扩展生日计算器，添加星座、生肖等计算功能
3. 制作一个日历应用，支持月份切换和日期选择

## 10. 参考资源

- [Moment.js 官方文档](https://momentjs.com/docs/)
- [Moment.js 中文文档](https://momentjs.cn/)
- [CDN 引入地址](https://www.bootcdn.cn/moment.js/)