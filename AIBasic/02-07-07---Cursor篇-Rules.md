# Cursor Rules 功能详解

## 概述

Rules 是 Cursor 的一个核心功能，用于控制 AI 的行为。这些规则会在每次对话时自动应用，让 AI 按照预定的方式工作。Rules 本质上属于提示词工程的一部分。

## 主要用途

- **设定编码风格**：统一代码格式和命名规范
- **定制文档格式**：规范文档结构和内容要求
- **统一团队流程**：确保团队开发流程一致性
- **个性化代码审查**：自定义代码审查标准和要求

## 设置方式

Cursor 提供三种规则设置方式：

### 1. 全局规则

全局规则对所有项目都有效，通过 Cursor 设置界面进行配置。

### 2. 项目规则

每个项目独立的规则，项目之间不共享。在项目根目录的 `.cursor/rules` 文件夹中创建 `.mdc` 文件。

### 3. 规则文件（已废弃）

为兼容性保留的 `.cursorrules` 文件，未来可能完全移除，建议迁移到新系统。

## 项目规则详解

### 规则文件格式

项目规则使用 `.mdc` 格式（基于 Markdown），支持四种规则类型：

1. **Always**：自动附加到所有聊天和命令请求（适用于全局规则）
2. **Auto Attached**：基于文件模式匹配自动触发，如 `.tsx`, `.json`, `Test.cpp`
3. **Agent Requested**：针对特定任务场景，需要提供任务描述
4. **Manual**：需要手动提及才会被包含，适用于特殊场景的临时规则

### 规则文件结构

```yaml
---
description: 规则描述
globs: 文件匹配模式
alwaysApply: 是否总是应用
---
# 规则内容（Markdown格式）
```

### 核心功能

- **规则内容**：使用 Markdown 格式编写规则
- **规则引用**：用 `@file` 引用其他规则文件，实现规则链式调用

## 实际应用案例

### 项目基础开发规范（`.cursor/rules/base.mdc`）

```markdown
# 项目基础开发规范

## 基础开发规则
- 使用 ESLint 和 Prettier 进行代码格式化
- 文件命名采用 kebab-case
- 导入语句按类型分组并排序
- 避免魔法数字，使用常量定义
- 函数长度不超过 50 行
```

### React 组件规范（`.cursor/rules/react.mdc`）

```markdown
Description: React 组件开发规范，确保组件的可维护性和性能
Globs: src/components/**/*.tsx, src/features/**/*.tsx

# React 组件开发规范
- 优先使用函数组件和 Hooks
- 组件文件结构：
  1. 类型定义
  2. 常量声明
  3. 组件实现
  4. 样式定义
- Props 必须使用 interface 定义类型
- 使用 React.memo() 优化渲染性能
- @base.mdc
```

### TypeScript 规范（`.cursor/rules/typescript.mdc`）

```markdown
Description: TypeScript 开发规范，确保类型安全和代码质量
Globs: **/*.ts, **/*.tsx

# TypeScript 规则
- 禁用 any 类型，使用 unknown 替代
- 启用 strict 模式
- 使用类型推导减少冗余类型声明
- 公共函数必须包含 JSDoc 注释
- 使用 type 而不是 interface（除了 Props）
```

### 测试规范（`.cursor/rules/test.mdc`）

```markdown
Description: 单元测试和集成测试规范
Globs: src/**/*.test.ts, src/**/*.test.tsx

# 测试规则
- 使用 React Testing Library
- 测试文件与源文件同目录
- 测试用例结构：
  1. 准备测试数据
  2. 执行被测试代码
  3. 断言结果
- Mock 外部依赖使用 MSW
- 测试覆盖率要求：
  * 语句覆盖率 > 80%
  * 分支覆盖率 > 70%
  * 函数覆盖率 > 90%
```

### 文档规范（`.cursor/rules/doc.mdc`）

```markdown
Description: 项目文档编写规范
Globs: **/*.md

# 文档规则
- 使用中文编写
- 包含以下部分：
  1. 功能说明
  2. 使用示例
  3. API 文档
  4. 注意事项
- 代码块标注语言类型
- 配置说明使用表格格式
- 重要信息使用引用块标注
```

## 规则迁移指南

从旧版 `.cursorrules` 迁移到新系统：

1. 将现有规则按功能拆分为多个文件
2. 在 `.cursor/rules` 目录创建对应规则
3. 在 Globs 字段指定适用范围
4. 通过 `@file` 引用保持规则链式调用

## 使用技巧

### 在聊天中引用规则

通过在聊天中使用 `@Cursor Rules`，明确告诉 AI 按照项目规范生成或修改代码，使输出更加贴合项目实际需求。

### 规则最佳实践

1. **分层设计**：将规则按功能模块分离，便于管理和维护
2. **合理引用**：通过 `@file` 引用避免规则重复
3. **精确匹配**：使用合适的 Globs 模式确保规则在正确场景触发
4. **团队协作**：将规则纳入版本控制，确保团队规范统一

## 代码示例

遵循规则的 React 组件实现：

```typescript
// 类型定义
type ButtonProps = {
  children: React.ReactNode;
  onClick?: () => void;
  type?: 'button' | 'submit' | 'reset';
  className?: string;
};

// 组件实现
import React from 'react';

const Button: React.FC<ButtonProps> = React.memo(function Button({ 
  children, 
  onClick, 
  type = 'button', 
  className = '' 
}) {
  return (
    <button
      type={type}
      onClick={onClick}
      className={`px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition ${className}`}
    >
      {children}
    </button>
  );
});

export default Button;
```

## 总结

Cursor Rules 功能通过声明式配置，让 AI 编程助手能够更好地理解和遵循项目规范。合理使用 Rules 可以：

- 提高代码质量和一致性
- 减少重复性工作
- 加速团队协作
- 个性化 AI 辅助体验

建议从项目开始就建立完善的 Rules 体系，并在开发过程中持续优化。