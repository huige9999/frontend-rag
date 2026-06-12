# 接入DeepSeek

## 课程概述

本课程介绍如何接入和使用DeepSeek大模型API，包括基础API调用、流式响应、上下文支持，以及如何在Node.js项目中集成DeepSeek实现功能调用和会话管理。

## 学习目标

- 掌握DeepSeek API的基本调用方法
- 理解流式响应和非流式响应的区别
- 学会实现大模型的上下文支持
- 了解如何在项目中封装和调用大模型API
- 掌握Function Calling的实现原理

## 核心概念

### 1. API密钥获取

首先需要在[DeepSeek开发平台](https://platform.deepseek.com/)申请API密钥。

**重要提示**：API密钥只在创建时显示完整内容，请务必妥善保存。

### 2. 基础API调用

#### 2.1 非流式调用

使用curl命令发送POST请求到DeepSeek的`/chat/completions`接口：

```bash
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-YOUR_API_KEY" \
  -d '{
        "model": "deepseek-chat",
        "messages": [
          {"role": "user", "content": "你是谁？"}
        ],
        "stream": false
      }'
```

**参数说明：**
- `-H "Content-Type: application/json"`: 指定请求体为JSON格式
- `-H "Authorization: Bearer sk-xxx"`: 使用Bearer Token方式进行认证
- `-d '...'`: 传递请求体数据
  - `"model": "deepseek-chat"`: 指定使用的模型
  - `"messages": [...]`: 消息对象数组
  - `"stream": false`: 关闭流式响应

#### 2.2 响应格式

```json
{
  "id": "81dddbb5-c01b-476b-a5d5-c5ac87e31b92",
  "object": "chat.completion",
  "created": 1751766353,
  "model": "deepseek-chat",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "我是一个智能AI助手，随时为你提供帮助和解答问题。有什么我可以帮你的吗？"
      },
      "logprobs": null,
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 19,
    "total_tokens": 44,
    "prompt_tokens_details": {
      "cached_tokens": 0
    },
    "prompt_cache_hit_tokens": 0,
    "prompt_cache_miss_tokens": 25
  },
  "system_fingerprint": "fp_8802369eaa_prod0623_fp8_kvcache"
}
```

**响应字段说明：**
- `id`: 请求的唯一标识符
- `object`: 返回对象类型
- `created`: 响应生成时间戳
- `choices`: 模型返回的回答数组
- `usage`: Token使用情况统计
- `system_fingerprint`: 模型运行环境指纹

### 3. 流式响应

#### 3.1 开启流式响应

```bash
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-YOUR_API_KEY" \
  -d '{
        "model": "deepseek-chat",
        "messages": [
          {"role": "user", "content": "你是谁？"}
        ],
        "stream": true
      }'
```

#### 3.2 流式响应格式

流式响应以`data: `前缀的JSON对象形式返回：

```
data: {"id":"83e0da03-9eb4-4883-887c-14847e88a441","object":"chat.completion.chunk","created":1751767365,"model":"deepseek-chat","choices":[{"index":0,"delta":{"content":"我是一个"},"finish_reason":null}]}

data: {"id":"83e0da03-9eb4-4883-887c-14847e88a441","object":"chat.completion.chunk","created":1751767365,"model":"deepseek-chat","choices":[{"index":0,"delta":{"content":"智能"},"finish_reason":null}]}

data: {"id":"83e0da03-9eb4-4883-887c-14847e88a441","object":"chat.completion.chunk","created":1751767365,"model":"deepseek-chat","choices":[{"index":0,"delta":{"content":""},"finish_reason":"stop"}],"usage":{"prompt_tokens":25,"completion_tokens":19,"total_tokens":44}}

data: [DONE]
```

**流式响应特点：**
- 数据以`data: `前缀的JSON行返回
- 每行包含一个或多个token的增量内容
- 最后以`data: [DONE]`标记结束
- 需要客户端解析并拼接完整响应

### 4. 上下文支持

#### 4.1 上下文问题演示

**问题示例：**

```bash
# 第一次请求
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-YOUR_API_KEY" \
  -d '{
        "model": "deepseek-chat",
        "messages": [
          {"role": "user", "content": "你是谁？"}
        ],
        "stream": false
      }'

# 第二次请求
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-YOUR_API_KEY" \
  -d '{
        "model": "deepseek-chat",
        "messages": [
          {"role": "user", "content": "我刚才说的啥？"}
        ],
        "stream": false
      }'
```

**模型回复：**
```
你之前没有发送过任何内容哦～可能是消息没发送成功？你可以重新告诉我你的问题或需求，我会尽力帮你解答！ 😊
```

#### 4.2 实现上下文支持

**解决方案：** 将历史会话记录添加到messages数组中

```bash
curl https://api.deepseek.com/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-YOUR_API_KEY" \
  -d '{
        "model": "deepseek-chat",
        "messages": [
          {"role": "user", "content": "你知道大象么？"},
          {"role": "assistant", "content": "大象是陆地上体型最大的哺乳动物，以其智慧、社会性和标志性的长鼻子（象鼻）而闻名。"},
          {"role": "user", "content": "我们刚才聊了啥？"}
        ],
        "stream": false
      }'
```

**上下文支持原理：**
- 大模型本身是无状态的
- 上下文支持通过将历史消息传递给模型实现
- 客户端需要维护会话历史数组

## 项目集成实践

### 1. 项目结构

```
chat-bot/
├── client/          # Vue前端应用
└── server/          # Node.js后端服务
    ├── app.js
    ├── routes/
    │   └── index.js
    └── utils/
        ├── LLM.js
        ├── wetherHandler.js
        ├── translateHandler.js
        └── promptTemplates.js
```

### 2. LLM工具封装 (utils/LLM.js)

#### 2.1 超时机制封装

```javascript
async function fetchWithTimeout(url, options = {}, timeout = LLM_TIMEOUT_MS) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal,
    });
    
    clearTimeout(timeoutId);
    return response;
  } catch (err) {
    clearTimeout(timeoutId);
    
    if (err.name === "AbortError") 
      throw new Error("请求超时，请稍候重试");
    throw err;
  }
}
```

#### 2.2 LLM调用核心函数

```javascript
async function callLLM({ prompt, stream = false, callback }) {
  const response = await fetchWithTimeout(LLM_ENDPOINT, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${process.env.DEEPSEEK_API_KEY}`,
    },
    body: JSON.stringify({
      model: LLM_MODEL,
      messages: [{ role: "user", content: prompt }],
      stream,
    }),
  });

  if (!response.ok)
    throw new Error(`模型请求失败：${response.status} : ${response.statusText}`);

  // 非流式处理
  if (!stream) {
    const data = await response.json();
    return data.choices?.[0]?.message?.content || "";
  }

  // 流式处理
  const reader = response.body.getReader();
  const decoder = new TextDecoder("utf-8");
  let fullResponse = "";

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    const chunk = decoder.decode(value, { stream: true });
    const lines = chunk.split("\n").filter((line) => line.trim());

    for (const line of lines) {
      if (!line.startsWith("data: ")) continue;
      
      const jsonStr = line.slice(6);
      if (jsonStr === "[DONE]") continue;
      
      try {
        const data = JSON.parse(jsonStr);
        const chunk = data.choices?.[0]?.delta?.content;
        if (chunk) {
          fullResponse += chunk;
          callback?.(chunk);
        }
      } catch (e) {
        console.error("JSON解析失败", e.message);
      }
    }
  }

  return fullResponse;
}
```

#### 2.3 导出接口

```javascript
module.exports = {
  // 非流式回复
  callLLM: (prompt) => callLLM({ prompt }),
  
  // 流式回复
  callLLMStream: (prompt, callback) => 
    callLLM({ prompt, stream: true, callback }),
};
```

### 3. 路由集成 (routes/index.js)

#### 3.1 会话管理

```javascript
const conversations = [];

/**
 * conversations = [
 *  {role: "user", content: "你是谁"},
 *  {role: "assistant", content: "大模型的回复"},
 * ]
 */
```

#### 3.2 核心API路由

```javascript
router.post("/ask", async (req, res) => {
  const question = req.body.question || "";
  
  // 设置SSE响应头
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");

  // Function Calling判断
  const functionCallPrompt = buildFunctionCallPrompt(question);
  const functionCallResult = await callLLM(functionCallPrompt);

  let finalResponse = "";

  if (functionCallResult.trim() === "无函数调用") {
    // 常规对话
    const prompt = [
      "你是一个中文智能助手，请严格使用中文来回答用户的问题",
      ...conversations.map(
        (item) => `${item.role === "user" ? "用户" : "助手"}：${item.content}`
      ),
      `用户的问题：${question}`,
    ].join("\n");

    finalResponse = await callLLMStream(prompt, (chunk) => {
      res.write(`${JSON.stringify({ response: chunk })}\n`);
    });
  } else {
    // Function Calling处理
    const toolCalls = JSON.parse(functionCallResult);
    const toolsResult = [];

    for (const tool of toolCalls) {
      const { function: functionName, args } = tool;
      if (toolsMap[functionName]) {
        try {
          const result = await toolsMap[functionName](args);
          toolsResult.push({ function: functionName, args, result });
        } catch (err) {
          toolsResult.push({
            function: functionName,
            args,
            result: `工具调用失败：${err.message}`,
          });
        }
      }
    }

    const answerPrompt = buildAnswerPrompt(question, toolsResult);
    finalResponse = await callLLMStream(answerPrompt, (chunk) => {
      res.write(`${JSON.stringify({ response: chunk })}\n`);
    });
  }

  // 更新会话历史
  conversations.push(
    { role: "user", content: question },
    { role: "assistant", content: finalResponse }
  );

  // 限制会话历史长度
  if (conversations.length > 20)
    conversations.splice(0, conversations.length - 20);

  res.end();
});
```

### 4. 环境配置

```env
LLM_ENDPOINT=https://api.deepseek.com/chat/completions
LLM_MODEL=deepseek-chat
LLM_TIMEOUT_MS=30000
DEEPSEEK_API_KEY=sk-your_api_key_here
```

## 技术要点总结

### 1. API调用要点

- **认证方式**: Bearer Token
- **请求格式**: JSON
- **响应格式**: JSON（非流式）/ SSE流（流式）
- **错误处理**: HTTP状态码和错误消息

### 2. 流式响应处理

- 使用`response.body.getReader()`获取读取器
- 使用`TextDecoder`解码二进制数据
- 解析`data: `前缀的JSON行
- 处理`[DONE]`结束标记
- 拼接完整响应内容

### 3. 上下文管理

- 客户端维护会话历史数组
- 每次请求包含历史消息
- 限制历史长度避免Token浪费
- 区分user和assistant角色

### 4. 项目集成最佳实践

- **封装API调用**: 创建独立的LLM工具模块
- **超时处理**: 实现请求超时机制
- **错误处理**: 完善的异常捕获和错误提示
- **配置管理**: 使用环境变量存储敏感信息
- **模块化设计**: 功能拆分为独立工具函数

## Function Calling 实现原理

### 1. 工具判断阶段

通过特定的提示词让模型判断是否需要调用工具：

```javascript
const functionCallPrompt = buildFunctionCallPrompt(question);
const functionCallResult = await callLLM(functionCallPrompt);
```

### 2. 工具执行阶段

解析模型返回的工具调用指令，执行相应的工具函数：

```javascript
const toolCalls = JSON.parse(functionCallResult);
for (const tool of toolCalls) {
  const { function: functionName, args } = tool;
  const result = await toolsMap[functionName](args);
  toolsResult.push({ function: functionName, args, result });
}
```

### 3. 结果生成阶段

将工具执行结果反馈给模型，生成最终回复：

```javascript
const answerPrompt = buildAnswerPrompt(question, toolsResult);
finalResponse = await callLLMStream(answerPrompt, (chunk) => {
  res.write(`${JSON.stringify({ response: chunk })}\n`);
});
```

## 实践练习

### 练习目标

重构当前的AI聊天机器人，实现以下功能：
- DeepSeek API集成
- 流式响应支持
- 上下文会话管理
- Function Calling功能

### 技术要求

1. **API集成**
   - 封装LLM调用工具
   - 实现超时机制
   - 完善错误处理

2. **流式处理**
   - 正确解析SSE流
   - 实现增量内容传输
   - 处理流结束标记

3. **会话管理**
   - 维护历史会话数组
   - 实现上下文传递
   - 限制会话长度

4. **功能扩展**
   - 实现工具调用机制
   - 支持多工具管理
   - 生成工具调用结果

## 总结

本课程详细介绍了如何接入和使用DeepSeek大模型API，从基础的curl命令调用到完整的项目集成实践。重点掌握了：

1. **API基础调用**: 掌握了DeepSeek API的基本使用方法和参数配置
2. **流式响应处理**: 学会了处理SSE流式响应的技术实现
3. **上下文管理**: 理解了大模型上下文支持的原理和实现方式
4. **项目集成**: 掌握了在Node.js项目中封装和调用大模型API的最佳实践
5. **Function Calling**: 了解了功能调用的实现原理和完整流程

通过这些知识，可以构建具有上下文支持、流式响应和功能调用能力的智能对话系统。

## 相关资源

- [DeepSeek开发平台](https://platform.deepseek.com/)
- [DeepSeek API文档](https://platform.deepseek.com/docs)
- [Server-Sent Events规范](https://html.spec.whatwg.org/multipage/server-sent-events.html)