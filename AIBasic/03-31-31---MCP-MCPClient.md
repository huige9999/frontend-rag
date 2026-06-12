# 31. MCP Client - MCP客户端实现

## 课程概述

本课程讲解如何实现一个MCP (Model Context Protocol) 客户端，让大模型能够使用MCP服务器提供的工具。

## 学习目标

- 理解Claude Desktop如何使用MCP Server
- 掌握MCP Client的基本工作流程
- 学习如何连接MCP Server并调用工具
- 实现一个完整的MCP客户端示例

## 核心概念

### MCP Client工作流程

1. **查看配置** - 查看当前配置了哪些MCP Server
2. **建立连接** - 连接这些MCP Server
3. **获取工具** - 查看MCP Server上提供了哪些工具
4. **工具调用** - 用户提问时，将工具箱和问题一起发送给大模型
5. **工具执行** - 当LLM判断需要调用工具时，返回调用标识
6. **结果处理** - 执行工具调用并将结果返回给LLM

## 项目结构

```
demo/
├── .mcpconfig.json          # MCP Server配置文件
├── .env                     # 环境变量配置
├── package.json             # 项目依赖配置
├── mcp-server.js           # MCP Server实现
└── Client/
    ├── index.js            # 客户端主程序
    ├── loadServer.js       # 配置加载模块
    ├── process.js          # 查询处理模块
    └── LLM.js              # 大模型交互模块
```

## 核心代码实现

### 1. MCP Server实现 (mcp-server.js)

```javascript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "weather-server",
  version: "1.0.0",
});

server.registerTool(
  "get_weather",
  {
    title: "天气查询工具",
    description: "根据城市名称查询当前天气",
    inputSchema: {
      location: z.string().describe("城市名称，例如 Hangzhou"),
    },
  },
  async ({ location }) => {
    console.error("调用了MCP Server 上的 get_weather 工具");
    return {
      content: [
        {
          type: "text",
          text: `📍 ${location} 天气晴，气温 26℃ ~ 32℃`,
        },
      ],
    };
  }
);

const transport = new StdioServerTransport();
server.connect(transport);
```

### 2. 配置加载模块 (Client/loadServer.js)

```javascript
import { readFile } from "fs/promises";
import path from "path";

export async function loadConfig() {
  const configPath = path.resolve(process.cwd(), ".mcpconfig.json");
  const raw = await readFile(configPath, "utf-8");

  if (!raw) throw new Error(".mcpconfig.json 文件缺失");

  const config = JSON.parse(raw);

  return config;
}
```

### 3. 大模型交互模块 (Client/LLM.js)

```javascript
import dotenv from "dotenv";
dotenv.config();
const DEEPSEEK_API_KEY = process.env.DEEPSEEK_API_KEY;

export async function callDeepseek(messages, tools) {
  const res = await fetch("https://api.deepseek.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${DEEPSEEK_API_KEY}`,
    },
    body: JSON.stringify({
      model: "deepseek-chat",
      messages, // 会话历史
      tools, // 工具箱
    }),
  });

  const json = await res.json();

  return json.choices[0].message;
}
```

### 4. 查询处理模块 (Client/process.js)

```javascript
import { callDeepseek } from "./LLM.js";

export async function processQuery(input, messages, tools, mcp) {
  try {
    messages.push({
      role: "user",
      content: input,
    });

    const message = await callDeepseek(messages, tools);
    messages.push(message);

    // 检查是否需要调用工具
    if (message.tool_calls) {
      // 执行工具调用
      for (const call of message.tool_calls) {
        const args = JSON.parse(call.function.arguments);
        const result = await mcp.callTool({
          name: call.function.name,
          arguments: args,
        });
        messages.push({
          role: "tool",
          tool_call_id: call.id,
          content: typeof result.content === "string"
            ? result.content
            : JSON.stringify(result.content),
        });
      }
      
      // 获取最终回复
      const final = await callDeepseek(messages, tools);
      messages.push(final);
      return final.content;
    }

    // 不需要调用工具，直接返回回复
    return message.content;
  } catch (err) {
    console.error(`处理消息时出错，错误信息为：${err.message}`);
  }
}
```

### 5. 客户端主程序 (Client/index.js)

```javascript
import { loadConfig } from "./loadServer.js";
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";
import readline from "readline/promises";
import { processQuery } from "./process.js";

// 加载配置
const { server } = await loadConfig();

// 创建MCP客户端
const mcp = new Client({
  name: "mcp-client",
  version: "0.1.0",
});

// 建立连接通道
const transport = new StdioClientTransport({
  command: "node",
  args: [server],
});
mcp.connect(transport);

// 获取所有工具
const toolsResult = await mcp.listTools();
const tools = toolsResult.tools.map((t) => ({
  type: "function",
  function: {
    name: t.name,
    description: t.description,
    parameters: t.inputSchema,
  },
}));

let messages = [];

// 启动控制台聊天
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

console.log(
  `MCP Client已经成功启动，输入 quit 退出聊天，输入 clear 清除聊天历史`
);

while (true) {
  const input = await rl.question("请输入您的问题：");

  if (input.toLocaleLowerCase() === "quit") break;
  if (input.toLocaleLowerCase() === "clear") {
    messages = [];
    console.log("聊天记录已经全部清除");
    continue;
  }

  const output = await processQuery(input, messages, tools, mcp);
  console.log(output);
  console.log("\n\n");
}

await mcp.close();
rl.close();
process.exit(0);
```

## 配置文件

### .mcpconfig.json
```json
{
  "server": "./mcp-server.js"
}
```

### package.json
```json
{
  "name": "demo",
  "version": "1.0.0",
  "main": "Client/index.js",
  "type": "module",
  "scripts": {
    "start": "node Client/index.js"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.17.0",
    "dotenv": "^17.2.1",
    "zod": "^3.25.76"
  }
}
```

## 技术要点

### 1. MCP Client与Server的通信
- 使用StdioTransport进行进程间通信
- Client通过`connect()`方法建立连接
- 支持工具列表获取(`listTools()`)和工具调用(`callTool()`)

### 2. 工具调用的完整流程
1. 用户输入问题
2. 将问题和工具列表发送给LLM
3. LLM判断是否需要调用工具
4. 如需调用，执行工具并获取结果
5. 将工具调用结果再次发送给LLM
6. LLM生成最终回复

### 3. 消息历史管理
- 维护完整的对话历史
- 包含用户消息、助手回复、工具调用结果
- 支持清除历史记录功能

## 依赖包

- `@modelcontextprotocol/sdk`: MCP协议的官方SDK
- `dotenv`: 环境变量管理
- `zod`: 工具输入参数验证

## 运行方式

```bash
# 安装依赖
npm install

# 启动客户端
npm start
```

## 课程总结

通过本课程学习，我们掌握了：
- MCP Client的完整实现流程
- 如何与MCP Server建立通信
- 工具调用的标准化处理流程
- 大模型与工具的协作模式

这为实现更复杂的AI应用提供了基础架构，使得大模型能够通过标准化的协议访问各种外部工具和服务。