# 19. MCP stdio - 标准输入输出通信

## 课程概述

本课程介绍MCP（Model Context Protocol）中的stdio通信机制，讲解进程间通信的基本概念和实现方式。

## 核心知识点

### 1. 进程概念

**进程定义：**
- 执行一个应用程序就会启动一个进程
- 操作系统会为进程分配内存空间和系统资源
- 应用程序执行完毕后，系统分配给进程的资源会被回收

**进程持久化原理：**
- 进程通过监听（listening）保持运行状态
- 类似微信、QQ等应用启动后持续运行
- 在Node.js中可以通过监听stdin实现：

```javascript
// 监听输入
process.stdin.on("data", () => {});
```

### 2. 父子进程模型

**基本概念：**
- 一个进程可以启动另一个进程
- 形成父子进程关系
- 例如：控制台（父进程）启动node程序（子进程）

```bash
node index.js
```

**进程间通信要求：**
- 进程不能结束（需要保持监听状态）
- 进程间需要通信机制

### 3. stdio通信机制

**stdio定义：**
- **st**an**d**ard **i**nput and **o**utput（标准输入输出）
- 每个进程启动后都会有两个对外通信接口：
  - 标准输入接口：standard in
  - 标准输出接口：standard output

**通信原理：**
- 父进程通过stdin向子进程发送数据
- 子进程通过stdout向父进程返回数据
- 形成双向通信通道

## 实践代码

### 服务端代码 (server.js)

```javascript
process.stdin.setEncoding("utf8");
process.stdin.on("data", (data) => {
  // 将收到的data信息做一个简单的处理，作为响应信息
  const response = `AI: ${data
    .replace(/[?？吗]/g, "")
    .replace(/我/g, "你")
    .replace(/你/g, "我")}\n`;
  // 将响应信息输出给原来的进程
  process.stdout.write(response);
});
```

**功能说明：**
- 监听标准输入
- 对收到的消息进行简单处理（模拟AI回复）
- 通过标准输出返回处理结果

### 客户端代码 (client.js)

```javascript
const { spawn } = require("child_process");

// 启动 server.js 子进程
const serverProcess = spawn("node", ["server.js"]); // node server.js

// 监听服务端的响应
// 数据是哪个进程给我的？-> serverProcess
// 我这个进程又将数据输出到了哪个进程？-> process.stdin
serverProcess.stdout.on("data", (data) => {
  process.stdin.write(data.toString()); // 输出到当前进程的标准输入
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

**功能说明：**
- 使用`spawn`启动server.js子进程
- 监听子进程的stdout输出
- 定时向子进程发送测试消息
- 将子进程的响应输出到当前进程

## 技术特点

### stdio通信优势
- **高效**：直接使用系统标准输入输出，性能优异
- **简洁**：无需复杂的网络配置和协议处理
- **实时**：数据传输延迟低

### 局限性
- 仅适用于本地进程间通信
- 不支持网络通信
- 父子进程必须运行在同一台机器上

## 应用场景

1. **CLI工具开发**：命令行工具与用户交互
2. **进程间协作**：不同进程间的数据交换
3. **MCP协议实现**：AI模型与外部工具通信的基础
4. **开发调试**：进程状态监控和调试信息输出

## 总结

stdio通信是进程间通信最基础和重要的方式之一，通过标准输入输出实现进程间的数据交换。在MCP协议中，stdio为AI模型与外部工具提供了简单高效的通信通道，是实现模型能力扩展的重要基础设施。