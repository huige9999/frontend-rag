# MockJS 数据模拟库

## 章节介绍

MockJS 是一个用于生成随机数据、拦截 Ajax 请求的 JavaScript 库。它可以帮助前端开发人员在不依赖后端接口的情况下，模拟接口数据，提高开发效率。

### 核心特性

- **数据模拟丰富**：支持生成随机文本、数字、布尔值、日期、图片、颜色等
- **拦截 Ajax 请求**：可以拦截并模拟 Ajax 请求，无需修改既有代码
- **模板语法简洁**：通过简单的语法即可生成复杂的数据结构
- **数据类型丰富**：支持生成各种类型的数据，满足不同场景需求

## 安装与引入

### CDN 引入

```html
<script src="https://cdn.bootcdn.net/ajax/libs/Mock.js/1.0.0/mock-min.js"></script>
```

### NPM 安装

```bash
npm install mockjs
```

```javascript
// Node.js 环境
const Mock = require('mockjs');
```

## 基本语法

### 1. 字符串规则

```javascript
// 'name|min-max': string  通过重复 string 生成一个字符串，重复次数大于等于 min，小于等于 max
Mock.mock({
  'name|2-5': 'abc'  // 生成 "abcabc" 或 "abcabcabc" 等，重复2-5次
});

// 'name|count': string  通过重复 string 生成一个字符串，重复次数为 count
Mock.mock({
  'name|3': 'abc'  // 生成 "abcabcabc"
});
```

### 2. 数字规则

```javascript
// 'name|+1': count  属性值自动加 1，初始值为 count
Mock.mock({
  'id|+1': 1  // 生成 1, 2, 3, 4...
});

// 'name|min-max': number  生成一个大于等于 min、小于等于 max 的整数
Mock.mock({
  'age|10-50': 0  // 生成 10-50 之间的整数
});

// 'name|min-max.dcount': number  生成一个浮点数，整数部分大于等于 min、小于等于 max，小数部分保留 dcount 位
Mock.mock({
  'price|1-100.2': 0  // 生成 1-100 之间的浮点数，保留2位小数
});
```

### 3. 数组规则

```javascript
// 'name|count': array  从属性值 array 中随机选取 1 个元素
Mock.mock({
  'favorite|1': ['篮球', '足球', '游泳']
});

// 'name|min-max': array  通过重复属性值 array 生成一个新数组，重复次数大于等于 min，小于等于 max
Mock.mock({
  'list|5-10': ['item']  // 生成包含 5-10 个 'item' 的数组
});

// 'name|count': array  通过重复属性值 array 生成一个新数组，重复次数为 count
Mock.mock({
  'list|3': ['item']  // 生成包含 3 个 'item' 的数组
});
```

## 内置数据占位符

Mock.js 提供了丰富的占位符，用于生成各种类型的随机数据。

### 基础类型

```javascript
Mock.mock({
  // 布尔值
  'boolean': '@boolean',  // 随机布尔值
  
  // 自然数
  'natural': '@natural',  // 随机自然数（大于等于 0 的整数）
  'natural|min-max': '@natural(10, 100)',  // 指定范围的自然数
  
  // 整数
  'integer': '@integer',  // 随机整数
  'integer|min-max': '@integer(-10, 10)',  // 指定范围的整数
  
  // 浮点数
  'float': '@float',  // 随机浮点数
  'float|min-max.dmin-dmax': '@float(1, 100, 2, 4)',  // 指定范围和小数位数的浮点数
  
  // 字符
  'character': '@character',  // 随机字符
  'string': '@string',  // 随机字符串
  'range': '@range(10, 20)',  // 数组，包含指定范围内的所有整数
});
```

### 日期时间

```javascript
Mock.mock({
  'date': '@date',  // 随机日期字符串，格式：yyyy-MM-dd
  'date-format': '@date("yyyy-MM-dd")',  // 指定格式的日期
  'time': '@time',  // 随机时间字符串，格式：HH:mm:ss
  'datetime': '@datetime',  // 随机日期时间字符串，格式：yyyy-MM-dd HH:mm:ss
  'now': '@now',  // 当前日期时间
});
```

### 图片

```javascript
Mock.mock({
  // 基本图片
  'image': '@image',  // 随机图片地址
  
  // 指定尺寸的图片
  'image-size': '@image("200x100")',  // 200x100 的图片
  
  // 指定背景色和前景色的图片
  'image-color': '@image("200x100", "#50B347", "#FFF")',  // 200x100，背景色 #50B347，前景色 #FFF
  
  // 完整参数的图片
  'image-full': '@image("200x100", "#50B347", "#FFF", "png", "文字")',
  // 参数：尺寸、背景色、前景色、格式、文字
});
```

### 文本相关

```javascript
Mock.mock({
  // 段落
  'paragraph': '@paragraph',  // 随机段落
  'paragraph-count': '@paragraph(2)',  // 2个随机段落
  
  // 句子
  'sentence': '@sentence',  // 随机句子
  'sentence-count': '@sentence(3)',  // 3个随机句子
  
  // 单词
  'word': '@word',  // 随机单词
  'word-count': '@word(5)',  // 5个随机单词
  
  // 标题
  'title': '@title',  // 随机标题
  
  // 中文相关
  'cparagraph': '@cparagraph',  // 随机中文段落
  'csentence': '@csentence',  // 随机中文句子
  'cword': '@cword',  // 随机中文字
  'ctitle': '@ctitle',  // 随机中文标题
});
```

### 姓名相关

```javascript
Mock.mock({
  // 英文姓名
  'first': '@first',  // 英文名
  'last': '@last',  // 英文姓
  'name': '@name',  // 英文全名
  
  // 中文姓名
  'cname': '@cname',  // 中文姓名
  
  // 其他
  'url': '@url',  // 随机 URL
  'email': '@email',  // 随机邮箱
  'ip': '@ip',  // 随机 IP 地址
  'region': '@region',  // 随机地区（如：华北）
  'province': '@province',  // 随机省份
  'city': '@city',  // 随机城市
  'city-prefix': '@city(true)',  // 随机城市（带省份前缀）
  'address': '@address',  // 随机完整地址
  'zip': '@zip',  // 随机邮政编码
  'guid': '@guid',  // 随机 GUID
  'id': '@id',  // 随机 18 位身份证号
});
```

### 颜色相关

```javascript
Mock.mock({
  'color': '@color',  // 随机颜色（十六进制表示）
  'rgb': '@rgb',  // 随机 RGB 颜色
  'rgba': '@rgba',  // 随机 RGBA 颜色  'hsl': '@hsl',  // 随机 HSL 颜色
});
```

## 拦截 Ajax 请求

Mock.js 可以拦截 Ajax 请求并返回模拟数据，这是其最核心的功能之一。

### 基本用法

```javascript
// 拦截 GET 请求
Mock.mock('/api/users', 'get', {
  code: 0,
  msg: 'success',
  'data|5-10': [
    {
      'id|+1': 1,
      name: '@cname',
      age: '@integer(18, 60)',
      email: '@email'
    }
  ]
});

// 拦截 POST 请求
Mock.mock('/api/users', 'post', {
  code: 0,
  msg: '创建成功'
});
```

### 完整示例

```javascript
// 引入必要的库
import Mock from 'mockjs';
import axios from 'axios';

// 拦截购物车接口
Mock.mock('/api/cart', 'get', {
  code: 0,           // 状态码，0表示成功
  msg: '',           // 错误消息
  'data|5-20': [     // 数组包含5-20个元素
    {
      productName: '@csentence',                                    // 商品名称（中文句子）
      productUrl: '@image(180x150, #008c8c, #fff, testimage)',      // 商品图片
      'unitPrice|1-500.2': 0,                                       // 单价（1-500元，保留2位小数）
      'count|1-10': 0                                               // 数量（1-10件）
    }
  ]
});

// 发送请求（会被拦截）
axios.get('/api/cart').then(response => {
  console.log(response.data);
  // 输出模拟的购物车数据
});
```

## 实战案例：购物车系统

### 1. 接口文档

```markdown
# 获取购物车数据

**请求路径**：/api/cart

**请求方法**：GET

**响应结果示例**：

```json
{
  "code": 0,        // 无错误
  "msg": "",        // 错误消息
  "data": [         // 主体数据
    {
      "productName": "iPhone 15 Pro",           // 商品名称
      "productUrl": "http://img.com/iphone15", // 商品图片URL地址
      "unitPrice": 8999.00,                    // 商品单价
      "count": 2                               // 购物数量
    }
  ]
}
```

### 2. Mock 数据配置

```javascript
// mock.js
Mock.mock('/api/cart', 'get', {
  code: 0,
  msg: '',
  'data|5-20': [  // 生成5-20个商品
    {
      productName: '@csentence',  // 随机中文商品名称
      productUrl: '@image(180x150, #008c8c, #fff, testimage)',  // 商品图片
      'unitPrice|1-500.2': 0,  // 单价：1-500元，保留2位小数
      'count|1-10': 0,          // 数量：1-10件
    },
  ],
});
```

### 3. 前端调用

```javascript
// index.js
$(function () {
  // 获取购物车数据
  axios.get('/api/cart').then((resp) => {
    const products = resp.data.data; // 获取到商品数据

    // 根据 products 生成 HTML 元素并加入到页面中
    const html = products
      .map((it) => `
        <div class="item">
          <div class="check">
            <input type="checkbox" class="checkItem" />
          </div>
          <div class="info">
            <img src="${it.productUrl}" alt="" />
            <a href="">${it.productName}</a>
          </div>
          <div class="price">
            <em>￥${it.unitPrice.toFixed(2)}</em>
          </div>
          <div class="num">
            <a href="" class="decr">-</a>
            <input type="text" value="${it.count}" class="txt" />
            <a href="" class="incr">+</a>
          </div>
          <div class="sum">
            <em>￥${(it.unitPrice * it.count).toFixed(2)}</em>
          </div>
          <div class="del">
            <a href="">删除</a>
          </div>
        </div>
      `)
      .join('');
    
    $('.list').html(html);

    // 设置汇总信息
    function setTotal() {
      let sum = 0;
      const checked = $(':checked:not(.checkAll)');
      
      // 计算选中商品的总价
      checked.each((i, dom) => {
        sum += +$(dom)
          .parents('.item')
          .find('.sum em')
          .text()
          .replace('￥', '');
      });
      
      // 更新显示
      $('.footer .right .nums em').text(checked.length);
      $('.footer .right .sums em').text(`￥${sum.toFixed(2)}`);
    }

    // 全选功能
    $('.checkAll').change(function () {
      $(':checkbox').not(this).prop('checked', $(this).prop('checked'));
      setTotal();
    });

    // 单个商品选择
    $('.checkItem').change(function () {
      setTotal();
    });

    // 增加数量
    $('.incr').click(function (e) {
      e.preventDefault();
      const inp = $(this).prevAll('input');
      const newNumber = +inp.val() + 1;
      changeNumber(newNumber, inp);
    });

    // 减少数量
    $('.decr').click(function (e) {
      e.preventDefault();
      const inp = $(this).nextAll('input');
      const newNumber = +inp.val() - 1;
      changeNumber(newNumber, inp);
    });

    // 改变商品数量
    function changeNumber(newNumber, inp) {
      if (newNumber < 1) {
        newNumber = 1;
      }
      inp.val(newNumber);
      
      // 重新计算小计
      const unitPrice = +inp
        .parents('.item')
        .find('.price em')
        .text()
        .replace('￥', '');
      const sum = unitPrice * newNumber;
      
      inp
        .parents('.item')
        .find('.sum em')
        .text(`￥${sum.toFixed(2)}`);
      setTotal();
    }

    // 删除单个商品
    $('.del a').click(function (e) {
      e.preventDefault();
      $(this).parents('.item').remove();
      setTotal();
    });

    // 删除选中商品
    $('.delChecked').click(function (e) {
      e.preventDefault();
      $(':checked:not(.checkAll)').parents('.item').remove();
      setTotal();
    });

    // 清空购物车
    $('.clearAll').click(function (e) {
      e.preventDefault();
      $('.item').remove();
      setTotal();
    });
  });
});
```

## 常用占位符速查表

| 占位符 | 说明 | 示例 |
|--------|------|------|
| `@boolean` | 布尔值 | true, false |
| `@natural` | 自然数 | 18, 25, 102 |
| `@integer` | 整数 | -12, 0, 56 |
| `@float` | 浮点数 | 12.35, 89.01 |
| `@string` | 字符串 | "xYzKk" |
| `@date` | 日期 | "2024-01-15" |
| `@time` | 时间 | "14:32:05" |
| `@datetime` | 日期时间 | "2024-01-15 14:32:05" |
| `@image` | 图片地址 | "http://dummyimage.com/200x100" |
| `@paragraph` | 段落 | "Dr. aliquip dolor..." |
| `@sentence` | 句子 | "Voluptatem exercitationem..." |
| `@word` | 单词 | "recusandae" |
| `@name` | 英文名 | "John Smith" |
| `@cname` | 中文姓名 | "张三" |
| `@email` | 邮箱 | "john@example.com" |
| `@url` | URL地址 | "http://smith.com/explore" |
| `@ip` | IP地址 | "192.168.1.100" |
| `@region` | 地区 | "华南" |
| `@province` | 省份 | "广东省" |
| `@city` | 城市 | "广州市" |
| `@address` | 完整地址 | "广东省广州市白云区..." |
| `@zip` | 邮编 | "510000" |
| `@color` | 颜色十六进制 | "#792b8f" |
| `@rgb` | RGB颜色 | "rgb(121, 43, 143)" |
| `@guid` | GUID | "662C5F26-73A5-4BEF-BE5E-1D4AF78C3E8E" |
| `@id` | 身份证号 | "420103198205149876" |

## 注意事项

1. **拦截顺序**：Mock.js 的拦截会先于真实的 Ajax 请求，开发完成后需要移除 Mock 代码
2. **数据类型**：占位符生成的是字符串类型，如需数字类型需使用数字规则
3. **性能考虑**：大量随机数据生成可能影响性能，建议合理设置数据量
4. **调试技巧**：可以配合浏览器的 Network 面板查看拦截情况
5. **生产环境**：确保在打包部署时不包含 Mock.js 代码

## 总结

MockJS 是前端开发中非常实用的数据模拟工具：

- **开发独立**：前端开发不依赖后端接口，提高开发效率
- **数据丰富**：通过占位符生成各种类型的随机数据
- **拦截简单**：无需修改现有代码，只需配置拦截规则
- **语法直观**：模板语法简洁明了，易于理解和维护

通过本章节的学习，你应该能够：
1. 掌握 Mock.js 的安装和基本语法
2. 熟练使用各种数据占位符
3. 拦截并模拟 Ajax 请求
4. 在实际项目中应用 Mock.js 进行数据模拟

## 练习

1. 使用 Mock.js 生成一个用户列表，包含姓名、年龄、邮箱、电话等信息
2. 模拟一个商品列表接口，包含商品名称、价格、库存、图片等字段
3. 结合 axios 和 Mock.js，实现一个完整的商品搜索功能
4. 模拟一个订单接口，包含订单号、商品列表、总价、状态等复杂结构