# jQuery - 让DOM操作更简单

## 课程简介

jQuery是一个快速、小型且功能丰富的JavaScript库。它使得HTML文档遍历、事件处理、动画和Ajax等操作变得更加简单，具有易用的API，可在多种浏览器中运行。

**官方资源：**
- 官网：https://jquery.com/
- 中文网：https://jquery.cuishifeng.cn/
- CDN：https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js

## 为什么需要jQuery

在jQuery出现之前，开发者需要处理各种浏览器兼容性问题，代码冗长且复杂。jQuery的出现解决了这些痛点：

### 1. 浏览器兼容性
```javascript
// 传统JavaScript方式 - 需要处理兼容性
function addEvent(element, type, handler) {
  if (element.addEventListener) {
    element.addEventListener(type, handler, false);
  } else if (element.attachEvent) {
    element.attachEvent('on' + type, handler);
  } else {
    element['on' + type] = handler;
  }
}

// jQuery方式 - 自动处理兼容性
$(element).on(type, handler);
```

### 2. 代码简洁性
```javascript
// 传统方式 - 获取所有类名为item的元素
var items = document.getElementsByClassName('item');
for (var i = 0; i < items.length; i++) {
  items[i].style.display = 'none';
}

// jQuery方式 - 一行代码搞定
$('.item').hide();
```

### 3. 链式调用
```javascript
// jQuery支持链式调用，代码更优雅
$('.container')
  .addClass('active')
  .fadeIn(300)
  .find('button')
  .on('click', handleClick);
```

## jQuery核心概念

### 1. jQuery函数 `$`

jQuery提供了核心函数`jQuery`，简写为`$`。这是jQuery的入口点，可以用于：

#### 选择元素
```javascript
// 通过ID选择
$('#myId')

// 通过类名选择
$('.myClass')

// 通过标签名选择
$('div')

// 通过属性选择
$('[data-id="123"]')

// 组合选择
$('.container > .item.active')
```

#### 创建元素
```javascript
// 创建一个div元素
$('<div>')

// 创建带内容的元素
$('<a>', {
  text: '点击我',
  href: '#',
  class: 'btn'
})

// 创建并设置样式
$('<p>').text('Hello jQuery').css('color', 'red')
```

#### DOM加载完成事件
```javascript
// 等价于DOMContentLoaded
$(function() {
  // DOM加载完成后执行的代码
  console.log('DOM ready!');
});
```

### 2. jQuery对象 vs DOM对象

#### 区别与联系
```javascript
// jQuery对象 - 包含多个DOM元素的伪数组
var $items = $('.item');  // 返回jQuery对象

// DOM对象 - 单个HTML元素
var item = document.querySelector('.item');  // 返回DOM对象
```

#### 相互转换
```javascript
// jQuery对象转DOM对象
var $divs = $('div');
var firstDiv = $divs[0];        // 方式1：使用数组索引
var secondDiv = $divs.get(1);   // 方式2：使用get方法

// DOM对象转jQuery对象
var div = document.querySelector('div');
var $div = $(div);  // 用$包装DOM对象
```

#### 伪数组结构
```
jQuery对象 = [DOM元素1, DOM元素2, DOM元素3, ...]
           length: 3
           context: document
           selector: ".item"
```

## jQuery核心功能

### 1. 选择器 (Selectors)

jQuery支持CSS1-CSS3的所有选择器，以及一些自定义选择器：

#### 基本选择器
```javascript
$('#id')           // ID选择器
$('.class')        // 类选择器
$('element')       // 元素选择器
$('*')             // 通配符选择器
$('selector1, selector2')  // 群组选择器
```

#### 层级选择器
```javascript
$('.parent > .child')    // 子选择器（直接子元素）
$('.ancestor .descendant')  // 后代选择器
$('.prev + .next')       // 相邻兄弟选择器
$('.prev ~ .siblings')   // 兄弟选择器
```

#### 过滤选择器
```javascript
$('li:first')            // 第一个li元素
$('li:last')             // 最后一个li元素
$('li:even')             // 偶数位置的li元素（索引0,2,4...）
$('li:odd')              // 奇数位置的li元素（索引1,3,5...）
$('li:eq(2)')            // 索引为2的li元素
$('li:gt(2)')            // 索引大于2的li元素
$('li:lt(2)')            // 索引小于2的li元素
$('li:not(.active)')     // 不包含active类的li元素
```

#### 表单选择器
```javascript
$(':text')               // 所有文本框
$(':password')           // 所有密码框
$(':radio')              // 所有单选按钮
$(':checkbox')           // 所有复选框
$(':submit')             // 所有提交按钮
$(':checked')            // 所有被选中的元素
$(':selected')           // 所有被选中的option元素
$(':input')              // 所有input、textarea、select、button元素
```

### 2. 筛选 (Filtering)

在选择的基础上进一步筛选元素：

#### 基本筛选
```javascript
// 获取第一个元素
$('li').first()

// 获取最后一个元素  
$('li').last()

// 获取指定索引的元素
$('li').eq(2)

// 获取满足条件的元素
$('div').filter('.active')

// 移除满足条件的元素
$('div').not('.active')
```

#### 关系筛选
```javascript
// 查找子元素
$('.parent').find('.child')     // 查找所有后代中的.child
$('.parent').children('.child') // 只查找直接子元素中的.child

// 查找父元素
$('.child').parent()            // 直接父元素
$('.child').parents('.parent')  // 所有祖先元素中的.parent
$('.child').closest('.parent')  // 向上查找最近的.parent

// 查找兄弟元素
$('.item').next()               // 下一个兄弟元素
$('.item').prev()               // 上一个兄弟元素
$('.item').nextAll('.active')   // 后面所有.active的兄弟元素
$('.item').prevAll('.active')   // 前面所有.active的兄弟元素
$('.item').siblings('.active')  // 所有.active的兄弟元素
```

### 3. 文档处理 (DOM Manipulation)

#### 内部插入
```javascript
// 在元素内部末尾添加
$('.container').append('<div>新内容</div>')   // 把新内容添加到.container末尾
$('<div>新内容</div>').appendTo('.container') // 把新内容添加到.container末尾

// 在元素内部开头添加
$('.container').prepend('<div>新内容</div>')  // 把新内容添加到.container开头
$('<div>新内容</div>').prependTo('.container') // 把新内容添加到.container开头
```

#### 外部插入
```javascript
// 在元素外部后面添加
$('.item').after('<div>新内容</div>')        // 在.item后面添加新内容
$('<div>新内容</div>').insertAfter('.item')  // 在.item后面添加新内容

// 在元素外部前面添加
$('.item').before('<div>新内容</div>')       // 在.item前面添加新内容
$('<div>新内容</div>').insertBefore('.item') // 在.item前面添加新内容
```

#### 包裹和替换
```javascript
// 包裹元素
$('div').wrap('<div class="wrapper"></div>')      // 用wrapper包裹每个div
$('div').wrapAll('<div class="wrapper"></div>')   // 用wrapper包裹所有div
$('div').wrapInner('<span></span>')               // 用span包裹div的内容

// 替换元素
$('.old').replaceWith('<div class="new"></div>')  // 用新元素替换.old元素
$('<div class="new"></div>').replaceAll('.old')  // 用新元素替换.old元素
```

#### 删除和清空
```javascript
// 删除元素
$('.item').remove()              // 删除元素及其所有事件和数据
$('.item').detach()              // 删除元素但保留事件和数据

// 清空元素内容
$('.container').empty()          // 清空.container的所有子元素
$('.container').html('')         // 等价于empty()
```

#### 克隆元素
```javascript
// 克隆元素
$('.item').clone()                     // 克隆元素（不包含事件）
$('.item').clone(true)                 // 克隆元素（包含事件）
$('.item').clone().appendTo('.container') // 克隆并添加到.container
```

### 4. 属性操作 (Attributes)

#### 属性
```javascript
// 获取属性值
$('img').attr('src')              // 获取第一个img的src属性
$('a').attr('href')               // 获取第一个a的href属性

// 设置单个属性
$('img').attr('src', 'new.jpg')   // 设置所有img的src属性
$('a').attr('href', '#')          // 设置所有a的href属性

// 设置多个属性
$('img').attr({
  src: 'new.jpg',
  alt: '图片描述',
  title: '提示信息'
})

// 删除属性
$('img').removeAttr('title')      // 删除所有img的title属性
```

#### CSS类
```javascript
// 添加类
$('.item').addClass('active')     // 添加active类
$('.item').addClass('active highlight') // 添加多个类

// 删除类
$('.item').removeClass('active')  // 删除active类
$('.item').removeClass()          // 删除所有类

// 切换类
$('.item').toggleClass('active')  // 切换active类

// 判断是否包含类
$('.item').hasClass('active')     // 返回true/false
```

#### 值和内容
```javascript
// 获取/设置表单元素的值
$('input').val()                  // 获取第一个input的值
$('input').val('新值')            // 设置所有input的值
$('select').val()                 // 获取select的值
$('textarea').val('新内容')       // 设置textarea的值

// 获取/设置文本内容
$('div').text()                   // 获取所有div的文本内容（合并）
$('div').text('新文本')           // 设置所有div的文本内容（转义HTML）

// 获取/设置HTML内容
$('div').html()                   // 获取第一个div的HTML内容
$('div').html('<strong>新内容</strong>') // 设置所有div的HTML内容
```

#### 表单属性
```javascript
// 获取/设置checked属性
$('input[type="checkbox"]').prop('checked')  // 获取checked状态
$('input[type="checkbox"]').prop('checked', true) // 设置为选中

// 获取/设置disabled属性
$('input').prop('disabled')       // 获取disabled状态
$('input').prop('disabled', true) // 设置为禁用

// 注意：attr vs prop
// attr() 用于HTML属性（初始值）
// prop() 用于DOM属性（当前值，如checked、selected、disabled）
```

### 5. CSS操作 (Styles)

#### 样式
```javascript
// 获取样式
$('div').css('color')             // 获取第一个div的color样式

// 设置单个样式
$('div').css('color', 'red')      // 设置所有div的color为red

// 设置多个样式
$('div').css({
  color: 'red',
  'font-size': '16px',
  backgroundColor: 'blue'
})

// 获取计算后的样式
$('div').css(['width', 'height']) // 获取多个样式
```

#### 尺寸
```javascript
// 获取宽度
$('div').width()                  // 内容宽度（不包括padding、border、margin）
$('div').innerWidth()             // 内容宽度 + padding
$('div').outerWidth()             // 内容宽度 + padding + border
$('div').outerWidth(true)         // 内容宽度 + padding + border + margin

// 获取高度
$('div').height()                 // 内容高度（不包括padding、border、margin）
$('div').innerHeight()            // 内容高度 + padding
$('div').outerHeight()            // 内容高度 + padding + border
$('div').outerHeight(true)        // 内容高度 + padding + border + margin

// 设置宽高
$('div').width(200)               // 设置宽度为200px
$('div').height('100px')          // 设置高度为100px
```

#### 位置
```javascript
// 获取相对位置（相对于offsetParent）
$('div').position().left
$('div').position().top

// 获取绝对位置（相对于document）
$('div').offset().left
$('div').offset().top

// 设置绝对位置
$('div').offset({left: 100, top: 200})

// 获取滚动位置
$(window).scrollTop()             // 获取页面滚动距离
$(window).scrollLeft()            // 获取页面水平滚动距离

// 设置滚动位置
$(window).scrollTop(0)            // 滚动到页面顶部
$('div').scrollLeft(100)          // 设置div的水平滚动位置
```

### 6. 事件处理 (Events)

#### 事件绑定
```javascript
// 简写事件（常用事件）
$('button').click(function() {
  console.log('按钮被点击');
});

$('input').focus(function() {
  console.log('输入框获得焦点');
});

// 通用事件绑定
$('.item').on('click', function() {
  console.log('元素被点击');
});

// 事件委托（动态元素）
$('.container').on('click', '.item', function() {
  console.log('container内的.item被点击');
});

// 绑定多个事件
$('.item').on({
  click: function() { console.log('点击'); },
  mouseenter: function() { console.log('鼠标进入'); },
  mouseleave: function() { console.log('鼠标离开'); }
});

// 一次性事件
$('button').one('click', function() {
  console.log('只会触发一次');
});
```

#### 事件解绑
```javascript
// 解除所有事件
$('button').off();

// 解除指定事件
$('button').off('click');

// 解除指定处理函数
function handleClick() { /*...*/ }
$('button').on('click', handleClick);
$('button').off('click', handleClick);
```

#### 事件对象
```javascript
$('.item').click(function(event) {
  // 阻止默认行为
  event.preventDefault();
  
  // 阻止事件冒泡
  event.stopPropagation();
  
  // 阻止默认行为和冒泡（简写）
  event.isDefaultPrevented();
  
  // 获取事件目标
  console.log(event.target);        // 触发事件的元素
  console.log(event.currentTarget);  // 绑定事件的元素
  
  // 获取鼠标位置
  console.log(event.pageX);          // 相对于文档的X坐标
  console.log(event.pageY);          // 相对于文档的Y坐标
  
  // 获取按键信息
  console.log(event.which);          // 按钮编码（1=左键，2=中键，3=右键）
});
```

#### 常用事件
```javascript
// 鼠标事件
.click()          // 点击
.dblclick()       // 双击
.mousedown()      // 鼠标按下
.mouseup()        // 鼠标松开
.mousemove()      // 鼠标移动
.mouseenter()     // 鼠标进入（不冒泡）
.mouseleave()     // 鼠标离开（不冒泡）
.mouseover()      // 鼠标悬停（冒泡）
.mouseout()       // 鼠标移出（冒泡）
.hover()          // 鼠标进入和离开

// 键盘事件
.keydown()        // 按键按下
.keyup()          // 按键松开
.keypress()       // 按键按下并松开

// 表单事件
.focus()          // 获得焦点
.blur()           // 失去焦点
.change()         // 值改变
.submit()         // 表单提交
.select()         // 文本被选中

// 文档/窗口事件
.ready()          // DOM加载完成
.load()           // 元素加载完成
.unload()         // 页面卸载
.resize()         // 窗口大小改变
.scroll()         // 滚动事件
```

#### 触发事件
```javascript
// 程序触发事件
$('button').click();              // 触发点击事件
$('form').submit();              // 触发表单提交

// 传递自定义数据
$('.item').trigger('click', {data: 'custom'});
$('.item').on('click', function(event, customData) {
  console.log(customData.data);   // 输出: custom
});
```

### 7. 动画效果 (Effects)

#### 基本动画
```javascript
// 显示/隐藏
$('div').hide()                   // 隐藏元素（display:none）
$('div').show()                  // 显示元素
$('div').toggle()                // 切换显示/隐藏

// 带动画参数
$('div').hide(1000)              // 1秒内隐藏
$('div').hide('slow')            // 慢速隐藏（600ms）
$('div').hide('fast')            // 快速隐藏（200ms）
$('div').hide(1000, function() {
  console.log('隐藏完成');
});

// 淡入淡出
$('div').fadeIn()                // 淡入
$('div').fadeOut()               // 淡出
$('div').fadeToggle()            // 切换淡入淡出
$('div').fadeTo(1000, 0.5)       // 1秒内淡出到透明度0.5

// 滑动
$('div').slideDown()            // 向下滑动展开
$('div').slideUp()              // 向上滑动收起
$('div').slideToggle()          // 切换滑动
```

#### 自定义动画
```javascript
// 基本动画
$('div').animate({
  width: '200px',
  height: '100px',
  opacity: 0.5
}, 1000);

// 相对值动画
$('div').animate({
  left: '+=50px',    // 当前left值 + 50px
  opacity: '-=0.2'   // 当前透明度 - 0.2
}, 1000);

// 动画参数
$('div').animate({
  width: '200px'
}, {
  duration: 1000,           // 动画时长
  easing: 'swing',          // 缓动函数（swing, linear）
  complete: function() {    // 动画完成回调
    console.log('动画完成');
  },
  step: function(now, fx) { // 每一步回调
    console.log('当前值:', now);
  },
  queue: true,              // 是否加入动画队列
  specialEasing: {
    width: 'linear',
    height: 'easeOutBounce'
  }
});
```

#### 动画控制
```javascript
// 停止动画
$('div').stop()                    // 停止当前动画
$('div').stop(true)               // 停止所有动画队列
$('div').stop(true, true)         // 停止并跳转到最终状态
$('div').finish()                 // 完成所有动画

// 延迟
$('div').delay(1000).fadeOut()    // 延迟1秒后淡出

// 关闭动画
$.fx.off = true;                  // 全局关闭所有动画
```

### 8. Ajax

jQuery提供了简洁的Ajax API，虽然现在被axios等库超越，但在维护老项目时仍然重要。

#### GET请求
```javascript
// 简单GET请求
$.get('https://api.example.com/data', function(data) {
  console.log(data);
});

// 带参数的GET请求
$.get('https://api.example.com/search', {
  keyword: 'jquery',
  page: 1
}, function(data) {
  console.log(data);
});

// 完整参数
$.get('https://api.example.com/data', {
  id: 123
}, function(data) {
  console.log('成功:', data);
}).fail(function(error) {
  console.log('失败:', error);
}).always(function() {
  console.log('完成');
});
```

#### POST请求
```javascript
// 简单POST请求
$.post('https://api.example.com/user', {
  name: '张三',
  age: 25
}, function(data) {
  console.log(data);
});

// JSON格式
$.post('https://api.example.com/user', 
  JSON.stringify({name: '张三', age: 25}),
  function(data) {
    console.log(data);
  },
  'json'  // 期望的响应类型
);
```

#### ajax方法
```javascript
// 完整配置
$.ajax({
  url: 'https://api.example.com/data',
  type: 'GET',
  dataType: 'json',           // 期望的响应数据类型
  data: {                    // 请求参数
    id: 123
  },
  timeout: 5000,             // 超时时间
  beforeSend: function(xhr) {
    // 请求发送前
    xhr.setRequestHeader('Authorization', 'token');
  },
  success: function(data) {
    console.log('成功:', data);
  },
  error: function(xhr, status, error) {
    console.log('失败:', error);
  },
  complete: function() {
    console.log('完成');
  }
});
```

## 实战案例：购物车

下面是一个完整的购物车功能实现，展示了jQuery的实际应用。

### HTML结构
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>购物车jQuery版</title>
  <link rel="stylesheet" href="./style.css">
</head>
<body>
  <div id="cart">
    <!-- 购物车头部 -->
    <div class="header">
      <div class="check">
        <input type="checkbox" class="checkAll">
        全选
      </div>
      <div class="info">商品信息</div>
      <div class="price">单价</div>
      <div class="num">数量</div>
      <div class="sum">金额</div>
      <div class="del">操作</div>
    </div>

    <!-- 商品列表 -->
    <div class="list">
      <div class="item">
        <div class="check">
          <input type="checkbox" class="checkItem">
        </div>
        <div class="info">
          <img src="https://img.alicdn.com/bao/uploaded/i2/3817310001/O1CN01qiU0Yt1BsV2LpfHvc_!!3817310001.jpg_80x80.jpg" alt="">
          <a href="">酷冷至尊 冰神B360ARGB电脑散热器白色 台式主机水冷散热器</a>
        </div>
        <div class="price"><em>￥599.00</em></div>
        <div class="num">
          <a href="" class="decr">-</a>
          <input type="text" value="1" class="txt">
          <a href="" class="incr">+</a>
        </div>
        <div class="sum"><em>￥599.00</em></div>
        <div class="del">
          <a href="">删除</a>
        </div>
      </div>
      <!-- 更多商品项... -->
    </div>

    <!-- 购物车底部 -->
    <div class="footer">
      <div class="check">
        <input type="checkbox" class="checkAll">
        全选
      </div>
      <div class="operation">
        <a href="" class="delChecked">删除选中商品</a>
        <a href="" class="clearAll">清空购物车</a>
      </div>
      <div class="right">
        <div class="nums">
          已选<em>0</em>件商品
        </div>
        <div class="sums">
          合计：<em>￥0.00</em>
        </div>
        <div class="done">
          <a href="">结算</a>
        </div>
      </div>
    </div>
  </div>
  <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
  <script src="./index.js"></script>
</body>
</html>
```

### JavaScript实现
```javascript
/**
 * 购物车功能 - jQuery实现
 * 功能：全选、数量修改、删除、价格计算
 */
$(function() {
  /**
   * 计算并更新汇总信息
   * 统计选中商品数量和总金额
   */
  function setTotal() {
    let sum = 0;
    // 获取所有被选中的商品复选框（排除全选框）
    const checked = $(':checked:not(.checkAll)');
    
    // 遍历选中的商品，计算总金额
    checked.each((i, dom) => {
      // 提取每个商品的价格（去除￥符号并转换为数字）
      const price = $(dom)
        .parents('.item')
        .find('.sum em')
        .text()
        .replace('￥', '');
      sum += +price;
    });
    
    // 更新选中的商品数量
    $('.footer .right .nums em').text(checked.length);
    
    // 更新总金额（保留两位小数）
    $('.footer .right .sums em').text(`￥${sum.toFixed(2)}`);
  }

  /**
   * 全选/全不选功能
   */
  $('.checkAll').change(function() {
    // 设置所有商品复选框的选中状态与全选框一致
    $(':checkbox').not(this).prop('checked', $(this).prop('checked'));
    // 更新汇总信息
    setTotal();
  });

  /**
   * 单个商品复选框变化
   */
  $('.checkItem').change(function() {
    setTotal();
  });

  /**
   * 增加商品数量
   */
  $('.incr').click(function(e) {
    e.preventDefault(); // 阻止链接默认行为
    const inp = $(this).prevAll('input'); // 获取数量输入框
    const newNumber = +inp.val() + 1;     // 数量+1
    changeNumber(newNumber, inp);
  });

  /**
   * 减少商品数量
   */
  $('.decr').click(function(e) {
    e.preventDefault(); // 阻止链接默认行为
    const inp = $(this).nextAll('input'); // 获取数量输入框
    const newNumber = +inp.val() - 1;     // 数量-1
    changeNumber(newNumber, inp);
  });

  /**
   * 修改商品数量并更新金额
   * @param {number} newNumber - 新的数量
   * @param {jQuery} inp - 数量输入框的jQuery对象
   */
  function changeNumber(newNumber, inp) {
    // 数量不能小于1
    if (newNumber < 1) {
      newNumber = 1;
    }
    
    // 更新数量输入框的值
    inp.val(newNumber);
    
    // 获取单价
    const unitPrice = +inp
      .parents('.item')
      .find('.price em')
      .text()
      .replace('￥', '');
    
    // 计算并更新该商品的金额
    const sum = unitPrice * newNumber;
    inp
      .parents('.item')
      .find('.sum em')
      .text(`￥${sum.toFixed(2)}`);
    
    // 更新汇总信息
    setTotal();
  }

  /**
   * 删除单个商品
   */
  $('.del a').click(function(e) {
    e.preventDefault(); // 阻止链接默认行为
    // 找到并删除该商品行
    $(this).parents('.item').remove();
    // 更新汇总信息
    setTotal();
  });

  /**
   * 删除选中的商品
   */
  $('.delChecked').click(function(e) {
    e.preventDefault(); // 阻止链接默认行为
    // 找到所有被选中的商品并删除
    $(':checked:not(.checkAll)').parents('.item').remove();
    // 更新汇总信息
    setTotal();
  });

  /**
   * 清空购物车
   */
  $('.clearAll').click(function(e) {
    e.preventDefault(); // 阻止链接默认行为
    // 删除所有商品
    $('.item').remove();
    // 更新汇总信息
    setTotal();
  });
});
```

### 代码要点解析

1. **jQuery选择器使用**
   - `$(':checked:not(.checkAll)')` - 选择所有被选中的复选框（排除全选框）
   - `$(this).prevAll('input')` - 查找前面所有同级元素中的input
   - `$(this).nextAll('input')` - 查找后面所有同级元素中的input
   - `$(this).parents('.item')` - 向上查找最近的.item祖先元素

2. **事件处理**
   - `change事件` - 复选框状态改变时触发
   - `click事件` - 链接点击时触发
   - `e.preventDefault()` - 阻止链接的默认跳转行为

3. **DOM操作**
   - `.prop('checked', true/false)` - 设置复选框选中状态
   - `.text()` - 获取/设置文本内容
   - `.val()` - 获取/设置表单值
   - `.remove()` - 删除元素

4. **数据计算**
   - `.replace('￥', '')` - 去除货币符号
   - `+string` - 将字符串转换为数字
   - `.toFixed(2)` - 保留两位小数

5. **链式调用**
   ```javascript
   inp
     .parents('.item')           // 向上查找.item
     .find('.sum em')             // 在.item内查找.sum em
     .text(`￥${sum.toFixed(2)}`); // 设置文本
   ```

## jQuery性能优化

### 1. 选择器优化
```javascript
// 优化前：从右向左解析，性能较差
$('.container div.item p.title')

// 优化后：使用find方法，性能更好
$('.container').find('div.item p.title')

// 优化前：使用复杂选择器
$(':visible')

// 优化后：使用过滤方法
$('div').filter(':visible')
```

### 2. 缓存jQuery对象
```javascript
// 优化前：重复查询DOM
$('.item').addClass('active');
$('.item').css('color', 'red');
$('.item').show();

// 优化后：缓存jQuery对象
var $item = $('.item');
$item.addClass('active');
$item.css('color', 'red');
$item.show();

// 或使用链式调用
$('.item').addClass('active').css('color', 'red').show();
```

### 3. 批量DOM操作
```javascript
// 优化前：多次DOM操作，频繁重绘
for (var i = 0; i < 100; i++) {
  $('.container').append('<div>Item ' + i + '</div>');
}

// 优化后：先构建HTML，一次性插入
var html = '';
for (var i = 0; i < 100; i++) {
  html += '<div>Item ' + i + '</div>';
}
$('.container').html(html);

// 或使用文档片段
var fragment = document.createDocumentFragment();
for (var i = 0; i < 100; i++) {
  var div = document.createElement('div');
  div.textContent = 'Item ' + i;
  fragment.appendChild(div);
}
$('.container').append(fragment);
```

### 4. 事件委托
```javascript
// 优化前：为每个元素绑定事件（不适用于动态添加的元素）
$('.item').on('click', function() {
  // ...
});

// 优化后：使用事件委托
$('.container').on('click', '.item', function() {
  // 适用于动态添加的.item元素
});
```

### 5. 减少DOM操作
```javascript
// 优化前：多次操作class
$('.item').removeClass('old');
$('.item').addClass('new');

// 优化后：一次性操作
$('.item').removeClass('old').addClass('new');

// 或使用toggleClass
$('.item').toggleClass('old new');
```

## jQuery与现代开发

### jQuery的优势
1. **学习曲线平缓** - API简单直观，适合初学者
2. **浏览器兼容性** - 自动处理各种浏览器差异
3. **代码简洁** - 用少量代码实现复杂功能
4. **生态完善** - 丰富的插件和社区支持
5. **维护老项目** - 大量现有项目使用jQuery

### jQuery的局限
1. **性能问题** - 相比原生JS和现代框架，性能较差
2. **包体积** - 压缩后约30KB，比原生解决方案大
3. **链式调用** - 虽然简洁，但可能影响代码调试
4. **现代框架** - Vue、React等提供了更好的状态管理和组件化

### 何时使用jQuery
- ✅ 维护现有jQuery项目
- ✅ 需要快速实现简单交互效果
- ✅ 需要兼容旧版浏览器
- ✅ 使用jQuery插件生态
- ❌ 新的大型单页应用
- ❌ 需要高性能的应用
- ❌ 现代前端工程化项目

## 学习建议

1. **掌握核心概念**
   - 理解jQuery对象和DOM对象的区别
   - 熟悉选择器和筛选方法
   - 掌握常用API的使用

2. **实践项目**
   - 从简单案例开始（如轮播图、手风琴）
   - 逐步实现复杂功能（如购物车、表单验证）
   - 学习jQuery插件的使用和开发

3. **对比学习**
   - 对比原生JS和jQuery的实现方式
   - 理解jQuery如何简化DOM操作
   - 学习现代框架的类似功能

4. **阅读源码**
   - 学习jQuery的设计思想
   - 理解其兼容性处理方式
   - 借鉴其API设计理念

## 总结

jQuery作为一个经典的JavaScript库，极大地简化了前端开发，特别是DOM操作和事件处理。虽然现代前端开发更多使用Vue、React等框架，但jQuery的核心思想和设计理念仍然值得我们学习。

**核心要点：**
1. 掌握jQuery函数`$`的使用
2. 理解jQuery对象和DOM对象的转换
3. 熟练使用选择器和筛选方法
4. 掌握事件处理和动画效果
5. 了解性能优化和最佳实践

**扩展学习：**
- jQuery插件开发
- jQuery UI组件库
- jQuery Mobile移动开发
- 相关工具库（Lodash、Axios等）

继续深入学习第三方库，如Axios、ECharts等，将帮助你更好地构建现代化的Web应用。