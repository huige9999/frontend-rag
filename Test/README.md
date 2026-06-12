# 前端测试框架课程课件导航

> 本目录整理了前端测试框架（Jest）课程的全部课件与关键代码，共 5 章 27 节课。

---

## 📖 第一章：前端测试框架概述

| 序号 | 课程 | 文件 | 简介 |
|:---:|------|------|------|
| 1-1 | 测试基本认知 | [1-1-测试基本认知.md](1-1-测试基本认知.md) | 介绍测试的基本概念，包括为什么需要测试、测试的四种类型（静态测试、单元测试、集成测试、E2E测试）以及 TDD 和 BDD 两种测试驱动开发模式 |
| 1-2 | 前端自动化测试 | [1-2-前端自动化测试.md](1-2-前端自动化测试.md) | 讲解单元测试在前端的重要性，梳理瀑布、敏捷、DevOps 三种软件开发模型的演变与自动化测试的关系，概览 Jest、Mocha、Cypress 等主流前端测试框架 |

---

## 📖 第二章：Jest 基础实践

| 序号 | 课程 | 文件 | 简介 |
|:---:|------|------|------|
| 2-1 | Jest 基础使用 | [2-1-Jest基础使用.md](2-1-Jest基础使用.md) | 介绍 Jest 的安装、test/it/expect 等全局方法的使用，以及通过 describe 对测试用例进行分组 |
| 2-2 | 匹配器 | [2-2-匹配器.md](2-2-匹配器.md) | 讲解 Jest 中丰富的匹配器体系，包括 toBe、toEqual、布尔值、数值、字符串、数组、异常及非对称匹配器的用法 |
| 2-3 | 生命周期方法 | [2-3-生命周期方法.md](2-3-生命周期方法.md) | 详解 beforeEach/afterEach 和 beforeAll/afterAll 生命周期钩子的使用顺序和分组作用域 |
| 2-4 | 模拟函数 | [2-4-模拟函数.md](2-4-模拟函数.md) | 介绍 jest.fn 创建模拟函数，通过 mockReturnValue、mockImplementation 等方法控制函数行为 |
| 2-5 | 模拟模块 | [2-5-模拟模块.md](2-5-模拟模块.md) | 讲解 jest.mock 模拟第三方模块（如 axios）和文件模块的方法 |
| 2-6 | Jest 配置文件 | [2-6-Jest配置文件.md](2-6-Jest配置文件.md) | 介绍 jest.config.js 的生成与常用配置项，包括 collectCoverage、coverageThreshold、testMatch 等 |

---

## 📖 第三章：Jest 进阶知识

| 序号 | 课程 | 文件 | 简介 |
|:---:|------|------|------|
| 3-1 | 整合 TypeScript | [3-1-整合TypeScript.md](3-1-整合TypeScript.md) | 介绍如何在 TypeScript 项目中配置和使用 Jest，包括 ts-jest 的配置与 TS 类型检查 |
| 3-2 | 浏览器环境下的测试 | [3-2-浏览器环境下的测试.md](3-2-浏览器环境下的测试.md) | 讲解如何使用 jsdom 模拟浏览器环境，测试 localStorage、DOM 操作和 fetch 请求等浏览器 API |
| 3-3 | 模拟计时器 | [3-3-模拟计时器.md](3-3-模拟计时器.md) | 介绍 jest.useFakeTimers 模拟定时器，加速 setTimeout/setInterval 相关测试 |
| 3-4 | 模拟 ES6 类 | [3-4-模拟ES6类.md](3-4-模拟ES6类.md) | 讲解如何使用 jest.mock 模拟 ES6 Class，控制类实例的行为进行隔离测试 |
| 3-5 | 整合 webpack 综合练习 | [3-5-整合webpack综合练习.md](3-5-整合webpack综合练习.md) | 综合运用 Jest + TypeScript + webpack，对飞鸟游戏项目进行完整的单元测试实战 |
| 3-6 | 测试 React 组件 | [3-6-测试React组件.md](3-6-测试React组件.md) | 使用 Testing Library 与 Jest 配合测试 React 组件，涵盖 render/screen/fireEvent 及 msw 模拟服务器 |
| 3-7 | 测试 Hook | [3-7-测试Hook.md](3-7-测试Hook.md) | 使用 renderHook 和 act 测试 React 自定义 Hook，测试增减、设置、重置及异步操作 |
| 3-8 | 测试快照 | [3-8-测试快照.md](3-8-测试快照.md) | 介绍 Jest 快照测试的原理与实践，包括生成、更新快照及在非 UI 场景中的扩展应用 |
| 3-9 | 测试 Vue 组件 | [3-9-测试Vue组件.md](3-9-测试Vue组件.md) | 使用 Vue Test Utils 测试 Vue 组件，涵盖 shallowMount/mount 及多个组件的完整测试示例 |

---

## 📖 第四章：实战项目

| 序号 | 课程 | 文件 | 简介 |
|:---:|------|------|------|
| 4-1 | JavaScript 库设计 | [4-1-JavaScript库设计.md](4-1-JavaScript库设计.md) | 讲解如何设计一个 JavaScript 工具库，明确功能需求与模块划分 |
| 4-2 | 项目开发与测试 | [4-2-项目开发与测试.md](4-2-项目开发与测试.md) | 使用 TDD 方式开发工具库的字符串、数组和函数模块，边开发边编写单元测试 |
| 4-3 | 开源协议与准备工作 | [4-3-开源协议与准备工作.md](4-3-开源协议与准备工作.md) | 介绍常见开源协议（MIT、Apache、GPL）的选择，以及开源项目的 README、LICENSE 等准备工作 |
| 4-4 | 开源代码 | [4-4-开源代码.md](4-4-开源代码.md) | 讲解如何将项目代码推送到 GitHub 并进行开源管理 |
| 4-5 | 发布代码 | [4-5-发布代码.md](4-5-发布代码.md) | 使用 Rollup 打包工具库并通过 npm 发布，支持 ESM/CJS/UMD 多种模块格式 |
| 4-6 | 搭建文档网站 | [4-6-搭建文档网站.md](4-6-搭建文档网站.md) | 使用 VitePress 搭建项目文档网站，配置导航、侧边栏和 Markdown 扩展 |
| 4-7 | 部署文档网站 | [4-7-部署文档网站.md](4-7-部署文档网站.md) | 通过 GitHub Actions 自动构建并将 VitePress 文档部署到 GitHub Pages |
| 4-8 | 部署文档网站补充内容 | [4-8-部署文档网站补充内容.md](4-8-部署文档网站补充内容.md) | 针对用户/组织站点需要将部署内容放在根目录的问题，更新 GitHub Actions 工作流配置 |

---

## 📖 第五章：Jest 扩展知识

| 序号 | 课程 | 文件 | 简介 |
|:---:|------|------|------|
| 5-1 | Jest 测试 Express 应用 | [5-1-Jest测试Express应用.md](5-1-Jest测试Express应用.md) | 使用 Jest + Supertest 对 Express + MongoDB 后端应用进行集成测试，涵盖分页查询、详情获取、新增验证等接口测试场景 |
| 5-2 | 手写简易版测试框架 | [5-2-手写简易版测试框架.md](5-2-手写简易版测试框架.md) | 从零搭建简易测试框架 Best，逐步演进并集成 jest-circus/expect/jest-mock 等模块实现完整测试能力 |

---

> 📌 每个课件文件包含：课程讲义内容 + 关键代码（源码与测试文件）
