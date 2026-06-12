# 第9章 Fetch API

Fetch API 是现代 Web 开发中用于进行网络请求的新标准，它提供了更强大、更灵活的方式来处理 HTTP 请求，相比传统的 XMLHttpRequest 有显著的改进。

## 9-1 Fetch API 概述

### XMLHttpRequest 的问题

1. 所有的功能全部集中在同一个对象上，容易书写出混乱不易维护的代码
2. 采用传统的事件驱动模式，无法适配新的 Promise API

### Fetch API 的特点

1. 并非取代 AJAX，而是对 AJAX 传统 API 的改进
2. 精细的功能分割：头部信息、请求信息、响应信息等均分布到不同的对象，更利于处理各种复杂的 AJAX 场景
3. 使用 Promise API，更利于异步代码的书写
4. Fetch API 并非 ES6 的内容，属于 HTML5 新增的 Web API
5. 需要掌握网络通信的知识

## 9-2 基本使用

使用 `fetch` 函数即可立即向服务器发送网络请求。

### 基本语法

```javascript
fetch(url地址, 配置对象)
```

### 参数说明

1. **必填参数**：字符串，请求地址
2. **选填参数**：对象，请求配置

### 请求配置对象

- **method**：字符串，请求方法，默认值 GET
- **headers**：对象，请求头信息
- **body**：请求体的内容，必须匹配请求头中的 Content-Type
- **mode**：字符串，请求模式
  - `cors`：默认值，配置为该值，会在请求头中加入 origin 和 referer
  - `no-cors`：配置为该值，不会在请求头中加入 origin 和 referer，跨域的时候可能会出现问题
  - `same-origin`：指示请求必须在同一个域中发生，如果请求其他域，则会报错
- **credentials**：如何携带凭据（cookie）
  - `omit`：默认值，不携带 cookie
  - `same-origin`：请求同源地址时携带 cookie
  - `include`：请求任何地址都携带 cookie
- **cache**：配置缓存模式
  - `default`：表示 fetch 请求之前将检查下 http 的缓存
  - `no-store`：表示 fetch 请求将完全忽略 http 缓存的存在，请求之前将不再检查 http 缓存，拿到响应后也不会更新 http 缓存
  - `no-cache`：如果存在缓存，那么 fetch 将发送一个条件查询 request 和一个正常的 request，拿到响应后，它会更新 http 缓存
  - `reload`：表示 fetch 请求之前将忽略 http 缓存的存在，但是请求拿到响应后，它将主动更新 http 缓存
  - `force-cache`：表示 fetch 请求不顾一切的依赖缓存，即使缓存过期了，它依然从缓存中读取，除非没有任何缓存，那么它将发送一个正常的 request
  - `only-if-cached`：表示 fetch 请求不顾一切的依赖缓存，即使缓存过期了，它依然从缓存中读取，如果没有缓存，它将抛出网络错误（该设置只在 mode 为"same-origin"时有效）

### 返回值

fetch 函数返回一个 Promise 对象：

- 当收到服务器的返回结果后，Promise 进入 resolved 状态，状态数据为 Response 对象
- 当网络发生错误（或其他导致无法完成交互的错误）时，Promise 进入 rejected 状态，状态数据为错误信息

### Response 对象

Response 对象包含以下常用属性和方法：

- **ok**：boolean，当响应消息码在 200~299 之间时为 true，其他为 false
- **status**：number，响应的状态码
- **text()**：用于处理文本格式的 Ajax 响应，从响应中获取文本流，将其读完，然后返回一个被解决为 string 对象的 Promise
- **blob()**：用于处理二进制文件格式（比如图片或者电子表格）的 Ajax 响应，读取文件的原始数据，一旦读取完整个文件，就返回一个被解决为 blob 对象的 Promise
- **json()**：用于处理 JSON 格式的 Ajax 的响应，将 JSON 数据流转换为一个被解决为 JavaScript 对象的 promise
- **redirect()**：可以用于重定向到另一个 URL，它会创建一个新的 Promise，以解决来自重定向的 URL 的响应

### 基本使用示例

```javascript
async function getProvinces() {
  const url = 'http://study.yuanjin.tech/api/local';
  const resp = await fetch(url);
  const result = await resp.json();
  console.log(result);
}

document.querySelector('button').onclick = function () {
  getProvinces();
};
```

**代码说明：**
- 使用 `async/await` 语法简化异步操作
- `fetch(url)` 发送 GET 请求到指定地址
- `await resp.json()` 将响应数据解析为 JSON 对象
- 整个过程简洁明了，避免了传统回调地狱的问题

## 9-3 Request 对象

除了使用基本的 fetch 方法，还可以通过创建一个 Request 对象来完成请求（实际上，fetch 的内部会帮你创建一个 Request 对象）。

### 基本语法

```javascript
new Request(url地址, 配置)
```

### 重要注意点

尽量保证每次请求都是一个新的 Request 对象。

### Request 对象示例

```javascript
let req;

function getRequestInfo() {
  if (!req) {
    const url = 'http://study.yuanjin.tech/api/local';
    req = new Request(url, {});
    console.log(req);
  }
  return req.clone(); // 克隆一个全新的request对象，配置一致
}

async function getProvinces() {
  const resp = await fetch(getRequestInfo());
  const result = await resp.json();
  console.log(result);
}

document.querySelector('button').onclick = function () {
  getProvinces();
};
```

**代码说明：**
- 使用 `new Request()` 创建请求对象
- `req.clone()` 方法用于克隆 Request 对象，确保每次请求都使用新的对象
- 这种方式便于请求对象的复用和管理
- 将请求配置封装在 Request 对象中，使代码更加模块化

## 9-4 Response 对象

Response 对象用于表示 HTTP 响应，包含了响应的所有信息和数据处理方法。

### Response 对象的创建

```javascript
const resp = new Response(
  `[
    {"id":1, "name":"北京"},
    {"id":2, "name":"天津"}
  ]`,
  {
    ok: true,
    status: 200,
  }
);
```

### Response 对象使用示例

```javascript
async function getProvinces() {
  // 创建一个模拟的 Response 对象
  const resp = new Response(
    `[
      {"id":1, "name":"北京"},
      {"id":2, "name":"天津"}
    ]`,
    {
      ok: true,
      status: 200,
    }
  );
  const result = await getJSON(resp);
  console.log(result);
}

async function getJSON(resp) {
  const json = await resp.json();
  return json;
}

document.querySelector('button').onclick = function () {
  getProvinces();
};
```

**代码说明：**
- 可以手动创建 Response 对象用于测试或模拟服务器响应
- Response 构造函数接受两个参数：响应体和配置对象
- 配置对象可以设置 `ok`、`status` 等响应状态
- `resp.json()` 方法将响应体解析为 JSON 对象
- 这种方式便于单元测试和前端开发调试

## 9-5 Headers 对象

在 Request 和 Response 对象内部，会将传递的请求头对象转换为 Headers 对象。

### Headers 对象的常用方法

- **has(key)**：检查请求头中是否存在指定的 key 值
- **get(key)**：得到请求头中对应的 key 值
- **set(key, value)**：修改对应的键值对
- **append(key, value)**：添加对应的键值对
- **keys()**：得到所有的请求头键的集合
- **values()**：得到所有的请求头中的值的集合
- **entries()**：得到所有请求头中的键值对的集合

### Headers 对象使用示例

```javascript
function getCommonHeaders() {
  return new Headers({
    a: 1,
    b: 2,
  });
}

function printHeaders(headers) {
  const datas = headers.entries();
  for (const pair of datas) {
    console.log(`key: ${pair[0]}，value: ${pair[1]}`);
  }
}

function getRequestInfo() {
  if (!req) {
    const url = 'http://study.yuanjin.tech/api/local';
    const headers = getCommonHeaders();
    headers.set('a', 3); // 修改已有的请求头
    req = new Request(url, {
      headers,
    });
    printHeaders(headers);
  }
  return req.clone();
}

async function getProvinces() {
  const resp = await fetch(getRequestInfo());
  printHeaders(resp.headers); // 打印响应头
  const result = await getJSON(resp);
  console.log(result);
}

async function getJSON(resp) {
  const json = await resp.json();
  return json;
}

document.querySelector('button').onclick = function () {
  getProvinces();
};
```

**代码说明：**
- 使用 `new Headers()` 创建 Headers 对象
- `headers.set()` 方法用于修改请求头的值
- `headers.entries()` 返回一个迭代器，可以遍历所有键值对
- 在 Request 和 Response 中都可以访问 headers 属性
- Headers 对象提供了丰富的方法来操作 HTTP 头部信息
- 这种方式使得请求头的操作更加安全和规范

## 9-6 文件上传

文件上传是 Web 开发中的常见功能，Fetch API 提供了简洁的文件上传方式。

### 文件上传流程

1. 客户端将文件数据发送给服务器
2. 服务器保存上传的文件数据到服务器端
3. 服务器响应给客户端一个文件访问地址

### 测试环境

- 测试地址：`http://study.yuanjin.tech/api/upload`
- 键的名称（表单域名称）：`imagefile`

### 请求配置

- **请求方法**：POST
- **请求的表单格式**：multipart/form-data
- **请求体**：必须包含一个键值对，键的名称是服务器要求的名称，值是文件数据

### FormData 的使用

HTML5 中，JS 仍然无法随意的获取文件数据，但是可以获取到 input 元素中，被用户选中的文件数据。可以利用 HTML5 提供的 FormData 构造函数来创建请求体。

### 文件上传示例

```javascript
async function upload() {
  const inp = document.getElementById('avatar');
  if (inp.files.length === 0) {
    alert('请选择要上传的文件');
    return;
  }
  
  const formData = new FormData(); // 构建请求体
  formData.append('imagefile', inp.files[0]);
  
  const url = 'http://study.yuanjin.tech/api/upload';
  const resp = await fetch(url, {
    method: 'POST',
    body: formData, // 自动修改请求头
  });
  
  const result = await resp.json();
  return result;
}

document.querySelector('button').onclick = async function () {
  const result = await upload();
  const img = document.getElementById('imgAvatar');
  img.src = result.path; // 显示上传后的图片
};
```

**代码说明：**
- 使用 `input[type="file"]` 元素让用户选择文件
- 通过 `inp.files[0]` 获取用户选择的文件
- `new FormData()` 创建表单数据对象
- `formData.append()` 添加文件数据，键名必须与服务器要求一致
- Fetch 自动设置合适的 Content-Type 头（multipart/form-data）
- 上传成功后，服务器返回文件访问路径
- 将返回的路径设置给 img 元素显示上传的图片

### HTML 结构

```html
<img src="" alt="" id="imgAvatar" />
<input type="file" id="avatar" />
<button>上传</button>
```

**关键点：**
- FormData 对象简化了文件上传的实现
- 不需要手动设置 Content-Type，Fetch API 会自动处理
- 支持多种文件类型（图片、文档、视频等）
- 提供了良好的用户体验和错误处理机制

## 总结

Fetch API 作为现代化的网络请求标准，具有以下优势：

1. **更清晰的 API 设计**：功能分离到不同对象，职责明确
2. **Promise 支持**：更好地处理异步操作，避免回调地狱
3. **更强大的功能**：支持 Request、Response、Headers 等对象的精细控制
4. **更简洁的语法**：相比 XMLHttpRequest，代码更加简洁易懂
5. **更好的扩展性**：易于添加中间件、拦截器等功能

掌握 Fetch API 对于现代 Web 开发至关重要，它为构建高性能、用户体验优秀的 Web 应用提供了坚实基础。