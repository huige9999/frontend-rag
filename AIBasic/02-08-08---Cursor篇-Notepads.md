# [Cursor篇] Notepads - 在聊天和代码编辑器之间构建并共享上下文

## 官方描述

> Craft and share context between chat and composers

在聊天和代码编辑器之间构建并共享上下文。

## 什么是 Notepads？

**Notepad** 是笔记本功能，你可以在笔记本里面记录一些内容，供 AI 作为参考信息。

笔记内容会自动注入到 AI 的上下文中，影响它对代码、问题和对话的理解。简单说，就是帮 AI"提前看过项目文档"。

## 启用 Notepads

首先在资源管理器需要开启 Notepads：

![启用Notepads](https://xiejie-typora.oss-cn-chengdu.aliyuncs.com/2025-07-01-005820.png)

## 使用场景举例

### 场景：API 接口文档管理

假设创建一个如下的 Notepad：

```
📌 模块：用户管理模块

✅ 接口：GET /api/users

- 返回活跃用户列表（isActive = true）
- 支持通过 query 参数筛选 role
- 默认分页大小为 20，最大为 100

📦 响应字段说明：

- id: string，用户唯一标识
- name: string，用户名
- roles: string[]，可包含 "admin" | "editor" | "viewer"
- createdAt: ISO8601 字符串
- isActive: boolean，是否处于激活状态

⚠️ 注意事项：

- 部分老用户的 `roles` 字段可能为 null，请使用空数组兜底处理
- 若无数据，返回空数组 `[]`，不会返回 null

📍 示例请求：
GET /api/users?role=editor&page=2
```

### 与 AI 对话

当你给 AI 提问：

> "为什么我的 `users.length` 总是比预期少？"

AI 会根据 Notepad 中的信息回答：

> "你请求的是 `/api/users`，根据笔记说明它只返回 `isActive = true` 的用户，建议确认是否有部分用户是未激活状态。"

## Notepads 与 CursorRules 的区别

### Notepads 的特点

- 就像你平时写的一些项目备注（比如接口参数说明、组件行为约定、状态流程图解等），可以作为 Notepad 保存
- 启用这个功能后，你每次打开 Chat 与 AI 对话时，Cursor 会自动把这些 Notepad 笔记传给 AI 模型
- AI 就像提前"读过你写的文档"，可以在回答问题时参考这些内容，给出更准确、更符合项目上下文的回答

### 对比总结

| 特性 | CursorRules | Notepads |
|------|-------------|----------|
| 作用 | 行为规范 | 上下文信息 |
| 目的 | 指导 AI 应该怎么做 | 告诉 AI 发生了什么 |
| 使用方式 | 规范和约束 | 信息和背景 |
| AI 处理 | 按规范执行 | 结合背景信息判断该做什么 |

## 创建 Notepads

> [创建步骤部分原文档未详细说明，需要参考 Cursor 官方文档补充]

## 最佳实践

1. **项目约定记录**：将接口规范、组件使用说明等关键信息记录在 Notepads 中
2. **业务逻辑说明**：记录复杂的业务规则和注意事项
3. **API 文档**：将常用的接口参数、响应格式等信息整理成 Notepad
4. **代码约定**：记录编码规范、命名约定等团队共识
5. **注意事项**：记录常见的坑和需要注意的地方

## 总结

Notepads 是一个强大的上下文共享工具，它让 AI 能够：

- 提前了解项目背景信息
- 给出更准确的建议和回答
- 理解项目的特定约定和规范
- 避免常见的错误和陷阱

通过合理使用 Notepads，可以显著提升 AI 辅助编程的效率和准确性。