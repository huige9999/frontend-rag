# 前端服务监控 — 课程笔记导航

> 从零搭建前端监控 SDK，涵盖错误监控、性能监控、用户行为采集、数据上报等完整链路。

## 📋 课程目录

| 序号 | 主题 | 关键内容 |
|:---:|------|---------|
| 01 | [前端服务监控概述](01.前端服务监控概述.md) | 监控体系介绍、阿里 ARMS / Sentry 使用、SDK 与埋点概念 |
| 02 | [监控SDK环境搭建](02.监控SDK环境搭建.md) | SDK 项目结构、Rollup 打包配置（IIFE/ESM/UMD）、Vue2 测试项目 |
| 03 | [JS错误的监听](03.JS错误的监听.md) | window.onerror、addEventListener('error')、Promise 异常、Vue 错误捕获 |
| 04 | [JS错误采集与事件路径的处理](04.JS错误采集与事件路径的处理.md) | DOM 事件采集、事件路径（CSS 选择器）计算、堆栈解析、错误数据格式 |
| 05 | [数据上报](05.数据上报.md) | sendBeacon、requestIdleCallback、延迟批量上报、Node.js 服务端接收、MongoDB 存储 |
| 06 | [页面性能监控](06.页面性能监控.md) | PerformanceObserver、web-vitals 库、FCP / LCP / CLS / FID / TTFB 指标采集 |
| 07 | [用户行为收集与埋点](07.用户行为收集与埋点.md) | 手动埋点 tracker()、自动埋点 autoTracker、防抖机制、data-tracker 标记 |
| 08 | [页面访问痕迹采集](08.页面访问痕迹采集.md) | PV/UV 采集、页面停留时长、hash/popstate 路由变更、Vue Router 集成 |
| 09 | [api远程请求数据采集](09.api远程请求数据采集.md) | XHR 拦截重写、Fetch 拦截重写、请求耗时/状态码/URL 采集 |

## 🏗️ 整体架构

```
┌─────────────── 前端监控 SDK ───────────────┐
│                                             │
│  ┌──────────┐  ┌───────────┐  ┌──────────┐ │
│  │ 错误监控  │  │ 性能监控   │  │ 行为采集  │ │
│  │ (error)  │  │(performance)│ │ (action) │ │
│  └────┬─────┘  └─────┬─────┘  └────┬─────┘ │
│       │              │              │        │
│  ┌────┴──────────────┴──────────────┴─────┐ │
│  │              数据上报 (report)           │ │
│  │   sendBeacon / requestIdleCallback      │ │
│  └──────────────────┬─────────────────────┘ │
└─────────────────────┼───────────────────────┘
                      │
                      ▼
              ┌───────────────┐
              │  Node.js 服务  │
              │  (MongoDB)    │
              └───────────────┘
```

## 🛠️ 技术栈

- **SDK 构建**: Rollup（IIFE / ESM / UMD 三种输出格式）
- **前端框架**: Vue 2（测试项目）
- **性能指标**: web-vitals 库 + PerformanceObserver API
- **数据上报**: Navigator.sendBeacon + requestIdleCallback
- **服务端**: Express + MongoDB
- **第三方监控参考**: 阿里 ARMS、Sentry
