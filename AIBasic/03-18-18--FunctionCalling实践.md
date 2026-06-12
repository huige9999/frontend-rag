# Function Calling 实践

## 知识点

### 1. Function Calling 的背景与价值

在 Function Calling 出现之前，开发者通过**提示词**的形式将工具信息传递给大模型，这种方式存在以下问题：

1. **繁琐**：需要编写大量提示词来约束大模型输出
2. **不标准**：不同开发者的提示词描述千差万别
3. **约束力不高**：即使使用强制语气，大模型也可能不按要求回复

### 2. Function Calling 标准化

OpenAI 在 2023 年 6 月推出 Function Calling，通过 JSON Schema 格式实现标准化：

#### 工具定义（Tool Definition）
```javascript
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

#### 工具调用响应（Tool Call Response）
```javascript
[{
    "type": "function_call",
    "id": "fc_12345xyz",
    "call_id": "call_12345xyz",
    "name": "get_weather",
    "arguments": "{\"latitude\":48.8566,\"longitude\":2.3522}"
}]
```

### 3. Function Calling 的原理

Function Calling 能约束大模型输出的根本原因是**大模型微调**。正因如此，并非所有模型都支持 Function Calling。可以在 [Hugging Face](https://huggingface.co/models?other=function+calling&sort=trending) 查询模型是否支持该特性。

> Hugging Face 是目前最主流的开源 AI 模型托管与使用平台，相当于 AI 界的 Github。

### 4. 不同模型的 Function Calling 格式

#### DeepSeek 格式
```javascript
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

#### GPT 格式
```javascript
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

#### Claude 格式
```javascript
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

## 实践案例：智能聊天机器人

### 技术栈
- **前端**：Vue 3 + TypeScript + Vite
- **后端**：Node.js + Express
- **大模型**：DeepSeek API
- **第三方服务**：和风天气 API、百度翻译 API

### 项目结构
```
chat-bot/
├── client/          # Vue 前端项目
│   ├── src/
│   │   └── App.vue  # 主应用组件
├── server/          # Express 后端项目
│   ├── routes/
│   │   └── index.js         # 路由处理
│   ├── utils/
│   │   ├── LLM.js           # 大模型调用封装
│   │   ├── tools.js         # 工具定义
│   │   ├── wetherHandler.js # 天气服务处理
│   │   └── translateHandler.js # 翻译服务处理
│   └── .env                # 环境变量配置
```

### 核心代码实现

#### 1. 工具定义（tools.js）

定义两个工具：天气查询和中文翻译：

```javascript
module.exports = [
  {
    type: "function",
    function: {
      name: "getWeather",
      description: "获取指定城市和日期的天气信息",
      parameters: {
        type: "object",
        properties: {
          city: {
            type: "string",
            description: "城市名称，如：北京、上海、广州",
          },
          date: {
            type: "string",
            description: "日期，只能是：今天、明天、后天",
          },
        },
        required: ["city", "date"],
      },
    },
  },
  {
    type: "function",
    function: {
      name: "translate",
      description: "将指定的文本从中文翻译到英文",
      parameters: {
        type: "object",
        properties: {
          input: {
            type: "string",
            description: "需要翻译的文本",
          },
        },
        required: ["input"],
      },
    },
  },
];
```

#### 2. 大模型调用封装（LLM.js）

核心功能：
- 带超时机制的 fetch 封装
- 流式响应处理
- 工具调用信息收集

```javascript
async function callLLM(messages, tools = null, callback) {
  const requestBody = {
    model: LLM_MODEL,
    messages,
    stream: true,
  };

  if (tools) requestBody.tools = tools;

  const response = await fetchWithTimeout(LLM_ENDPOINT, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Authorization: `Bearer ${process.env.DEEPSEEK_API_KEY}`,
    },
    body: JSON.stringify(requestBody),
  });

  // 流式处理
  const reader = response.body.getReader();
  const decoder = new TextDecoder("utf-8");
  let fullResponse = "";
  let toolCalls = [];

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
      const delta = data.choices?.[0]?.delta;

      if (delta) {
        if (delta.content) {
          fullResponse += delta.content;
          callback?.(delta.content);
        }

        if (delta.tool_calls) {
          for (const toolCall of delta.tool_calls) {
            const existingCall = toolCalls.find(
              (call) => call.index === toolCall.index
            );

            if (existingCall) {
              if (toolCall.function?.name) {
                existingCall.function.name = toolCall.function.name;
              }
              if (toolCall.function?.arguments) {
                existingCall.function.arguments += toolCall.function.arguments;
              }
            } else {
              toolCalls.push({
                index: toolCall.index,
                id: toolCall.id,
                type: "function",
                function: {
                  name: toolCall.function?.name,
                  arguments: toolCall.function?.arguments,
                },
              });
            }
          }
        }
      }
    }
  }

  if (toolCalls.length > 0)
    return { content: fullResponse, tool_calls: toolCalls };

  return fullResponse;
}
```

#### 3. 天气服务处理（wetherHandler.js）

对接和风天气 API：

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

#### 4. 翻译服务处理（translateHandler.js）

对接百度翻译 API：

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

#### 5. 路由处理（index.js）

核心业务逻辑：

```javascript
router.post("/ask", async (req, res) => {
  const question = req.body.question || "";
  
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");

  const messages = [...conversations, { role: "user", content: question }];

  const response = await callLLM(messages, toolList, (chunk) => {
    res.write(`${JSON.stringify({ response: chunk })}\n`);
  });

  if (response.tool_calls) {
    const toolResults = [];

    for (const toolCall of response.tool_calls) {
      const functionName = toolCall.function.name;
      const args = JSON.parse(toolCall.function.arguments);

      if (toolsMap[functionName]) {
        const result = await toolsMap[functionName](args);
        toolResults.push({
          tool_call_id: toolCall.id,
          role: "tool",
          content: result,
        });
      }
    }

    messages.push(
      {
        role: "assistant",
        content: response.content,
        tool_calls: response.tool_calls,
      },
      ...toolResults
    );

    const finalResponse = await callLLM(messages, toolList, (chunk) => {
      res.write(`${JSON.stringify({ response: chunk })}\n`);
    });

    conversations.push(
      { role: "user", content: question },
      {
        role: "assistant",
        content: response.content,
        tool_calls: response.tool_calls,
      },
      ...toolResults,
      { role: "assistant", content: finalResponse }
    );
  } else {
    conversations.push(
      { role: "user", content: question },
      { role: "assistant", content: response }
    );
  }

  res.end();
});
```

### Function Calling 工作流程

```
用户输入 → LLM判断是否需要工具 → 需要工具
                              ↓
                        返回工具调用请求
                              ↓
                        执行工具调用
                              ↓
                        将工具结果返回给LLM
                              ↓
                        LLM生成最终回复
                              ↓
                        保存会话历史
```

### 环境配置

需要配置以下环境变量（.env 文件）：

```env
LLM_ENDPOINT=https://api.deepseek.com/chat/completions
LLM_MODEL=deepseek-chat
DEEPSEEK_API_KEY=your_deepseek_api_key
HEFENG_API_KEY=your_hefeng_api_key
BAIDU_APP_ID=your_baidu_app_id
BAIDU_SECRET_KEY=your_baidu_secret_key
LLM_TIMEOUT_MS=60000
```

## 学习资源

- [OpenAI Function Calling 官方文档](https://platform.openai.com/docs/guides/function-calling?api-mode=responses&lang=javascript#function-calling-steps)
- [OpenAI Playground](https://platform.openai.com/playground/prompts?models=gpt-4.1)
- [Hugging Face 模型查询](https://huggingface.co/models?other=function+calling&sort=trending)
- [DeepSeek Function Calling 文档](https://api-docs.deepseek.com/guides/function_calling)

## 总结

1. **Function Calling** 提供了标准化的工具调用机制，解决了提示词方式的繁琐和不标准问题
2. 不同模型的 Function Calling 格式存在差异，需要针对具体模型调整代码
3. 通过流式响应处理可以实现实时的用户体验
4. 工具调用的结果需要回传给大模型，让模型生成最终回复
5. 完整的会话历史管理是保持上下文连续性的关键
