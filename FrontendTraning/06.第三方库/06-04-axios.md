# Axios - HTTP 客户端库

## 1. Axios 简介

Axios 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Node.js 环境。它是目前最流行的 HTTP 请求库之一，提供了简洁的 API 和强大的功能。

### 主要特点

- **支持 Promise API**：可以使用 async/await 语法
- **自动转换 JSON 数据**：响应数据自动解析为 JavaScript 对象
- **请求和响应拦截器**：统一处理请求和响应
- **请求取消**：支持取消请求功能
- **并发请求**：支持批量请求
- **客户端支持防御 XSRF**：内置安全机制

### 安装方式

```html
<!-- CDN 引入 -->
<script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js"></script>
```

```bash
# npm 安装
npm install axios

# yarn 安装
yarn add axios
```

## 2. 基本用法

### 2.1 GET 请求

```javascript
// 基本 GET 请求
axios.get('https://study.duyiedu.com/api/herolist')
  .then((resp) => {
    console.log(resp.data); // resp.data 为响应体的数据，axios会自动解析JSON格式
  });

// 使用 async/await
(async function () {
  const resp = await axios.get('https://study.duyiedu.com/api/herolist');
  console.log(resp.data);
})();

// 带查询参数的 GET 请求
axios.get('https://study.duyiedu.com/api/user/exists', {
  params: {
    // 这里可以配置 query，axios会自动将其进行Url编码
    loginId: 'a&k=3',
    a: 1,
    b: 2,
  },
})
.then((resp) => {
  console.log(resp.data);
});
```

### 2.2 POST 请求

```javascript
// 发送 POST 请求
axios.post('https://study.duyiedu.com/api/user/reg', {
  loginId: 'abc',
  loginPwd: '123123',
  nickname: '棒棒鸡',
})
.then((resp) => {
  console.log(resp.data);
});
```

### 2.3 其他请求方法

```javascript
// PUT 请求
axios.put('/api/user/1', {
  name: '更新用户'
});

// DELETE 请求
axios.delete('/api/user/1');

// PATCH 请求
axios.patch('/api/user/1', {
  name: '部分更新'
});
```

## 3. 响应结构

Axios 的响应对象包含以下重要属性：

```javascript
{
  // 服务端提供的响应数据
  data: {},
  
  // HTTP 状态码
  status: 200,
  
  // HTTP 状态信息
  statusText: 'OK',
  
  // 响应头信息
  headers: {},
  
  // axios 请求配置
  config: {},
  
  // 原始请求对象
  request: {}
}
```

## 4. 请求配置对象

Axios 支持丰富的配置选项：

```javascript
axios({
  method: 'post',                    // 请求方法
  url: '/api/user/1',                // 请求 URL
  data: {                            // 请求体数据
    firstName: 'Fred'
  },
  params: {                          // URL 查询参数
    id: 12345
  },
  baseURL: 'https://api.example.com', // 基础 URL
  headers: {                         // 自定义请求头
    'Authorization': 'Bearer token'
  },
  timeout: 1000,                     // 请求超时时间
  responseType: 'json',             // 响应数据类型
  // ... 更多配置选项
});
```

## 5. 拦截器

拦截器是 Axios 最强大的功能之一，用于在请求或响应被 then 或 catch 处理前拦截它们。

### 5.1 请求拦截器

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
  // 在发送请求之前做些什么
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.authorization = `Bearer ${token}`;
  }
  return config; // 必须返回配置对象
}, function (error) {
  // 对请求错误做些什么
  return Promise.reject(error);
});
```

### 5.2 响应拦截器

```javascript
// 添加响应拦截器
axios.interceptors.response.use(function (response) {
  // 对响应数据做点什么
  const token = response.headers.authorization;
  if (token) {
    localStorage.setItem('token', token);
  }
  if (response.data.code !== 0) {
    alert(response.data.msg);
  }
  return response.data.data; // 仅返回响应体中的data属性
}, function (error) {
  // 对响应错误做点什么
  alert(error.message);
  return Promise.reject(error);
});
```

## 6. 创建实例

当需要不同的配置时，可以创建 Axios 实例：

```javascript
// 创建自定义实例
const instance = axios.create({
  baseURL: 'https://study.duyiedu.com',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});

// 使用实例发送请求
instance.get('/api/user/profile')
  .then(response => console.log(response.data));

// 为实例添加拦截器
instance.interceptors.request.use(/* ... */);
instance.interceptors.response.use(/* ... */);
```

## 7. 完整实战示例

下面是一个完整的用户认证 API 封装示例：

```javascript
/**
 * Axios 实战示例 - 用户认证 API 封装
 * 功能：登录、注册、验证账号、获取用户信息
 */

// 1. 创建 Axios 实例，配置基础 URL
const ins = axios.create({
  baseURL: 'https://study.duyiedu.com',
});

// 2. 配置响应拦截器 - 统一处理响应数据
ins.interceptors.response.use(
  function (resp) {
    // 成功响应处理
    // 保存服务器返回的 token
    const token = resp.headers.authorization;
    if (token) {
      localStorage.setItem('token', token);
    }
    // 检查业务状态码
    if (resp.data.code !== 0) {
      alert(resp.data.msg);
    }
    return resp.data.data; // 仅返回数据部分
  },
  function (error) {
    // 错误响应处理
    alert(error.message);
    return Promise.reject(error);
  }
);

// 3. 配置请求拦截器 - 统一添加认证信息
ins.interceptors.request.use(function (config) {
  // 从 localStorage 获取 token
  const token = localStorage.getItem('token');
  if (token) {
    // 添加认证请求头
    config.headers.authorization = `Bearer ${token}`;
  }
  return config;
});

/**
 * 用户登录
 * @param {string} loginId - 账号
 * @param {string} loginPwd - 密码
 * @returns {Promise} 返回登录用户信息
 */
async function login(loginId, loginPwd) {
  return await ins.post('/api/user/login', {
    loginId,
    loginPwd,
  });
}

/**
 * 用户注册
 * @param {string} loginId - 账号
 * @param {string} loginPwd - 密码
 * @param {string} nickname - 昵称
 * @returns {Promise} 返回注册结果
 */
async function reg(loginId, loginPwd, nickname) {
  return await ins.post('/api/user/reg', { 
    loginId, 
    loginPwd, 
    nickname 
  });
}

/**
 * 验证账号是否存在
 * @param {string} loginId - 账号
 * @returns {Promise} 返回验证结果
 */
async function exists(loginId) {
  return await ins.get('/api/user/exists', {
    params: {
      loginId,
    },
  });
}

/**
 * 获取当前登录用户信息
 * @returns {Promise} 返回用户信息
 */
async function profile() {
  return await ins.get('/api/user/profile');
}

// 使用示例
(async function () {
  try {
    // 登录
    const user = await login('admin', '123123');
    console.log('登录成功:', user);
    
    // 获取用户信息
    const profile = await profile();
    console.log('用户信息:', profile);
    
  } catch (error) {
    console.error('操作失败:', error);
  }
})();
```

## 8. 最佳实践

### 8.1 错误处理

```javascript
axios.get('/api/data')
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    if (error.response) {
      // 服务器响应了错误状态码
      console.log(error.response.data);
      console.log(error.response.status);
    } else if (error.request) {
      // 请求已发出但没有收到响应
      console.log(error.request);
    } else {
      // 设置请求时发生错误
      console.log('Error', error.message);
    }
  });
```

### 8.2 请求取消

```javascript
// 创建取消令牌
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/api/data', {
  cancelToken: source.token
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('请求已取消:', thrown.message);
  }
});

// 取消请求
source.cancel('操作被用户取消');
```

### 8.3 并发请求

```javascript
// 同时发起多个请求
function getUserAccount() {
  return axios.get('/api/user/12345');
}

function getUserPermissions() {
  return axios.get('/api/user/12345/permissions');
}

// 使用 Promise.all
Promise.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (account, permissions) {
    // 两个请求都完成
    console.log(account, permissions);
  }));
```

## 9. 练习题

### 练习 1：基础 API 封装

参考接口文档：https://app.apifox.com/project/2429576

完成以下 API 函数：

```javascript
/**
 * 登录
 * @param {*} loginId 账号
 * @param {*} loginPwd 密码
 * @return 返回登录成功的用户
 */
async function login(loginId, loginPwd) {}

/**
 * 注册
 * @param {*} loginId 账号
 * @param {*} loginPwd 密码
 * @param {*} nickname 昵称
 */
async function reg(loginId, loginPwd, nickname) {}

/**
 * 验证账号是否存在
 * @param {*} loginId 账号
 */
async function exists(loginId) {}

/**
 * 恢复登录，获取当前登录的用户信息
 */
async function profile() {}
```

### 练习 2：统一处理

对上述 API 进行统一处理：

1. 对 baseURL 进行统一处理
2. 当服务器响应结果中的 code 不为 0 时，使用 alert 弹出错误消息 msg
3. 如果服务器响应头中出现 Authorization:token，保存到 localStorage
4. 请求时，如果 localStorage 中包含 token，带入到请求头中 Authorization: Bearer token

### 练习 3：测试验证

对每个函数进行调用测试，确保：
- 注册功能正常
- 登录后能获取 token
- 使用 token 能获取用户信息
- 错误处理正确

## 10. 参考资源

- [Axios 官方文档](https://axios-http.com/)
- [Axios GitHub](https://github.com/axios/axios)
- [MDN Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)

## 11. 总结

Axios 是一个功能强大的 HTTP 客户端库，它提供了：

1. **简洁的 API**：支持多种请求方式和配置选项
2. **拦截器机制**：统一处理请求和响应
3. **实例创建**：支持多个不同的配置
4. **错误处理**：完善的错误处理机制
5. **请求取消**：支持取消正在进行的请求

掌握 Axios 的使用对于前端开发非常重要，它是现代 Web 应用中与后端通信的核心工具。