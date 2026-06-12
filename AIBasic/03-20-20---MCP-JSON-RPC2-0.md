# 03-20-20 --- MCP-JSON-RPC2-0

## 课程概述

本课程介绍了MCP（Model Context Protocol）的基础理论知识和JSON-RPC2.0通信协议，重点讲解了进程间通信（stdio）的标准输入输出机制。

## 核心概念

### 1. 进程（Process）

**定义**：执行一个应用程序就会启动一个进程，操作系统会为其分配内存空间和系统资源。应用程序执行完毕后，系统分配给进程的资源就会被回收。

**关键特性**：
- 进程之间可以进行通信
- 进程需要保持运行状态，通常通过监听机制实现
- 父子进程模型：一个进程可以启动另一个进程

### 2. stdio（标准输入输出）

**定义**：Standard Input and Output，标准输入输出接口。

**组成**：
- **标准输入接口（standard in）**：接收外部数据
- **标准输出接口（standard output）**：向外部发送数据

**实现原理**：
```javascript
// 监听输入，保持进程不结束
process.stdin.on("data", () => {});
```

### 3. 父子进程通信模型

控制台作为父进程，Node.js程序作为子进程，通过stdio进行双向通信。

**通信流程**：
1. 父进程启动子进程
2. 子进程监听标准输入
3. 父进程向子进程的标准输入写入数据
4. 子进程处理后将结果写入标准输出
5. 父进程从子进程的标准输出读取响应

## JSON-RPC 2.0协议

### 协议简介

**全称**：JSON Remote Procedure Call（JSON远程过程调用）

**作用**：一种基于JSON的轻量级远程调用协议，用于进程间的通信。

### 请求格式（Request）

```json
{
  "jsonrpc": "2.0",
  "method": "sum",
  "params": {
    "a": 5,
    "b": 6
  },
  "id": 1
}
```

**字段说明**：
- `jsonrpc`：协议版本，必须为"2.0"
- `method`：要调用的方法名
- `params`：方法参数，可以是对象或数组
- `id`：请求标识符，用于匹配响应

### 响应格式（Response）

```json
{
  "jsonrpc": "2.0",
  "result": 11,
  "id": 1
}
```

**字段说明**：
- `jsonrpc`：协议版本
- `result`：方法执行结果
- `id`：对应的请求标识符

## 实现示例

### 服务器端实现（server.js）

```javascript
const utils = require("./utils.js");

process.stdin.on("data", (data) => {
  const req = JSON.parse(data);
  const funcName = req.method;
  const params = req.params;
  
  // 调用方法
  const result = utils[funcName](params);
  
  // 返回符合 JSON-RPC2.0 格式的响应
  const res = {
    jsonrpc: "2.0",
    result,
    id: req.id,
  };
  
  process.stdout.write(JSON.stringify(res) + "\n");
});
```

### 工具函数实现（utils.js）

```javascript
const fs = require("fs");

module.exports = {
  sum({ a, b }) {
    return a + b;
  },
  createFile({ filename, content }) {
    try {
      fs.writeFileSync(filename, content);
      return true;
    } catch (error) {
      console.log(error);
      return false;
    }
  },
};
```

### 测试请求示例

```json
// 求和请求
{ 
  "jsonrpc": "2.0", 
  "method": "sum", 
  "params": { "a": 11, "b": 22 }, 
  "id": 1 
}

// 创建文件请求
{ 
  "jsonrpc": "2.0", 
  "method": "createFile", 
  "params": {  
    "filename": "/Users/jie/desktop/渡一MCP.txt", 
    "content": "Hello, 渡一MCP!" 
  }, 
  "id": 2 
}
```

## 客户端实现示例

```javascript
const { spawn } = require("child_process");

// 启动 server.js 子进程
const serverProcess = spawn("node", ["server.js"]);

// 监听服务端的响应
serverProcess.stdout.on("data", (data) => {
  console.log(data.toString());
});

// 发送几条测试消息
const messages = [
  '{ "jsonrpc": "2.0", "method": "sum", "params": { "a": 5, "b": 6 }, "id": 1 }\n',
  '{ "jsonrpc": "2.0", "method": "createFile", "params": { "filename": "test.txt", "content": "Hello" }, "id": 2 }\n'
];

messages.forEach((msg, index) => {
  setTimeout(() => {
    console.log(`--> Request ${index + 1}`);
    serverProcess.stdin.write(msg);
  }, index * 1000);
});
```

## 技术特点

### stdio通信优势

1. **高效**：直接在进程间传输数据，无需网络开销
2. **简洁**：使用标准输入输出，实现简单
3. **可靠**：基于操作系统的进程通信机制

### 限制

- **仅适用于本地进程间通信**：无法用于网络通信
- **需要父子进程关系**：通常需要父进程启动子进程

### JSON-RPC 2.0优势

1. **轻量级**：基于JSON，格式简洁
2. **标准化**：遵循统一的协议规范
3. **易于实现**：结构清晰，便于解析和生成
4. **支持多种参数格式**：参数可以是对象或数组

## 应用场景

1. **MCP协议实现**：AI模型与外部工具的通信
2. **本地进程间通信**：需要高性能数据交换的场景
3. **工具调用**：远程过程调用、函数执行等
4. **自动化脚本**：进程间的命令执行和结果返回

## 总结

本课程详细介绍了：

1. **进程基础**：进程的概念、生命周期和通信机制
2. **stdio通信**：标准输入输出的原理和实现
3. **JSON-RPC 2.0**：远程过程调用协议的格式和使用
4. **实际应用**：通过代码示例展示了完整的通信流程

这些知识为理解MCP协议和实现AI模型与外部工具的通信奠定了基础。