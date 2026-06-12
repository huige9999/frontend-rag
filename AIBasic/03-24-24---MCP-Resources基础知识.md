# MCP Resources 基础知识

## 概述

资源（Resources）是 MCP Server 向客户端应用提供信息的一种形式，允许 MCP 服务器暴露各种类型的数据供 LLM 使用和访问。

### 资源类型示例

- **File contents（文件内容）**：本地的 `.txt`、`.md`、`.js`、`.json` 等文件
- **Database records（数据库记录）**：SQL 查询结果或表格内容
- **Screenshots and images（截图和图像）**：图像类资源

## URI（统一资源标识符）

### 定义

URI（Uniform Resource Identifier）是"统一资源标识符"，用于互联网上某个资源的唯一标识。

### 格式

```
[protocol]://[host]/[path]
```

### 标准示例

- `file:///home/user/documents/report.pdf`：文件资源（无 host）
- `postgres://database/customers/schema`：数据库资源
- `screen://localhost/display1`：屏幕资源

### MCP 中的 URI 规则

在 MCP 协议中，**不强制 URI 规则**，允许 Server 自定义，例如：
- `notebook://cell/123`
- `log://app/service/error`
- `chat://conversation/abc123`

## 资源类型

MCP 中的资源分为两类：文本资源和二进制资源。

### 文本资源

文本资源是用 **UTF-8 编码**的纯文本数据，适合展示、编辑、分析。

- **Source code（源代码）**：`.js`、`.ts`、`.py`、`.cpp` 等文件内容，作为 LLM 的上下文
- **Configuration files（配置文件）**：`.env`、`config.yaml`、`tsconfig.json` 等
- **Log files（日志文件）**：运行日志、错误日志，用于分析或总结
- **JSON/XML data**：结构化的文本格式，用于数据交换
- **Plain text（纯文本）**：普通的 `.txt` 文件或文档片段

### 二进制资源

二进制资源是原始的二进制数据，**必须**通过 **base64 编码**传输。

- **Images（图像）**：PNG、JPG、SVG 等，用于截图、照片识别等任务
- **PDFs**：格式化文档或技术手册
- **Audio files（音频）**：MP3、WAV，用于语音识别、分析等
- **Video files（视频）**：MP4、WEBM，用于视频摘要或分析
- **Other non-text formats**：`.zip` 压缩包、`.docx` 文档、`.exe` 文件等

## 发现资源

MCP 提供了两种发现资源的方式：

### 1. 直接资源

服务器直接暴露一组固定资源，通过 JSON-RPC 方法 `resources/list` 提供给客户端。

#### 资源对象结构

```json
{
  "uri": "string",         // 资源的唯一 URI（例如 file:///xxx）
  "name": "string",        // 人类可读的名称
  "description?": "string", // 可选描述，解释用途或内容
  "mimeType?": "string",   // MIME 类型，如 text/plain, image/png
  "size?": "number"        // 文件大小（单位：字节）
}
```

#### 示例

```json
{
  "uri": "file:///logs/build.log",
  "name": "构建日志",
  "description": "包含最近一次构建的所有输出信息",
  "mimeType": "text/plain",
  "size": 18423
}
```

### 2. 资源模板

（后续课程内容）

## 读取资源

### 请求格式

客户端通过发送 JSON-RPC 请求读取资源：

```json
{
  "method": "resources/read",
  "params": {
    "uri": "file:///logs/error.log"
  }
}
```

### 响应格式

服务器返回包含 `contents` 数组的 JSON 对象：

```json
{
  "contents": [
    {
      "uri": "string",          // 必填，资源的唯一 URI
      "mimeType?": "string",    // 可选，MIME 类型
      
      // 以下两者二选一
      "text?": "string",        // 文本资源内容（UTF-8 编码）
      "blob?": "string"         // 二进制资源内容（Base64 编码）
    }
  ]
}
```

### 多资源返回

MCP 支持 **一次 `resources/read` 返回多个资源内容**。

例如：读取一个目录 `file:///project/src/`，返回值可能是里面多个文件的内容（如多个 `.ts` 文件）

## 实践示例

### 服务器代码示例

```javascript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({
  name: "resources-server",
  version: "0.1.0",
});

// 注册文本资源
server.registerResource(
  "香蕉手机",
  "bananaphone://info",
  {
    title: "香蕉手机信息",
    description: "香蕉手机的产品以及特性的介绍文字",
    mimeType: "text/plain",
  },
  async (uri) => {
    const content = await readResourse("src/assets", "bananaphone.txt", false);
    return {
      contents: [
        {
          uri: uri.href,
          name: "香蕉手机信息",
          text: content,
        },
      ],
    };
  }
);

// 注册图像资源（二进制）
server.registerResource(
  "书籍图片",
  "pics://books",
  {
    title: "书籍图片",
    description: "一张有很多书籍的图片",
    mimeType: "image/jpeg",
  },
  async (uri) => {
    const content = await readResourse("src/assets", "books.jpeg", true);
    return {
      contents: [
        {
          uri: uri.href,
          name: "书籍图片",
          blob: content,  // Base64 编码的二进制数据
        },
      ],
    };
  }
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

### 工具函数示例

```javascript
import fs from "fs";
import path from "path";

/**
 * 读取资源文件
 * @param {string} filepath - 文件的路径
 * @param {string} filename - 文件的名称
 * @param {boolean} isBinary - 是否是二进制数据
 */
export async function readResourse(filepath, filename, isBinary) {
  try {
    const filePath = path.join(process.cwd(), filepath, filename);
    let content = null;
    
    if (isBinary) {
      // 二进制数据转换为 base64 编码
      const buffer = fs.readFileSync(filePath);
      content = buffer.toString("base64");
    } else {
      // 文本数据直接读取
      content = fs.readFileSync(filePath, "utf8");
    }
    return content;
  } catch (err) {
    return `读取资源失败：${err.message}`;
  }
}
```

## 依赖配置

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

## 课堂练习

为 MCP 服务器注册资源上下文。

### 调试工具

```bash
npx @modelcontextprotocol/inspector
```

### 在线 Base64 图片预览

https://jaredwinick.github.io/base64-image-viewer/

格式：
```
data:image/png;base64,<base64编码>
```

## 总结

- Resources 是 MCP Server 向客户端提供信息的重要方式
- URI 用于唯一标识资源，MCP 允许自定义 URI 格式
- 资源分为文本（UTF-8）和二进制（Base64 编码）两种类型
- 通过 `resources/list` 发现资源，`resources/read` 读取资源
- 支持一次请求返回多个资源内容
