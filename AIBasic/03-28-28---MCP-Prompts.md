# 28. [MCP] Prompts - 提示词模板

## 课程概述

本课程介绍 MCP (Model Context Protocol) 中的 Prompts 功能，这是 MCP 支持的 3 种上下文能力之一。

### MCP 的 3 种上下文能力

1. **tools**：工具
2. **resources**：资源  
3. **prompts**：提示词

## 什么是 MCP Prompts

在 MCP 中，prompts 表示服务端内置的**提示词模板**（prompt templates）集合。通过 prompt 模板机制：

- 客户端无需硬编码 prompt
- **复用服务端定义的标准提示词**
- 实现统一、版本化、模块化的调用方式

## Prompt 相关方法

### 1. 获取提示词列表

**请求：**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "prompts/list",
  "params": {}
}
```

**返回：**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "prompts": [
    {
      "name": "analyze-code",
      "description": "Analyze code for potential improvements",
      "arguments": [
        {
          "name": "language",
          "description": "Programming language",
          "required": true
        }
      ]
    }
  ]
}
```

### 2. 使用提示词

**请求：**
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "prompts/get",
  "params": {
    "name": "analyze-code",
    "arguments": {
      "language": "python"
    }
  }
}
```

**返回：**
```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "description": "Analyze Python code for potential improvements",
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Please analyze the following Python code for potential improvements:\n\n```python\ndef calculate_sum(numbers):\n    total = 0\n    for num in numbers:\n        total = total + num\n    return total\n\nresult = calculate_sum([1, 2, 3, 4, 5])\nprint(result)\n```"
      }
    }
  ]
}
```

## 代码实现示例

### 环境配置

**package.json：**
```json
{
  "name": "demo",
  "version": "1.0.0",
  "type": "module",
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.16.0"
  }
}
```

### 注册提示词模板

**示例 1：代码总结提示词**

```javascript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({
  name: "resources-server",
  version: "0.1.0",
});

server.registerPrompt(
  "总结代码", // 提示词模板的名称
  {
    description: "生成代码总结的prompt模板",
    arguments: [
      {
        name: "code",
        description: "需要总结的代码",
        required: false,
      },
      {
        name: "language",
        description: "代码对应的编程语言",
        required: false,
      },
    ],
  },
  async (request) => {
    const args = request.arguments || {};
    const { code, language } = args;

    // 提供默认值用于演示
    const codeContent = code || `
    function calculateTotal(items) {
      let total = 0;
      for (const item of items) {
        total += item.price * item.quantity;
      }
      return total;
    }`;

    const codeLanguage = language || "javascript";

    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `
              请帮我总结以下${codeLanguage}代码的功能和主要逻辑：
                \`\`\`${codeLanguage.toLowerCase()}
                ${codeContent}
                \`\`\`

                请用中文回答，包括：
                1. 代码的主要功能
                2. 关键的逻辑流程  
                3. 使用的主要技术或库
                4. 可能的改进建议

                ${!code ? "\n*注意：由于未提供代码参数，这里使用了示例代码进行演示。*" : ""}
            `,
          },
        },
      ],
    };
  }
);
```

**示例 2：测试用例生成提示词**

```javascript
server.registerPrompt(
  "生成测试的提示词模板",
  {
    description: "为指定函数生成测试用例的prompt模板",
    arguments: [
      {
        name: "function_name",
        description: "要测试的函数名称",
        required: false,
      },
      {
        name: "function_code",
        description: "函数的完整代码",
        required: false,
      },
      {
        name: "test_framework",
        description: "使用的测试框架 (例如: Jest, Mocha, Vitest)",
        required: false,
      },
    ],
  },
  async (request) => {
    const args = request.arguments || {};
    const { function_name, function_code, test_framework } = args;

    // 提供默认值
    const functionName = function_name || "calculateTotal";
    const functionCode = function_code || `function calculateTotal(items) {
  if (!Array.isArray(items)) {
    throw new Error('参数必须是数组');
  }
  
  let total = 0;
  for (const item of items) {
    if (!item.price || !item.quantity) {
      throw new Error('每个商品必须有价格和数量');
    }
    total += item.price * item.quantity;
  }
  return total;
}`;
    const testFramework = test_framework || "Jest";

    return {
      messages: [
        {
          role: "user",
          content: {
            type: "text",
            text: `请为以下函数生成${testFramework}测试用例：

函数名：${functionName}

函数代码：
\`\`\`javascript
${functionCode}
\`\`\`

请生成包括以下场景的测试用例：
1. 正常情况的测试
2. 边界情况的测试
3. 错误情况的测试
4. 输入验证的测试

请用中文注释说明每个测试用例的目的。

${!function_name && !function_code ? "\n*注意：由于未提供函数参数，这里使用了示例函数进行演示。*" : ""}`,
          },
        },
      ],
    };
  }
);

// 启动服务器
const transport = new StdioServerTransport();
await server.connect(transport);
```

## 重新认识 MCP

**MCP** 全称 **Model Context Protocol**（模型上下文协议），其旨在为 AI 应用与外部程序之间建立**通信标准**，从而：

- 使外部程序可以被部署到任意 AI（满足 MCP 协议）
- 使 AI 应用可以使用任意的外部程序（MCP Server）

### 为什么称之为"模型上下文"？

无论是工具、资源、提示词，这些信息最终都会作为**上下文的一部分**提供给大模型。也就是说，大模型是最终信息的消费者。

## MCP 资源聚合平台

### 官方资源
官方组织推出了一些 [MCP Server](https://github.com/modelcontextprotocol/servers)

### 第三方平台
1. [MCP.So](https://mcp.so/)
2. [Awesome MCP Servers](https://mcpservers.org/)

> **注意**：目前第三方平台的 MCP Server 质量参差不齐，推荐优先使用官方推出的 MCP Server。

## 关键要点总结

1. **Prompts 是模板化的提示词**，服务端预定义，客户端复用
2. **参数化设计**：通过 arguments 实现灵活的参数传递
3. **版本化管理**：提示词模板可以统一管理和版本控制
4. **标准化调用**：通过 `prompts/list` 和 `prompts/get` 方法统一调用
5. **上下文增强**：最终作为上下文提供给大模型消费

## 课堂练习

为 MCP Server 注册提示词：
- 设计一个有实际应用价值的提示词模板
- 定义清晰的参数结构
- 实现参数验证和默认值处理
- 返回结构化的消息格式

---

-EOF-
