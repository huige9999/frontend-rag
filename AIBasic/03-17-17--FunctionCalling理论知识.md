# Function Calling 理论知识

## 课程概述

Function Calling 是 OpenAI 在 2023 年 6 月推出的标准化机制，用于解决传统基于提示词的工具调用存在的问题。

## 传统工具调用的问题

1. **繁琐**：需要大段提示词来约束大模型输出
2. **不标准**：不同开发者的提示词描述千差万别
3. **约束力不高**：即使使用强调性提示词，大模型仍可能不按预期回复

## Function Calling 的标准化

Function Calling 通过 JSON Schema 格式实现标准化，主要包含两个部分：

### 1. 工具箱的定义

```js
// 工具箱定义示例
const tools = [{
    type: "function",
    name: "get_weather",
    description: "Get current temperature for provided coordinates in celsius.",
    parameters: {
        type: "object",
        properties: {
            latitude: { type: "number" },
            longitude: { type: "number" }
        },
        required: ["latitude", "longitude"],
        additionalProperties: false
    },
    strict: true
}];
```

### 2. 工具调用请求的返回

```js
// 工具调用请求格式
[{
    "type": "function_call",
    "id": "fc_12345xyz",
    "call_id": "call_12345xyz",
    "name": "get_weather",
    "arguments": "{\"latitude\":48.8566,\"longitude\":2.3522}"
}]
```

## 不同模型的 Function Calling 格式

### DeepSeek 格式

```js
tools = [{
  "type": "function",
  "function": {
      "name": "get_weather",
      "description": "Get weather of an location",
      "parameters": {
          "type": "object",
          "properties": {
              "location": {
                  "type": "string",
                  "description": "The city and state, e.g. San Francisco, CA",
              }
          },
          "required": ["location"]
      },
  }
}]
```

### GPT 格式

```js
const tools = [{
    type: "function",
    name: "get_weather",
    description: "Get current temperature for provided coordinates in celsius.",
    parameters: {
        type: "object",
        properties: {
            latitude: { type: "number" },
            longitude: { type: "number" }
        },
        required: ["latitude", "longitude"],
        additionalProperties: false
    },
    strict: true
}];
```

### Claude 格式

```js
"tools": [{
  "name": "get_weather",
  "description": "Get the current weather in a given location",
  "input_schema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The city and state, e.g. San Francisco, CA"
      }
    },
    "required": ["location"]
  }
}]
```

## 工作原理

Function Calling 能约束大模型输出的核心原理是**大模型微调**。通过专门的微调训练，让模型学会按照标准的 JSON Schema 格式输出。

正因为需要专门的微调，并非所有模型都支持 Function Calling。可以在 [Hugging Face](https://huggingface.co/models?other=function+calling&sort=trending) 查询特定模型是否支持该特性。

> **注意**：Hugging Face 是目前最主流的开源 AI 模型托管与使用平台，相当于 AI 界的 Github。

## 实践应用：聊天机器人实现

### 系统架构

聊天机器人采用前后端分离架构：
- **前端**：Vue.js + TypeScript 应用
- **后端**：Node.js + Express 服务器

### 核心功能实现

#### 1. LLM 接口封装

```javascript
async function callLLM({ prompt, stream = false, callback }) {
  const messages = [...prompt];
  
  const response = await fetchWithTimeout(LLM_ENDPOINT, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${process.env.DEEPSEEK_API_KEY}`,
    },
    body: JSON.stringify({
      model: LLM_MODEL,
      messages,
      stream,
    }),
  });

  // 根据流式/非流式进行不同处理
  if (!stream) {
    const data = await response.json();
    return data.choices?.[0]?.message?.content || "";
  }

  // 流式处理逻辑
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

      const data = JSON.parse(jsonStr);
      const chunk = data.choices?.[0]?.delta?.content;
      if (chunk) {
        fullResponse += chunk;
        callback?.(chunk);
      }
    }
  }

  return fullResponse;
}
```

#### 2. Function Calling 提示词模板

```javascript
function buildFunctionCallPrompt(userInput) {
  return `
你是一个中文智能助手，具有工具调用能力。请严格按照以下规则回复：

请根据用户的输入内容判断是否需要调用函数工具，规则如下：

1. 如果不需要调用函数工具，请直接返回字符串："无函数调用"（必须完全一致）
2. 如果需要调用函数，请返回标准JSON数组格式。

目前你支持的函数列表如下：
- getWeather：获取城市的天气信息
  参数要求：
  * city: 城市名称（必须是中文，如"北京"、"上海"、"成都"）
  * date: 日期（必须是中文，只能是："今天"、"明天"、"后天"）
- translate：用于中英文互译
  参数要求：
  * input: 需要翻译的文本

严格要求：
- 如果不需要调用函数，只返回"无函数调用"这5个字
- 如果需要调用函数，只返回JSON数组，不要包含其他文字解释
- 所有参数值必须使用中文，禁止使用英文
- JSON必须严格正确，不能有双引号嵌套问题

⚠️ 特别注意：返回的JSON必须能被JSON.parse()正确解析，任何格式错误都会导致系统无法处理！

以下是用户的输入，请根据内容判断是否需要函数调用：

【${userInput}】
`.trim();
}
```

#### 3. 工具执行逻辑

```javascript
// 工具函数映射表
const toolsMap = {
  getWeather,
  translate,
};

// 处理工具调用
const toolCalls = JSON.parse(functionCallResult);
const toolsResult = [];

for (const tool of toolCalls) {
  const { function: functionName, args } = tool;
  if (toolsMap[functionName]) {
    try {
      const result = await toolsMap[functionName](args);
      toolsResult.push({
        function: functionName,
        args,
        result,
      });
    } catch (err) {
      toolsResult.push({
        function: functionName,
        args,
        result: `工具调用失败：${err.message}`,
      });
    }
  } else {
    toolsResult.push({
      function: functionName,
      args,
      result: `未知工具`,
    });
  }
}
```

### 具体工具实现

#### 天气查询工具

```javascript
async function getWeather({ city, date }) {
  const formattedDate = formatDate(date);
  if (!formattedDate) {
    return `无法识别日期格式："${date}"，请使用"今天"、"明天"或"后天"`;
  }

  const locationId = await getCityLocation(city);
  if (!locationId) {
    return `无法识别城市："${city}"`;
  }

  const url = `https://devapi.qweather.com/v7/weather/7d?location=${locationId}&key=${HEFENG_API_KEY}`;
  const res = await fetch(url);
  const data = await res.json();

  const match = data.daily.find((d) => d.fxDate === formattedDate);
  if (!match) {
    return `暂无 ${formattedDate} 的天气数据`;
  }

  return `📍 ${city}（${formattedDate}）天气：${match.textDay}，气温 ${match.tempMin}°C ~ ${match.tempMax}°C`;
}
```

#### 翻译工具

```javascript
async function translate({ input }) {
  const text = input.replace(/.*翻译.*?[：:]?\s*/, "").trim();
  const salt = Date.now();
  const sign = crypto
    .createHash("md5")
    .update(ID + text + salt + KEY)
    .digest("hex");

  const url = `https://fanyi-api.baidu.com/api/trans/vip/translate?q=${encodeURIComponent(text)}&from=zh&to=en&appid=${ID}&salt=${salt}&sign=${sign}`;

  const res = await fetch(url);
  const data = await res.json();
  
  if (data.trans_result?.length > 0) {
    return `翻译结果：${data.trans_result[0].dst}`;
  } else {
    return `翻译失败：${JSON.stringify(data)}`;
  }
}
```

## 关键技术点

1. **超时机制**：使用 AbortController 实现请求超时控制
2. **流式响应**：支持 Server-Sent-Events (SSE) 实现实时响应
3. **上下文管理**：维护会话历史数组，支持上下文对话
4. **错误处理**：完善的工具调用错误处理机制
5. **JSON 解析**：严格的 JSON 格式验证和错误处理

## 学习资源

- [OpenAI Function Calling 官方文档](https://platform.openai.com/docs/guides/function-calling)
- [OpenAI Playground](https://platform.openai.com/playground/prompts?models=gpt-4.1)
- [DeepSeek Function Calling 文档](https://api-docs.deepseek.com/guides/function_calling)
- [Hugging Face 模型查询](https://huggingface.co/models?other=function+calling&sort=trending)

## 总结

Function Calling 通过标准化的 JSON Schema 格式，解决了传统提示词方式存在的繁琐、不标准、约束力低等问题。通过模型微调确保输出格式的一致性，为大模型与外部工具的集成提供了可靠的标准化方案。