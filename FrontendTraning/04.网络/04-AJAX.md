# AJAX

## 知识点概述

AJAX（Asynchronous JavaScript and XML）是一种创建交互式网页应用的网页开发技术。通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新，这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

## 核心概念

### 1. AJAX 的作用

- 不刷新页面更新网页内容
- 异步与服务器通信
- 提升用户体验，减少服务器负载

### 2. 传统请求 vs AJAX

| 特性 | 传统请求 | AJAX |
|------|---------|------|
| 页面刷新 | 需要 | 不需要 |
| 用户体验 | 较差 | 流畅 |
| 服务器负载 | 较高 | 较低 |
| 数据传输 | 完整页面 | 仅需要的数据 |

## XMLHttpRequest

### 基本使用

XMLHttpRequest 是 AJAX 的核心对象，用于在后台与服务器交换数据。

```javascript
// 1. 创建 XMLHttpRequest 对象
var xhr = new XMLHttpRequest();

// 2. 监听请求状态变化
xhr.onreadystatechange = function () {
  // readyState 状态值：
  // 0: 请求未初始化
  // 1: 服务器连接已建立（open方法已被调用）
  // 2: 请求已接收（send方法已被调用）
  // 3: 请求处理中（正在接收服务器的响应消息体）
  // 4: 请求已完成，且响应已就绪（服务器响应的所有内容均已接收完毕）
  
  if (xhr.readyState === 4) {
    // 请求完成，处理响应数据
    console.log('服务器的响应结果已经全部收到');
    const data = JSON.parse(xhr.responseText);
    // 处理数据...
  }
};

// 3. 配置请求（请求方法、URL、是否异步）
xhr.open('GET', 'http://localhost:7001/api/herolist');

// 4. 设置请求头（可选）
// xhr.setRequestHeader('Content-Type', 'application/json');

// 5. 发送请求
xhr.send(null); // GET请求没有请求体，传递null
```

### 完整示例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>AJAX - XMLHttpRequest示例</title>
  </head>
  <body>
    <h2>英雄列表</h2>
    <ul id="heroList"></ul>
    
    <script>
      // 创建发送请求的对象
      var xhr = new XMLHttpRequest();
      
      // 当请求状态发生改变时运行的函数
      xhr.onreadystatechange = function () {
        // xhr.readyState：一个数字，用于判断请求到了哪个阶段
        // 1: open方法已被调用
        // 2: send方法已被调用
        // 3: 正在接收服务器的响应消息体
        // 4: 服务器响应的所有内容均已接收完毕
        
        if (xhr.readyState === 4) {
          console.log('服务器的响应结果已经全部收到');
          
          // 解析JSON响应数据
          const obj = JSON.parse(xhr.responseText);
          const heroes = obj.data;
          
          // 将数据渲染到页面
          const ul = document.querySelector('ul');
          for (const h of heroes) {
            var li = document.createElement('li');
            li.innerText = h.cname;
            ul.appendChild(li);
          }
        }
        
        // xhr.responseText：获取服务器响应的消息体文本
        // xhr.getResponseHeader("Content-Type") 获取响应头Content-Type
      };
      
      // 配置请求
      xhr.open('GET', 'http://localhost:7001/api/herolist');
      
      // 设置请求头（可选）
      // xhr.setRequestHeader('Content-Type', 'application/json');
      
      // 构建请求体，发送到服务器（GET请求没有请求体，传递null）
      xhr.send(null);
    </script>
  </body>
</html>
```

## Fetch API

### 基本使用

Fetch API 是现代浏览器提供的用于网络请求的接口，基于 Promise，比 XMLHttpRequest 更简洁、更强大。

```javascript
// fetch 返回的是一个 Promise，当收到服务器的响应头之后，Promise 完成
// 完成之后，会给予一个响应对象 Response
async function getHeroes() {
  const resp = await fetch('http://localhost:7001/api/herolist');
  return await resp.json();
}

getHeroes().then((resp) => {
  console.log(resp);
});
```

### GET 请求示例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Fetch API - GET示例</title>
  </head>
  <body>
    <h2>英雄列表</h2>
    <ul id="heroList"></ul>
    
    <script>
      // 使用 fetch 发送 GET 请求
      // fetch 返回的是一个 Promise，当收到服务器的响应头之后，Promise 完成
      // 完成之后，会给予一个响应对象 Response
      async function getHeroes() {
        const resp = await fetch('http://localhost:7001/api/herolist');
        return await resp.json();
      }

      // 调用函数并处理结果
      getHeroes().then((resp) => {
        console.log(resp);
        // 将数据渲染到页面
        const ul = document.querySelector('#heroList');
        resp.data.forEach(hero => {
          const li = document.createElement('li');
          li.innerText = hero.cname;
          ul.appendChild(li);
        });
      });
    </script>
  </body>
</html>
```

### POST 请求示例

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Fetch API - POST示例</title>
  </head>
  <body>
    <h2>用户登录</h2>
    <p>
      账号：
      <input type="text" id="txtLoginId" />
    </p>
    <p>
      密码：
      <input type="password" id="txtLoginPwd" />
    </p>
    <p>
      <button>登录</button>
    </p>

    <script>
      const btn = document.querySelector('button');
      const txtLoginId = document.querySelector('#txtLoginId');
      const txtLoginPwd = document.querySelector('#txtLoginPwd');

      // 按钮点击事件
      btn.onclick = async function () {
        // 获取用户输入
        const loginId = txtLoginId.value,
              loginPwd = txtLoginPwd.value;
        
        // 构建请求体对象
        const obj = {
          loginId,
          loginPwd,
        };
        
        // 使用 fetch 发送 POST 请求
        const resp = await fetch('http://localhost:7001/api/user/login', {
          method: 'POST',                    // 请求方法
          headers: {                         // 请求头
            'Content-Type': 'application/json',
            a: 123,                          // 自定义请求头
          },
          body: JSON.stringify(obj),         // 请求体，需要转换为 JSON 字符串
        });
        
        // 解析响应数据
        const body = await resp.json();
        
        // 处理响应结果
        if (body.code) {
          alert(body.msg);  // 登录失败
        } else {
          alert('登录成功'); // 登录成功
        }
      };
    </script>
  </body>
</html>
```

## XMLHttpRequest vs Fetch API

### 对比表格

| 特性 | XMLHttpRequest | Fetch API |
|------|---------------|-----------|
| API 风格 | 事件驱动 | Promise |
| 代码简洁度 | 较复杂 | 简洁 |
| 配置方式 | 分步骤配置 | 配置对象 |
| 响应处理 | 需手动解析 | 提供 JSON 方法 |
| 浏览器支持 | 所有浏览器 | 现代浏览器 |
| 拦截器 | 需手动实现 | 可配合 Service Worker |

### 选择建议

- **新项目**：优先使用 Fetch API
- **旧项目维护**：继续使用 XMLHttpRequest
- **需要兼容老浏览器**：使用 XMLHttpRequest 或添加 Fetch polyfill

## 常见应用场景

### 1. 数据加载

```javascript
// 加载用户数据
async function loadUsers() {
  const response = await fetch('/api/users');
  const users = await response.json();
  return users;
}
```

### 2. 表单提交

```javascript
// 提交表单数据
async function submitForm(formData) {
  const response = await fetch('/api/submit', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(formData),
  });
  return await response.json();
}
```

### 3. 文件上传

```javascript
// 上传文件
async function uploadFile(file) {
  const formData = new FormData();
  formData.append('file', file);
  
  const response = await fetch('/api/upload', {
    method: 'POST',
    body: formData,
  });
  return await response.json();
}
```

## 错误处理

### Fetch API 错误处理

```javascript
async function fetchData(url) {
  try {
    const response = await fetch(url);
    
    // 检查 HTTP 状态码
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('请求失败:', error);
    // 处理错误
  }
}
```

## 注意事项

### 1. 同源策略

AJAX 请求受到浏览器同源策略的限制，只能访问同源（相同协议、域名、端口）的资源。

### 2. CORS 跨域

如需跨域请求，服务器需要设置 CORS 响应头：

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type
```

### 3. 安全性

- 验证用户输入
- 使用 HTTPS
- 防止 CSRF 攻击
- 敏感数据不要在前端存储

## 总结

- AJAX 可以在不刷新页面的情况下更新内容
- XMLHttpRequest 是传统的 AJAX 实现方式
- Fetch API 是现代的、基于 Promise 的网络请求接口
- 选择合适的工具取决于项目需求和浏览器兼容性
- 注意同源策略和跨域问题
- 做好错误处理和安全防护