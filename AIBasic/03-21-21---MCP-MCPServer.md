# MCP理论知识与MCP Server实现

## 1. 基础概念

### 1.1 进程与通信
- **进程**：执行一个应用程序，就会启动一个进程，操作系统会为其分配内存空间、系统资源
- **进程生命周期**：应用程序执行完毕后，系统分配给进程的资源就会被回收
- **进程通信要求**：进程不能结束，需要保持监听状态

### 1.2 标准输入输出 (stdio)
**stdio**: **st**an**d**ard **i**nput and **o**utput 标准输入输出

每个进程启动后，都会留出两个对外通信的接口：
- 标准输入接口：standard in
- 标准输出接口：standard output

**进程监听示例**：
```javascript
// 监听输入
process.stdin.on("data", () => {});
```

**父子进程模型**：
- 一个进程可以启动另一个进程
- 控制台是父进程，node程序是子进程
- stdio通信高效、简洁，但仅适用于本地进程间通信

**客户端启动子进程示例**：
```javascript
const { spawn } = require("child_process");

// 启动 server.js 子进程
const serverProcess = spawn("node", ["server.js"]); // node server.js

// 监听服务端的响应
serverProcess.stdout.on("data", (data) => {
    process.stdin.write(data.toString());
});

// 发送几条测试消息
const messages = ["生命有意义吗？", "宇宙有尽头吗？", "再见！"];

messages.forEach((msg, index) => {
  setTimeout(() => {
    console.log(`-->${msg}`);
    serverProcess.stdin.write(msg);
  }, index * 1000); // 每秒发一条
});
```

## 2. 通信格式

### 2.1 通信格式类型
- xml
- json
- 字符串

### 2.2 JSON-RPC 2.0
**JSON-RPC**: JSON Remote Procedure Call，远程函数调用

**请求格式 (request)**：
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

**响应格式 (response)**：
```json
{
  "jsonrpc": "2.0",
  "result": 11,
  "id": 1
}
```

## 3. MCP协议详解

### 3.1 MCP概述
MCP是一套**标准协议**，它规定了**应用程序**之间**如何通信**

**通信方式**：
- **stdio**：推荐，高效、简洁、本地
- **http**：可远程
  - StreamHTTP
  - SSE

**通信格式**：基于JSON-RPC的进一步规范

### 3.2 MCP基本规范

#### 3.2.1 初始化 initialize
两个应用程序要开始通信，首先需要初始化

**初始化请求**：
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2024-11-05",
    "capabilities": {
      "roots": {
        "listChanged": true
      },
      "sampling": {},
      "elicitation": {}
    },
    "clientInfo": {
      "name": "ExampleClient",
      "title": "Example Client Display Name",
      "version": "1.0.0"
    }
  }
}
```

**初始化响应**：
```json
{
  "jsonrpc": "2.0",
  "id": 1, 
  "result": {
    "protocolVersion": "2024-11-05",
    "capabilities": {
      "logging": {},
      "prompts": {
        "listChanged": true
      },
      "resources": {
        "subscribe": true,
        "listChanged": true
      },
      "tools": {
        "listChanged": true
      }
    },
    "serverInfo": {
      "name": "ExampleServer",
      "title": "Example Server Display Name",
      "version": "1.0.0"
    },
    "instructions": "Optional instructions for the client"
  }
}
```

#### 3.2.2 发现工具 tools/list
服务器有哪些工具函数可以供客户端调用

**工具列表请求**：
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/list"
}
```

**工具列表响应**：
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "get_weather",
        "title": "Weather Information Provider",
        "description": "Get current weather information for a location",
        "inputSchema": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "City name or zip code"
            }
          },
          "required": ["location"]
        }
      }
    ]
  }
}
```

#### 3.2.3 工具调用 tools/call
**工具调用请求**：
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {
      "location": "New York" 
    }
  }
}
```

**工具调用响应**：
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "content": [
      {
        "type": "text", 
        "text": "72°F" 
      }
    ]
  }
}
```

工具返回的类型有多种，具体可参考：https://modelcontextprotocol.io/specification/2025-06-18/server/tools#tool-result

## 4. MCP Server实现代码

### 4.1 协议处理模块 (protocal.js)
```javascript
module.exports = {
  initialize() {
    return {
      protocolVersion: "2024-11-05",
      capabilities: {
        logging: {},
        prompts: {
          listChanged: true,
        },
        resources: {
          subscribe: true,
          listChanged: true,
        },
        tools: {
          listChanged: true,
        },
      },
      serverInfo: {
        name: "ExampleServer",
        title: "Example Server Display Name",
        version: "1.0.0",
      },
      instructions: "Optional instructions for the client",
    };
  },
  "tools/list"() {
    return {
      tools: [
        {
          name: "sum",
          title: "两数求和",
          description: "得到两个数的和",
          inputSchema: {
            type: "object",
            properties: {
              a: {
                type: "number",
                description: "第一个数",
              },
              b: {
                type: "number",
                description: "第二个数",
              },
            },
            required: ["a", "b"],
          },
        },
        {
          name: "createFile",
          title: "创建文件",
          description: "在指定目录下创建一个文件",
          inputSchema: {
            type: "object",
            properties: {
              filename: {
                type: "string",
                description: "文件名",
              },
              content: {
                type: "string",
                description: "文件内容",
              },
            },
            required: ["filename", "content"],
          },
        },
      ],
    };
  },
};
```

### 4.2 工具实现模块 (tools.js)
```javascript
const fs = require("fs");
module.exports = {
  sum({ a, b }) {
    return {
      content: [
        {
          type: "text",
          text: `两个数求和结果为${a + b}`,
        },
      ],
    };
  },
  createFile({ filename, content }) {
    try {
      fs.writeFileSync(filename, content);
      return {
        content: [
          {
            type: "text",
            text: "文件创建成功",
          },
        ],
      };
    } catch (err) {
      return {
        content: [
          {
            type: "text",
            text: err.message || "文件创建失败",
          },
        ],
      };
    }
  },
};
```

### 4.3 服务器主程序 (server.js)
```javascript
const tools = require("./tools.js");
const protocal = require("./protocal.js");

process.stdin.on("data", (data) => {
  const req = JSON.parse(data);
  let result; // 存储执行工具的结果
  
  if (req.method === "tools/call") {
    // 说明是要调用工具
    result = tools[req.params.name](req.params.arguments);
  } else if (req.method in protocal) {
    result = protocal[req.method](req.params);
  } else {
    return;
  }

  const res = {
    jsonrpc: "2.0",
    result,
    id: req.id,
  };

  process.stdout.write(JSON.stringify(res) + "\n");
});
```

## 5. MCP调试工具

### 5.1 Inspector调试工具
在服务器目录下，直接运行：
```bash
npx @modelcontextprotocol/inspector
```

这个工具可以帮助调试和测试MCP服务器的实现。

## 6. 总结

1. **MCP协议**是一套标准化的应用程序间通信协议
2. **通信方式**主要使用stdio（本地）和http（远程）
3. **通信格式**基于JSON-RPC 2.0规范
4. **MCP Server**需要实现初始化、工具发现和工具调用三个核心功能
5. **调试工具**可以使用Inspector来测试和验证MCP服务器实现

MCP协议的优势在于标准化，使得遵循该协议的服务器能够与任何兼容的客户端进行通信，提高了互操作性和可扩展性。