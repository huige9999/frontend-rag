# 06. HTML5文档

## 学习目标

- 理解HTML5文档结构的语义化
- 掌握HTML5新增的多媒体元素
- 掌握HTML5新增的文章结构元素
- 理解自定义数据属性的使用
- 掌握常用的HTML5 Web API

## HTML5概述

HTML5包含两个部分的更新，分别是**文档**和**Web API**

- **文档部分**：新增语义化标签、多媒体标签等，让HTML结构更清晰、更具含义
- **Web API部分**：新增JavaScript接口，提供更强的浏览器交互能力

## 元素语义化

### 什么是元素语义化

元素语义化是指**每个HTML元素都代表着某种含义，在开发中应该根据元素含义选择元素**

### 语义化的好处

1. **利于SEO（搜索引擎优化）**：搜索引擎能更好地理解页面内容结构
2. **利于无障碍访问**：屏幕阅读器等辅助工具能更好地解析页面
3. **利于浏览器插件分析网页**：便于工具提取页面关键信息

### 语义化使用原则

```html
<!-- 不推荐：使用div布局 -->
<div class="header">头部</div>
<div class="article">文章</div>
<div class="footer">页脚</div>

<!-- 推荐：使用语义化标签 -->
<header>头部</header>
<article>文章</article>
<footer>页脚</footer>
```

## HTML5新增元素

### 多媒体元素

#### audio元素 - 音频播放

```html
<!-- 音频元素示例 -->
<audio src="audio.mp3" controls autoplay loop muted></audio>
```

#### video元素 - 视频播放

```html
<!-- 视频元素示例 -->
<video src="video.mp4" controls width="300"></video>
```

#### 多媒体元素属性

| 属性名   | 含义                 | 类型     | 说明                                 |
| -------- | -------------------- | -------- | ------------------------------------ |
| src      | 多媒体的文件路径     | 普通属性 | 指定音频/视频文件的地址              |
| controls | 是否显示播放控件     | 布尔属性 | 显示播放、暂停、进度条等控制按钮     |
| autoplay | 是否自动播放         | 布尔属性 | 页面加载后自动开始播放               |
| loop     | 是否循环播放         | 布尔属性 | 播放结束后自动重新开始               |
| muted    | 静音播放             | 布尔属性 | 默认静音状态（通常配合autoplay使用） |

> **注意**：新版浏览器不允许「带声音的自动播放」，可能将来甚至不允许自动播放。浏览器希望播放行为由用户决定。

#### 课堂代码示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>多媒体元素示例</title>
  <style>
    video {
      width: 300px;
    }
  </style>
</head>
<body>
  <!-- 加载视频文件：controls显示控件 autoplay自动播放 muted静音 -->
  <video src="open.mp4" controls autoplay muted></video>
  
  <!-- 加载音频文件：controls显示控件 autoplay自动播放 -->
  <audio src="shamoluotuo.mp3" controls autoplay></audio>
</body>
</html>
```

### 文章结构元素

为了让搜索引擎和浏览器更好地理解文档内容，HTML5新增了多个元素来表达内容的含义。

#### 主要结构元素

| 元素名   | 含义                           | 使用场景                     |
| -------- | ------------------------------ | ---------------------------- |
| article  | 表示一篇完整的文章内容         | 博客文章、新闻报道、论坛帖子  |
| header   | 表示头部信息                   | 页面头部、文章头部、区块头部 |
| footer   | 表示页脚信息                   | 页面页脚、文章页脚、区块页脚 |
| section  | 表示文档中的章节或区块         | 文章章节、内容区块           |
| aside    | 表示与主内容相关的侧边内容     | 侧边栏、相关文章、作者信息   |
| nav      | 表示导航链接                   | 主导航、侧边导航             |
| blockquote | 表示引用内容                | 引用他人的话或文献           |
| cite     | 表示引用的作品或文献           | 书名、文章名、链接           |

#### 文章结构示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>HTML5文章结构示例</title>
</head>
<body>
  <!-- article：一篇完整的文章 -->
  <article>
    <!-- header：文章头部信息，包含标题、作者等 -->
    <header>
      <h1>HTML5语义化标签详解</h1>
      <p>发布时间：2024-01-01</p>
      <!-- blockquote：引用信息 -->
      <blockquote>
        <p>本文参考了W3C的HTML5规范文档</p>
      </blockquote>
    </header>
    
    <!-- aside：文章的附加信息，如作者、浏览量等 -->
    <aside>
      <div class="author-info">
        <span>作者：张三</span>
        <span>分类：前端开发</span>
        <span>浏览量：1234</span>
      </div>
    </aside>
    
    <!-- section：文章的主要章节 -->
    <section>
      <h2>第一章：什么是语义化</h2>
      <p>语义化是指使用恰当的HTML标签来描述内容的含义...</p>
      <p>这样做的好处包括利于SEO、无障碍访问等...</p>
    </section>
    
    <section>
      <h2>第二章：常用语义化标签</h2>
      <p>HTML5提供了丰富的语义化标签，如header、footer等...</p>
    </section>
    
    <section>
      <h2>第三章：实际应用</h2>
      <p>在实际项目中，我们应该根据内容含义选择合适的标签...</p>
    </section>
    
    <!-- footer：文章的页脚信息 -->
    <footer>
      <h3>参考资料</h3>
      <ul>
        <li><cite>W3C HTML5规范</cite></li>
        <li><cite>MDN Web Docs</cite></li>
        <li><cite>HTML5权威指南</cite></li>
      </ul>
      <p>版权所有 © 2024</p>
    </footer>
  </article>
</body>
</html>
```

## HTML5新增属性

### 自定义数据属性（data-*）

自定义数据属性允许我们在HTML元素上存储额外的数据，这些数据不会影响元素的显示或行为，但可以通过JavaScript访问。

#### 使用方法

```html
<!-- 语法：data-* 形式，*可以是任意名称 -->
<div id="app" data-user-id="123" data-user-name="张三" data-age="25">用户信息</div>

<script>
  const div = document.getElementById('app');
  
  // 方法1：使用getAttribute（传统方式）
  const userId = div.getAttribute('data-user-id');
  console.log(userId); // "123"
  
  // 方法2：使用dataset属性（推荐）
  // dataset会自动将data-*转换为驼峰命名
  console.log(div.dataset); // { userId: "123", userName: "张三", age: "25" }
  console.log(div.dataset.userId); // "123"
  console.log(div.dataset.userName); // "张三"
  console.log(div.dataset.age); // "25"
  
  // 修改data属性
  div.dataset.age = "26"; // <div data-age="26">
  
  // 添加新的data属性
  div.dataset.email = "zhangsan@example.com"; // <div data-email="zhangsan@example.com">
</script>
```

#### 完整示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>自定义数据属性示例</title>
  <style>
    .product-card {
      border: 1px solid #ddd;
      padding: 20px;
      margin: 10px 0;
      border-radius: 5px;
    }
    .product-name {
      font-size: 18px;
      font-weight: bold;
    }
    .product-price {
      color: #e44d26;
      font-size: 20px;
    }
  </style>
</head>
<body>
  <!-- 使用data-*属性存储商品信息 -->
  <div class="product-card" 
       data-id="1001" 
       data-name="笔记本电脑" 
       data-price="5999" 
       data-category="电子产品"
       data-stock="50">
    <div class="product-name">笔记本电脑</div>
    <div class="product-price">¥5999</div>
    <button onclick="showProductInfo(this)">查看详情</button>
  </div>
  
  <script>
    function showProductInfo(button) {
      // 获取商品卡片元素
      const card = button.parentElement;
      
      // 使用dataset访问所有data-*属性
      const product = {
        id: card.dataset.id,
        name: card.dataset.name,
        price: card.dataset.price,
        category: card.dataset.category,
        stock: card.dataset.stock
      };
      
      // 显示商品详细信息
      alert(`商品详情：
ID: ${product.id}
名称: ${product.name}
价格: ¥${product.price}
分类: ${product.category}
库存: ${product.stock}件`);
    }
  </script>
</body>
</html>
```

#### 应用场景

1. **存储配置信息**：保存元素的配置参数
2. **传递数据**：在元素和JavaScript之间传递数据
3. **状态管理**：保存元素的当前状态
4. **数据分析**：配合前端埋点统计用户行为

### input元素的新增属性

HTML5为input元素增加了许多新的type和属性，提供了更丰富的表单控制能力。

> **详细参考**：[MDN input详细文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input)

#### 新的type类型

| type值   | 用途                   | 示例                               |
| -------- | ---------------------- | ---------------------------------- |
| email    | 邮箱输入框             | `<input type="email">`             |
| tel      | 电话输入框             | `<input type="tel">`               |
| url      | 网址输入框             | `<input type="url">`               |
| number   | 数字输入框             | `<input type="number" min="0" max="100">` |
| range    | 滑块选择器             | `<input type="range" min="0" max="100">` |
| color    | 颜色选择器             | `<input type="color">`             |
| date     | 日期选择器             | `<input type="date">`              |
| time     | 时间选择器             | `<input type="time">`              |
| datetime-local | 日期时间选择器 | `<input type="datetime-local">`   |
| search   | 搜索框（带清除按钮）   | `<input type="search">`            |

#### 新增属性

| 属性名       | 用途                           | 示例                                        |
| ------------ | ------------------------------ | ------------------------------------------- |
| placeholder  | 输入框占位提示文本             | `<input placeholder="请输入用户名">`         |
| required     | 必填项                         | `<input required>`                          |
| pattern      | 正则表达式验证                 | `<input pattern="[0-9]{6}">`                |
| min/max      | 最小值/最大值                  | `<input type="number" min="1" max="100">`   |
| step         | 步长（数字、日期等）           | `<input type="number" step="0.1">`          |
| maxlength    | 最大字符长度                   | `<input maxlength="20">`                    |
| autocomplete | 自动完成                       | `<input autocomplete="off">`                |
| autofocus    | 页面加载时自动获得焦点         | `<input autofocus>`                         |
| list         | 关联datalist元素，提供建议选项 | `<input list="suggestions">`                |
| multiple     | 允许多个值（邮箱、文件等）     | `<input type="email" multiple>`             |

#### 表单验证示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>HTML5表单验证示例</title>
  <style>
    .form-group {
      margin: 15px 0;
    }
    label {
      display: inline-block;
      width: 100px;
    }
    input, select {
      padding: 8px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    button {
      padding: 10px 20px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h2>用户注册表单</h2>
  <form id="registerForm">
    <!-- 邮箱输入：自动验证邮箱格式 -->
    <div class="form-group">
      <label for="email">邮箱：</label>
      <input type="email" id="email" name="email" 
             placeholder="请输入邮箱" required>
    </div>
    
    <!-- 密码输入：必填，最小长度6位 -->
    <div class="form-group">
      <label for="password">密码：</label>
      <input type="password" id="password" name="password" 
             minlength="6" required 
             placeholder="至少6位字符">
    </div>
    
    <!-- 年龄：数字类型，范围18-120 -->
    <div class="form-group">
      <label for="age">年龄：</label>
      <input type="number" id="age" name="age" 
             min="18" max="120" step="1" 
             placeholder="18-120">
    </div>
    
    <!-- 生日：日期选择器 -->
    <div class="form-group">
      <label for="birthday">生日：</label>
      <input type="date" id="birthday" name="birthday">
    </div>
    
    <!-- 个人网站：URL格式验证 -->
    <div class="form-group">
      <label for="website">个人网站：</label>
      <input type="url" id="website" name="website" 
             placeholder="https://example.com">
    </div>
    
    <!-- 电话号码：使用pattern正则验证 -->
    <div class="form-group">
      <label for="phone">手机号：</label>
      <input type="tel" id="phone" name="phone" 
             pattern="[0-9]{11}" 
             placeholder="11位手机号"
             title="请输入11位手机号码">
    </div>
    
    <!-- 颜色选择 -->
    <div class="form-group">
      <label for="favoriteColor">喜欢的颜色：</label>
      <input type="color" id="favoriteColor" name="favoriteColor" value="#007bff">
    </div>
    
    <!-- 滑块选择 -->
    <div class="form-group">
      <label for="satisfaction">满意度：</label>
      <input type="range" id="satisfaction" name="satisfaction" 
             min="1" max="5" step="1" value="3">
      <span id="satisfactionValue">3</span>
    </div>
    
    <!-- 自动完成的搜索框 -->
    <div class="form-group">
      <label for="search">搜索：</label>
      <input type="search" id="search" name="search" 
             placeholder="搜索内容..." list="searchSuggestions">
      <datalist id="searchSuggestions">
        <option value="HTML5教程">
        <option value="CSS3教程">
        <option value="JavaScript教程">
        <option value="前端开发">
      </datalist>
    </div>
    
    <button type="submit">注册</button>
  </form>
  
  <script>
    // 滑块值实时显示
    const satisfactionInput = document.getElementById('satisfaction');
    const satisfactionValue = document.getElementById('satisfactionValue');
    
    satisfactionInput.addEventListener('input', function() {
      satisfactionValue.textContent = this.value;
    });
    
    // 表单提交处理
    document.getElementById('registerForm').addEventListener('submit', function(e) {
      e.preventDefault(); // 阻止默认提交
      
      // 检查表单验证状态
      if (this.checkValidity()) {
        alert('注册成功！');
        console.log('表单数据：', new FormData(this));
      } else {
        alert('请检查表单填写是否正确！');
      }
    });
  </script>
</body>
</html>
```

## HTML5 Web API

HTML5不仅增加了新的HTML元素，还提供了强大的JavaScript API，让我们能够更好地控制浏览器行为和页面交互。

### 使用CSS选择器选中元素

HTML5提供了简洁的API来使用CSS选择器选中DOM元素。

```javascript
// 使用CSS选择器选中匹配的第一个元素
const element = document.querySelector('.container .item');
console.log(element); // 返回第一个匹配的元素，如果没有匹配返回null

// 使用CSS选择器选中匹配的所有元素，返回NodeList（伪数组）
const elements = document.querySelectorAll('.item');
console.log(elements); // 返回所有匹配的元素的NodeList
console.log(elements.length); // 匹配元素的数量

// 遍历所有匹配的元素
elements.forEach(function(element) {
  console.log(element);
});

// 常用选择器示例
document.querySelector('#myId'); // ID选择器
document.querySelector('.myClass'); // 类选择器
document.querySelector('div'); // 标签选择器
document.querySelector('[data-id="123"]'); // 属性选择器
document.querySelectorAll('li.active'); // 组合选择器
```

### 控制类样式（classList API）

HTML5提供了简洁的API来操作元素的class属性。

```javascript
const element = document.querySelector('.my-element');

// 添加类样式
element.classList.add('active');  // <div class="my-element active">
element.classList.add('highlight');  // <div class="my-element active highlight">

// 添加多个类
element.classList.add('class1', 'class2', 'class3');

// 是否包含某个类样式
const hasActive = element.classList.contains('active');  // true
const hasDisabled = element.classList.contains('disabled');  // false

// 移除类样式
element.classList.remove('highlight');  // <div class="my-element active">

// 移除多个类
element.classList.remove('class1', 'class2');

// 切换类样式（有则删除，无则添加）
element.classList.toggle('active'); 
// 如果有active类：删除 <div class="my-element">
// 如果没有active类：添加 <div class="my-element active">

// 替换类样式
element.classList.replace('old-class', 'new-class');
```

#### 完整示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>classList API示例</title>
  <style>
    .card {
      width: 200px;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 5px;
      text-align: center;
      transition: all 0.3s;
    }
    .card.active {
      background-color: #007bff;
      color: white;
      border-color: #0056b3;
    }
    .card.highlight {
      box-shadow: 0 0 10px rgba(255, 193, 7, 0.5);
      border-color: #ffc107;
    }
    .card.disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  </style>
</head>
<body>
  <div class="card" id="myCard">
    <h3>卡片标题</h3>
    <p>这是一张卡片</p>
    <button onclick="toggleActive()">切换激活状态</button>
    <button onclick="toggleHighlight()">切换高亮</button>
    <button onclick="checkClasses()">检查类名</button>
  </div>
  
  <script>
    const card = document.getElementById('myCard');
    
    // 切换active类
    function toggleActive() {
      card.classList.toggle('active');
      console.log('当前类名：', card.className);
    }
    
    // 切换highlight类
    function toggleHighlight() {
      card.classList.toggle('highlight');
      console.log('当前类名：', card.className);
    }
    
    // 检查类名
    function checkClasses() {
      const hasActive = card.classList.contains('active');
      const hasHighlight = card.classList.contains('highlight');
      
      console.log('包含active类：', hasActive);
      console.log('包含highlight类：', hasHighlight);
      
      alert(`包含active类：${hasActive}\n包含highlight类：${hasHighlight}`);
    }
  </script>
</body>
</html>
```

### 本地存储（Web Storage）

HTML5提供了本地存储API，允许在浏览器中保存键值对数据。

#### localStorage - 永久存储

localStorage中的数据会永久保存，除非手动清除或用户清除浏览器数据。

```javascript
// 保存数据
localStorage.setItem('username', '张三');
localStorage.setItem('userAge', '25');

// 读取数据
const username = localStorage.getItem('username'); // "张三"
const userAge = localStorage.getItem('userAge'); // "25"
const notExist = localStorage.getItem('notExist'); // null

// 删除指定数据
localStorage.removeItem('username');

// 清除所有数据
localStorage.clear();

// 获取存储的项目数量
console.log(localStorage.length); // 1

// 根据索引获取键名
const key = localStorage.key(0); // "userAge"
```

#### sessionStorage - 会话存储

sessionStorage中的数据仅在当前会话有效，关闭标签页或浏览器后数据会被清除。

```javascript
// 保存数据
sessionStorage.setItem('tempData', '临时数据');

// 读取数据
const tempData = sessionStorage.getItem('tempData'); // "临时数据"

// 删除数据
sessionStorage.removeItem('tempData');

// 清除所有数据
sessionStorage.clear();
```

#### 存储对象和数组

Web Storage只能存储字符串，如果需要存储对象或数组，需要使用JSON进行转换。

```javascript
// 存储对象
const user = {
  id: 1,
  name: '张三',
  age: 25,
  hobbies: ['阅读', '运动', '编程']
};

// 将对象转换为JSON字符串存储
localStorage.setItem('user', JSON.stringify(user));

// 读取并解析JSON字符串
const savedUser = JSON.parse(localStorage.getItem('user'));
console.log(savedUser.name); // "张三"
console.log(savedUser.hobbies); // ["阅读", "运动", "编程"]

// 存储数组
const todos = [
  { id: 1, text: '学习HTML5', completed: true },
  { id: 2, text: '学习CSS3', completed: false },
  { id: 3, text: '学习JavaScript', completed: false }
];

localStorage.setItem('todos', JSON.stringify(todos));

// 读取并使用数组
const savedTodos = JSON.parse(localStorage.getItem('todos'));
savedTodos.forEach(todo => {
  console.log(todo.text + ': ' + todo.completed);
});
```

#### 完整示例：待办事项管理

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>本地存储示例：待办事项</title>
  <style>
    .todo-app {
      max-width: 400px;
      margin: 50px auto;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
    .todo-input {
      display: flex;
      gap: 10px;
      margin-bottom: 20px;
    }
    .todo-input input {
      flex: 1;
      padding: 8px;
      border: 1px solid #ddd;
      border-radius: 4px;
    }
    .todo-input button {
      padding: 8px 15px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .todo-list {
      list-style: none;
      padding: 0;
    }
    .todo-item {
      display: flex;
      align-items: center;
      padding: 10px;
      border-bottom: 1px solid #eee;
    }
    .todo-item.completed span {
      text-decoration: line-through;
      color: #999;
    }
    .todo-item input {
      margin-right: 10px;
    }
    .todo-item button {
      margin-left: auto;
      padding: 5px 10px;
      background-color: #dc3545;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="todo-app">
    <h2>待办事项</h2>
    <div class="todo-input">
      <input type="text" id="todoInput" placeholder="输入待办事项...">
      <button onclick="addTodo()">添加</button>
    </div>
    <ul class="todo-list" id="todoList">
      <!-- 待办事项将在这里动态生成 -->
    </ul>
    <button onclick="clearAll()">清空所有</button>
  </div>
  
  <script>
    // 初始化：从localStorage读取待办事项
    let todos = JSON.parse(localStorage.getItem('todos')) || [];
    
    // 渲染待办事项列表
    function renderTodos() {
      const todoList = document.getElementById('todoList');
      todoList.innerHTML = '';
      
      todos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        li.innerHTML = `
          <input type="checkbox" ${todo.completed ? 'checked' : ''} 
                 onchange="toggleTodo(${todo.id})">
          <span>${todo.text}</span>
          <button onclick="deleteTodo(${todo.id})">删除</button>
        `;
        
        todoList.appendChild(li);
      });
    }
    
    // 添加待办事项
    function addTodo() {
      const input = document.getElementById('todoInput');
      const text = input.value.trim();
      
      if (text) {
        const newTodo = {
          id: Date.now(),
          text: text,
          completed: false
        };
        
        todos.push(newTodo);
        saveTodos();
        renderTodos();
        input.value = '';
      }
    }
    
    // 切换完成状态
    function toggleTodo(id) {
      const todo = todos.find(t => t.id === id);
      if (todo) {
        todo.completed = !todo.completed;
        saveTodos();
        renderTodos();
      }
    }
    
    // 删除待办事项
    function deleteTodo(id) {
      todos = todos.filter(t => t.id !== id);
      saveTodos();
      renderTodos();
    }
    
    // 清空所有
    function clearAll() {
      if (confirm('确定要清空所有待办事项吗？')) {
        todos = [];
        saveTodos();
        renderTodos();
      }
    }
    
    // 保存到localStorage
    function saveTodos() {
      localStorage.setItem('todos', JSON.stringify(todos));
    }
    
    // 页面加载时渲染列表
    renderTodos();
  </script>
</body>
</html>
```

### 渲染帧（requestAnimationFrame）

浏览器会不断对网页进行渲染，通常速度为每秒60次，每一次渲染称之为**一帧**。因此浏览器的渲染速率通常是60帧/秒（60FPS）。

#### 为什么需要requestAnimationFrame

使用setInterval实现动画时，由于帧率的浮动，可能导致动画不够流畅。requestAnimationFrame解决了这个问题，它确保回调函数在每一帧渲染之前执行。

#### 基本用法

```javascript
// 在下一帧渲染之前执行回调函数
requestAnimationFrame(function() {
  console.log('下一帧渲染前执行');
});

// 返回一个请求ID，可以用于取消动画
const requestId = requestAnimationFrame(callback);

// 取消动画
cancelAnimationFrame(requestId);
```

#### 实现流畅动画

```javascript
// 动画函数：在下一帧渲染前执行一次元素状态变化
function animateOnce() {
  requestAnimationFrame(function() {
    // 检查动画是否应该停止
    if (animationShouldStop) {
      return;
    }
    
    // 改变元素状态
    changeElementState();
    
    // 继续注册下一帧的变化
    animateOnce();
  });
}

// 开始动画
animateOnce();
```

#### 完整示例：移动动画

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>requestAnimationFrame动画示例</title>
  <style>
    .container {
      width: 100%;
      height: 200px;
      border: 2px solid #ddd;
      position: relative;
      overflow: hidden;
    }
    .box {
      width: 50px;
      height: 50px;
      background-color: #007bff;
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      border-radius: 5px;
    }
    .controls {
      margin: 20px 0;
      text-align: center;
    }
    button {
      padding: 10px 20px;
      margin: 0 5px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button.stop {
      background-color: #dc3545;
    }
    .info {
      text-align: center;
      margin: 10px 0;
    }
  </style>
</head>
<body>
  <h2>requestAnimationFrame动画示例</h2>
  <div class="container">
    <div class="box" id="box"></div>
  </div>
  <div class="controls">
    <button onclick="startAnimation()">开始移动</button>
    <button onclick="stopAnimation()" class="stop">停止</button>
  </div>
  <div class="info" id="info">位置: 0px</div>
  
  <script>
    const box = document.getElementById('box');
    const info = document.getElementById('info');
    
    let position = 0; // 当前位置
    let animationId = null; // 动画请求ID
    let isAnimating = false; // 动画状态
    
    // 动画函数
    function animate() {
      // 更新位置
      position += 2;
      
      // 如果超出容器宽度，重置位置
      if (position > window.innerWidth - 50) {
        position = 0;
      }
      
      // 更新元素位置
      box.style.left = position + 'px';
      info.textContent = '位置: ' + position + 'px';
      
      // 继续下一帧
      animationId = requestAnimationFrame(animate);
    }
    
    // 开始动画
    function startAnimation() {
      if (!isAnimating) {
        isAnimating = true;
        animate();
      }
    }
    
    // 停止动画
    function stopAnimation() {
      if (isAnimating) {
        cancelAnimationFrame(animationId);
        isAnimating = false;
      }
    }
  </script>
</body>
</html>
```

#### requestAnimationFrame的优势

1. **与浏览器刷新同步**：确保动画在最佳时间执行，更流畅
2. **节省资源**：页面不可见时（如切换标签页）自动暂停
3. **更精确的计时**：使用浏览器内部时钟，比setInterval更准确

### 音视频API

HTML5为video和audio元素提供了丰富的JavaScript API，让开发者可以完全控制媒体播放。

#### 音视频属性

| 属性名       | 类型    | 说明                                                         |
| ------------ | ------- | ------------------------------------------------------------ |
| currentTime  | Number  | 当前播放时间（秒），可读写。设置值会跳转到指定时间           |
| duration     | Number  | 总时长（秒），只读                                           |
| loop         | Boolean | 是否循环播放                                                 |
| controls     | Boolean | 是否显示播放控件                                             |
| src          | String  | 媒体文件地址                                                 |
| volume       | Number  | 音量（0.0静音到1.0最大音量）                                 |
| playbackRate | Number  | 播放倍速（1.0为正常速度，2.0为2倍速）                        |
| paused       | Boolean | 是否处于暂停状态，只读                                       |
| muted        | Boolean | 是否静音                                                     |
| ended        | Boolean | 是否播放结束，只读                                           |

#### 音视频方法

| 方法名       | 说明               |
| ------------ | ------------------ |
| play()       | 开始播放媒体       |
| pause()      | 暂停播放媒体       |
| load()       | 重新加载媒体       |

#### 音视频事件

| 事件名     | 说明                                   |
| ---------- | -------------------------------------- |
| play       | 开始播放时触发                         |
| pause      | 暂停时触发                             |
| ended      | 播放结束时触发                         |
| timeupdate | currentTime变化时触发（播放过程中不断触发） |
| loadeddata | 第一帧数据加载完成后触发               |
| canplay    | 媒体可以播放时触发                     |
| volumechange | 音量变化时触发                        |

#### 完整示例：视频播放器

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>视频播放器示例</title>
  <style>
    .video-player {
      max-width: 800px;
      margin: 50px auto;
    }
    video {
      width: 100%;
      border-radius: 5px;
    }
    .controls {
      display: flex;
      gap: 10px;
      margin-top: 15px;
      align-items: center;
    }
    .controls button {
      padding: 8px 15px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .controls button:disabled {
      background-color: #ccc;
      cursor: not-allowed;
    }
    .progress {
      flex: 1;
      height: 5px;
      background-color: #ddd;
      border-radius: 5px;
      cursor: pointer;
      position: relative;
    }
    .progress-bar {
      height: 100%;
      background-color: #007bff;
      border-radius: 5px;
      width: 0%;
    }
    .time-display {
      min-width: 120px;
      text-align: center;
      font-family: monospace;
    }
    .volume-control {
      display: flex;
      align-items: center;
      gap: 5px;
    }
    .volume-control input {
      width: 80px;
    }
    .speed-control {
      display: flex;
      align-items: center;
      gap: 5px;
    }
  </style>
</head>
<body>
  <div class="video-player">
    <video id="video" src="video.mp4" preload="metadata"></video>
    
    <div class="controls">
      <button id="playBtn">播放</button>
      <button id="pauseBtn" disabled>暂停</button>
      
      <div class="progress" id="progress">
        <div class="progress-bar" id="progressBar"></div>
      </div>
      
      <div class="time-display">
        <span id="currentTime">00:00</span> / 
        <span id="duration">00:00</span>
      </div>
      
      <div class="volume-control">
        <span>音量:</span>
        <input type="range" id="volume" min="0" max="1" step="0.1" value="1">
        <span id="volumeValue">100%</span>
      </div>
      
      <div class="speed-control">
        <span>倍速:</span>
        <select id="playbackRate">
          <option value="0.5">0.5x</option>
          <option value="1" selected>1.0x</option>
          <option value="1.5">1.5x</option>
          <option value="2">2.0x</option>
        </select>
      </div>
    </div>
    
    <div id="status" style="margin-top: 10px; text-align: center;"></div>
  </div>
  
  <script>
    const video = document.getElementById('video');
    const playBtn = document.getElementById('playBtn');
    const pauseBtn = document.getElementById('pauseBtn');
    const progress = document.getElementById('progress');
    const progressBar = document.getElementById('progressBar');
    const currentTimeDisplay = document.getElementById('currentTime');
    const durationDisplay = document.getElementById('duration');
    const volumeControl = document.getElementById('volume');
    const volumeValue = document.getElementById('volumeValue');
    const playbackRateSelect = document.getElementById('playbackRate');
    const status = document.getElementById('status');
    
    // 格式化时间显示
    function formatTime(seconds) {
      const mins = Math.floor(seconds / 60);
      const secs = Math.floor(seconds % 60);
      return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
    }
    
    // 播放按钮
    playBtn.addEventListener('click', function() {
      video.play();
    });
    
    // 暂停按钮
    pauseBtn.addEventListener('click', function() {
      video.pause();
    });
    
    // 视频播放事件
    video.addEventListener('play', function() {
      playBtn.disabled = true;
      pauseBtn.disabled = false;
      status.textContent = '正在播放...';
    });
    
    // 视频暂停事件
    video.addEventListener('pause', function() {
      playBtn.disabled = false;
      pauseBtn.disabled = true;
      status.textContent = '已暂停';
    });
    
    // 视频结束事件
    video.addEventListener('ended', function() {
      playBtn.disabled = false;
      pauseBtn.disabled = true;
      status.textContent = '播放结束';
    });
    
    // 时间更新事件
    video.addEventListener('timeupdate', function() {
      const current = video.currentTime;
      const duration = video.duration;
      
      // 更新进度条
      const progressPercent = (current / duration) * 100;
      progressBar.style.width = progressPercent + '%';
      
      // 更新时间显示
      currentTimeDisplay.textContent = formatTime(current);
    });
    
    // 视频元数据加载完成
    video.addEventListener('loadedmetadata', function() {
      durationDisplay.textContent = formatTime(video.duration);
      status.textContent = '视频已加载，点击播放';
    });
    
    // 点击进度条跳转
    progress.addEventListener('click', function(e) {
      const rect = progress.getBoundingClientRect();
      const clickX = e.clientX - rect.left;
      const percent = clickX / rect.width;
      
      video.currentTime = percent * video.duration;
    });
    
    // 音量控制
    volumeControl.addEventListener('input', function() {
      video.volume = this.value;
      volumeValue.textContent = Math.round(this.value * 100) + '%';
    });
    
    // 播放速度控制
    playbackRateSelect.addEventListener('change', function() {
      video.playbackRate = parseFloat(this.value);
    });
  </script>
</body>
</html>
```

## 参考资源

- [HTML5元素表](http://www.html5star.com/manual/html5label-meaning/)
- [MDN HTML文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML)
- [MDN input详细文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input)
- [MDN音视频API](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement)

## 课堂练习

### 练习1：使用多媒体元素

在页面中加载一个视频文件和一个音频文件，要求：
- 视频显示播放控件，自动播放但静音
- 音频显示播放控件，不自动播放

### 练习2：创建语义化文章

使用HTML5语义化标签创建一篇博客文章，包含：
- 文章头部（标题、作者、发布时间）
- 多个章节
- 侧边栏（相关链接）
- 页脚（版权信息、参考资料）

### 练习3：自定义数据属性

创建一个商品列表，使用data-*属性存储商品信息，点击按钮时显示商品详情。

### 练习4：本地存储

创建一个简单的记事本应用，能够：
- 添加新的笔记
- 显示所有笔记
- 删除笔记
- 数据保存在localStorage中

## 总结

HTML5为Web开发带来了重大改进：

**文档方面**：
- 新增语义化标签，提高页面的可读性和可访问性
- 新增多媒体标签，简化音视频的嵌入
- 新增表单属性，增强表单验证能力
- 支持自定义数据属性，便于数据传递

**API方面**：
- CSS选择器API，简化元素选择
- classList API，便捷的类名操作
- 本地存储，实现数据持久化
- requestAnimationFrame，实现流畅动画
- 音视频API，完全控制媒体播放

掌握这些新特性，能够让我们开发出更加现代化、用户体验更好的Web应用。
