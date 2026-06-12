# 03-30 [MCP] StreamableHTTP 远程通信

## 课程概述

本课程介绍 MCP (Model Context Protocol) 中的 StreamableHTTP 通信方式，这是一种用于 Web 环境的远程通信协议，支持通过 HTTP 进行客户端与服务器之间的交互。

## 学习目标

- 理解 SSE (Server-Sent Events) 的原理和特点
- 掌握 StreamableHTTP 的工作机制
- 学会使用 StreamableHTTPServerTransport 接口
- 实现基于 HTTP 的 MCP 服务器

## 核心内容

### 1. MCP 通信方式对比

MCP 支持两种主要的通信方式：

1. **Stdio**：推荐用于本地场景，高效、简洁
2. **Streamable HTTP**：用于远程通信场景

### 2. 前置知识：Server-Sent Events (SSE)

#### SSE 定义
SSE (Server-Sent Events) 是一种基于 HTTP 的单向通信协议，由浏览器发起连接，服务器可以持续不断地向客户端推送数据。

#### SSE 特点

- **协议**：基于 HTTP（长连接）
- **方向**：单向（服务器 → 客户端）
- **格式**：文本流，内容类型为 `text/event-stream`
- **浏览器支持**：所有现代浏览器（IE 除外）
- **应用场景**：实时通知、消息流、状态更新、股票/天气数据等

#### SSE 消息格式

```
event: 事件名   # 可选，默认是 message 事件
id: 唯一ID     # 可选
retry: 3000   # 客户端断线重连间隔，单位毫秒，可选
data: 内容     # 必需，可以多行
```

每条消息用空行 `\n\n` 作为结尾。

#### 事件类型处理

**默认事件类型：**
```
data: 这是默认消息
```

客户端监听：
```javascript
eventSource.addEventListener("message", (e) => {
  console.log("默认事件：", e.data);
});
```

**自定义事件类型：**
```
event: update
data: 新的更新内容
```

客户端监听：
```javascript
eventSource.addEventListener("update", (e) => {
  console.log("收到 update 事件：", e.data);
});
```

### 3. StreamableHTTP 工作原理

#### 基本概念
Streamable HTTP 是 MCP 中用于 Web 环境的通信方式，客户端基于 HTTP POST 发送 JSON-RPC 请求。

#### 通信流程

**请求-响应模式：**
```
客户端 → 服务器：POST /mcp（initialize / callTool）
服务器 → 客户端：JSON一次性响应 或 SSE 流响应
```

**持久连接模式：**
```
客户端 → 服务器：GET /mcp（建立 SSE）
服务器 → 客户端：推送 notifications（如 list_changed）
```

#### 支持的 JSON-RPC 方法

- `initialize` - 初始化连接
- `callTool` - 调用工具
- `listResources` - 列出资源
- `notifications/resources/list_changed` - 资源变更通知
- `notifications/tools/list_changed` - 工具变更通知

#### 响应类型

服务器可返回两种类型的响应：

1. **普通 JSON 响应**（application/json）
2. **流式 SSE 响应**（text/event-stream）

#### 使用场景

1. 通过 HTTP 接收远程请求（如前端网页、API 网关）
2. 需要支持多客户端并发访问
3. 浏览器与 MCP Server 通信
4. 流式响应（如 SSE 推送）需求

### 4. 官方接口：StreamableHTTPServerTransport

#### 导入和初始化

```javascript
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { randomUUID } from "crypto";

const transport = new StreamableHTTPServerTransport({
  sessionIdGenerator: () => randomUUID(), // 为每个连接生成唯一会话ID
});
```

#### 核心方法：handleRequest

```javascript
handleRequest(
  req: IncomingMessage,
  res: ServerResponse,
  parsedBody: unknown // 解析后的请求体
): Promise<void>;
```

**内部处理流程：**
1. 解析 body，识别 JSON-RPC 方法（如 "initialize", "callTool"）
2. 将请求路由给 MCP Server 的对应处理函数
3. 根据返回结果的 Content-Type 自动决定是普通 JSON 响应还是流式响应

## 实战示例

### 项目结构

```
demo/
├── package.json
└── src/
    ├── http-server.js      # HTTP 服务器
    ├── mcp-server.js       # MCP 服务器逻辑
    └── utils.js            # 工具函数
```

### 依赖配置

```json
{
  "name": "demo",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node src/http-server.js"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.17.0",
    "express": "^5.1.0",
    "zod": "^3.25.76"
  }
}
```

### HTTP 服务器实现

```javascript
import express from "express";
import { setCommonHeaders } from "./utils.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { randomUUID } from "crypto";
import { createMCPServer } from "./mcp-server.js";

const app = express();
const transports = {};

app.use(express.json());

// GET 请求处理
app.get("/mcp", (req, res) => {
  setCommonHeaders(res);
  res.status(405)
     .set("Allow", "POST")
     .send("当前服务器不支持GET方法，仅支持POST方法");
});

// POST 请求处理
app.post("/mcp", async (req, res) => {
  setCommonHeaders(res);

  try {
    const body = req.body;
    const method = body?.method;
    const sessionId = req.headers["mcp-session-id"];
    const transport = sessionId && transports[sessionId];

    // 处理初始化请求
    if (!transport && method === "initialize") {
      const newTransport = new StreamableHTTPServerTransport({
        sessionIdGenerator: () => randomUUID(),
      });

      // 设置关闭回调
      newTransport.onclose = () => {
        if (newTransport.sessionId) {
          delete transports[newTransport.sessionId];
        }
      };

      // 创建并连接 MCP Server
      const mcpServer = createMCPServer();
      await mcpServer.connect(newTransport);
      await newTransport.handleRequest(req, res, body);

      // 存储连接
      if (newTransport.sessionId) {
        transports[newTransport.sessionId] = newTransport;
      }
      return;
    }

    // 处理后续请求
    if (transport) {
      await transport.handleRequest(req, res, body);
      return;
    }

    // 错误处理
    res.status(400).json({
      error: "非法的请求",
      message: "非法的 SessionId 或者非初始化操作",
    });
  } catch (err) {
    console.error(`出错了，对应信息${err.message}`);
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`MCP Server 运行在 http://localhost:${PORT}/mcp`);
});
```

### MCP 服务器实现

```javascript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

export const createMCPServer = () => {
  const server = new McpServer({
    name: "http-mcp-server",
    version: "0.1.0",
  });

  // 注册工具
  server.registerTool(
    "两数之和",
    {
      title: "数字加法计算器",
      description: "计算两个数字的和",
      inputSchema: {
        num1: z.number().describe("第一个数字"),
        num2: z.number().describe("第二个数字"),
      },
    },
    async ({ num1, num2 }) => {
      return {
        content: [
          {
            type: "text",
            text: `计算结果: ${num1} + ${num2} = ${num1 + num2}!!!`,
          },
        ],
      };
    }
  );

  return server;
};
```

### 工具函数

```javascript
// 设置响应头的方法
export function setCommonHeaders(res) {
  // 允许哪些域（Origin）可以访问该服务
  res.setHeader("Access-Control-Allow-Origin", "*");
  // 允许客户端在跨域请求中使用哪些 HTTP 方法
  res.setHeader("Access-Control-Allow-Methods", "POST, GET, DELETE, OPTIONS");
  // 指定客户端请求时允许携带的自定义请求头
  res.setHeader("Access-Control-Allow-Headers", "Content-Type, mcp-session-id");
  // 允许前端 JS 访问响应中的哪些自定义头
  res.setHeader("Access-Control-Expose-Headers", "mcp-session-id");
}
```

## 关键技术要点

### 1. 会话管理

- 使用 `sessionId` 标识每个客户端连接
- 通过 `transports` 对象存储活跃连接
- 在连接关闭时自动清理资源

### 2. CORS 配置

正确设置跨域资源共享头，支持：
- 不同源的前端应用访问
- 自定义请求头（如 `mcp-session-id`）
- 多种 HTTP 方法

### 3. 错误处理

- 区分初始化请求和普通请求
- 处理无效的 sessionId
- 捕获和记录异常信息

### 4. 流式响应

- 支持 SSE 格式的流式数据推送
- 自动选择合适的响应类型
- 处理长连接和断线重连

## 实践练习

1. **基础练习**：搭建基于 StreamableHTTP 的 MCP 服务器
2. **进阶练习**：实现多客户端并发访问
3. **高级练习**：添加 SSE 流式响应功能

## 总结

StreamableHTTP 是 MCP 在 Web 环境中的重要通信方式，通过结合 HTTP 和 SSE 技术，实现了：

- 远程访问能力
- 流式数据传输
- 多客户端支持
- 灵活的会话管理

掌握 StreamableHTTP 对于构建基于 Web 的 AI 应用和工具集成具有重要意义。

## 参考资料

- MCP 官方文档
- SSE 规范
- Express.js 文档
- HTTP 协议相关标准

---

*课程编号：03-30*
*课程主题：MCP StreamableHTTP 远程通信*
*难度级别：中级*