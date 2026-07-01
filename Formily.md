# Formily

## 1. 目录结构整理

```
Formily（共 17 章，目录编号 02~20，缺 01/15/16）
│
├── 第一模块 从零实现表单引擎 MyFormily（02~08）
│   │   用 React + TS 从零手写一个类 Formily 的表单引擎，理解底层原理
│   │
│   ├── 02. 通过数据渲染UI页面         项目初始化(Vite+React+TS+Tailwind)、静态表单、Schema 驱动渲染
│   ├── 03. 表单状态模型               Field 类 / Form 类 / createForm 工厂（OOP 状态模型）
│   ├── 04. 发布订阅模式优化           subscribe/emit 事件机制、数据驱动 UI
│   ├── 05. 响应式原理                 Proxy + autorun、observable、依赖收集、disposed
│   ├── 06. 使用高阶组件处理响应式与重渲染  observer HOC、useObserver Hook
│   ├── 07. 表单校验                   validators 配置、同步/异步校验、submit 收敛校验
│   └── 08. 联动反应                   reactions 配置、字段间联动、notify 机制
│
├── 第二模块 学习 Formily 框架（09~14）
│   │   从手写引擎切换到真实 @formily 生态（core/react/antd-v5）
│   │
│   ├── 09. Formily快速开始            三种写法(JsonSchema/JsxField/MarkupSchema)、FormProvider/Field/createSchemaField
│   ├── 10. 精确渲染                   Formily 精准渲染 vs 原生 React 全量渲染（性能对比）
│   ├── 11. 领域模型                   effects 副作用、onFieldValueChange、setFieldState、表单联动
│   ├── 12. 路径系统                   FormPath、8 种匹配模式、query/setValuesIn/getValuesIn
│   ├── 13. 生命周期                   Form/Field 生命周期钩子（onFormInit/onFieldInit 等）
│   └── 14. 协议驱动                   x-reactions 主动/被动模式、x-validator、表达式语法
│
└── 第三模块 组件定制与实战（17~20）
    │   自定义组件 → 复杂组件库 → 综合项目 → 低代码设计器
    │
    ├── 17. 自定义简单组件             connect / mapProps / mapReadPretty、阅读态组件
    ├── 18. 自定义复杂组件             formily-shadcn 组件库（桥接 shadcn/ui）
    ├── 19. 综合项目示例               POMS 采购订单系统（多 Schema 组合 + effects + RemoteSelect）
    └── 20. 低代码的设计与实现          组件注册表、属性配置面板、Schema 转换、可视化设计器
```

> 注：课程目录从 02 开始（无 01），且 14 之后直接跳到 17（缺 15/16）。第一模块的工程名为 `MyFormily`，每章是一个完整可运行的迭代版本；第二、三模块的工程名为 `formily_guide`，是一个逐章累加 step 组件的多页应用（step1~step8 逐步递增）。第 19 章是独立的 POMS 综合项目。

---

## 2. 章节逻辑说明

这门课的主线是 **「先造轮子理解原理 → 再学真实框架 → 最后定制与实战」**。

一个很关键的设计：第一模块不讲 Formily 怎么用，而是**从零手写一个迷你表单引擎 MyFormily**，让你彻底理解「Schema 驱动、状态模型、响应式、校验、联动」这些表单框架的核心概念是怎么一步步长出来的；等原理吃透了，第二模块再切到真实的 `@formily` 生态，你会发现 Formily 的 API 全部对应得上你刚手写的东西；最后第三模块做组件定制和真实项目落地。

### 第一模块 从零实现表单引擎 MyFormily（02~08）：造轮子理解原理

- **02 通过数据渲染UI页面**：先用 Vite 起 React+TS+Tailwind 项目，硬编码一个静态表单，再引入 `FieldSchema` + `componentMap`，改成 Schema 驱动动态渲染。
- **03 表单状态模型**：从扁平 `Record<string, any>` 升级到 OOP——封装 `Field` 类（value/visible/touched/dirty/errors/validating）和 `Form` 类，配合 `createForm` 工厂，用 `forceUpdate` 桥接 React。
- **04 发布订阅模式优化**：去掉层层传递的 `onFieldChange` 回调，给 Field/Form 加 `subscribe/emit`，让组件在 `useEffect` 里自主订阅，实现「谁变谁通知、谁关心谁订阅」的数据驱动 UI。
- **05 响应式原理**：发布订阅的样板代码太多，于是引入 Proxy + `autorun` + `observable`，讲透依赖收集（签到→执行→收集→签退）和 disposed 销毁机制。
- **06 使用高阶组件处理响应式与重渲染**：用 `observer` HOC 和 `useObserver` Hook 桥接 Proxy 响应式与 React 重渲染，实现「零样板代码」自动重渲染。
- **07 表单校验**：Schema 加 `validators`，Field 实现 `validate()`（支持同步+异步），Form 加 `submit` 统一收敛校验，FieldRenderer 展示错误和「校验中」状态。
- **08 联动反应**：Schema 加 `reactions`（dependencies/when/fulfill/otherwise），Field 加 `runReactions`，Form 加 `notify` 广播，实现字段间条件显隐联动并自动清空隐藏值。

**为什么这么安排**：这是一条标准的「**演进式教学**」主线——先用最朴素的方式跑通（02 硬编码 → Schema），再解决状态管理（03 OOP → 04 发布订阅 → 05/06 响应式），再叠加表单核心能力（07 校验 → 08 联动）。每一步都是在解决上一步的痛点（回调地狱 → 样板代码 → 手动订阅），让学生直观感受到「为什么要有响应式系统」。到第 08 章结束时，MyFormily 已经是一个具备「Schema 驱动 + 响应式 + 校验 + 联动」的完整迷你表单框架。

### 第二模块 学习 Formily 框架（09~14）：从手写切换到真实生态

- **09 Formily快速开始**：切换到真实 `@formily/core` + `@formily/react` + `@formily/antd-v5`，演示三种写法——JsonSchema（纯 JSON）、JsxField（`<Field>` JSX 标签）、MarkupSchema（JSX + schema 属性）。
- **10 精确渲染**：用渲染计数器直观对比 Formily（改一个字段只渲染那一个）vs 原生 React（改一个全量重渲染），讲透 Formily 的核心性能优势——精准渲染。
- **11 领域模型**：用 `effects` + `onFieldValueChange` + `form.setFieldState` 实现省市区三级联动、订单自动算总价，这是 Formily 最核心的业务编排能力。
- **12 路径系统**：`FormPath` 的数据路径（点路径/下标/解构/相对）和 8 种匹配模式（全匹配/局部/分组/反向/扩展/范围/转义/解构），配合 `form.query()`、`getValuesIn/setValuesIn`。
- **13 生命周期**：Form 级（onFormInit/Mount/ValuesChange/Validate/Submit...）和 Field 级（onFieldInit/Mount/ValueChange/Validate...）全套生命周期钩子。
- **14 协议驱动**：用纯 JSON Schema 的 `x-reactions`（主动模式 `$self` → `target`、被动模式 `dependencies` → `$deps`、函数模式）和 `x-validator`、`{{}}` 表达式语法，实现纯协议驱动的复杂表单。

**为什么这么安排**：有了第一模块的手写经验，第二模块学真实 Formily 会非常顺畅——你会发现 09 的三种写法就是 Schema 驱动的不同形式，10 的精准渲染就是你手写的 observer 局部更新的工程版，11 的 effects 就是你手写的 reactions/notify，12 的路径系统是 Formily 处理嵌套/数组表单的底层，13 的生命周期是 Formily 的调度中枢，14 的协议驱动则是把所有能力收敛成「一份 JSON 描述一切」的终极形态。这一篇的顺序是「**会用 → 懂优势 → 会编排 → 懂底层路径 → 懂调度 → 终极协议化**」。

### 第三模块 组件定制与实战（17~20）：从定制到落地

- **17 自定义简单组件**：用 `connect` 把任意第三方组件（antd Input/Switch/Select/Rate/Slider）接入 Formily，配合 `mapProps`（属性映射）和 `mapReadPretty`（阅读态）做定制。
- **18 自定义复杂组件**：把整套 shadcn/ui 组件库通过 Formily 的 `connect`/`observer` API 桥接成 `formily-shadcn` 组件库，实现一套与 `@formily/antd-v5` 用法一致的组件体系。
- **19 综合项目示例**：POMS 采购订单管理系统——多 Schema 拆分（basic/products/approval/attachments）+ effects 编排 + 自定义 RemoteSelect（远程搜索+排重）+ 费用汇总，是真实中后台表单的典型落地。
- **20 低代码的设计与实现**：实现一个可视化表单设计器——组件注册表（ComponentMeta）、属性配置面板（SettingsSchema）、FieldNode → Formily JSON Schema 的 `transformToSchema` 转换，对应 `@formily/designable` 的核心思路。

**为什么放最后**：这是全课的「落地应用」段。先学会定制单个组件（17），再做整套组件库（18），再用真实项目把 effects + Schema + 自定义组件串起来（19），最后升华到低代码设计器（20）——从「用 Formily」到「围绕 Formily 造生态」。第 20 章是全课的最高点，把前面所有知识（Schema、组件注册、响应式、协议驱动）综合成一个可视化设计器。

### 主线一句话总结

**造轮子理解原理（02~08 手写 MyFormily）→ 学真实框架（09~14 @formily 生态）→ 定制与落地（17~20 组件库 + 综合项目 + 低代码设计器）**，目标是既懂表单框架底层原理，又能用 Formily 熟练落地中后台复杂表单和低代码平台。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：用 `@formily` 生态独立开发中后台复杂表单（多步表单、数组表单、联动表单、远程数据表单）；能自定义组件接入 Formily；能基于 Formily 搭建低代码表单设计器。
- **和实际项目的关系**：第二、三模块的省市区联动、订单计算、POMS 采购系统都是真实中后台业务场景；第 20 章的低代码设计器直接对应企业内部的表单搭建平台需求。
- **和其他课程的关系**：需要 **React** 基础（Hooks、HOC、受控组件、JSX）；需要 **TypeScript** 基础（泛型、类型定义）；第一模块的响应式原理（Proxy/依赖收集）与 **Vue3** 的响应式系统高度同构，可对照复习。
- **面试/教学高频场景**：响应式原理 Proxy + 依赖收集（05）、observer 桥接响应式与 React（06）、Formily 精准渲染 vs React 全量渲染（10）、effects 联动编排（11）、路径系统与通配符匹配（12）、x-reactions 三种模式（14）、connect/mapProps/mapReadPretty（17）。

---

## 4. 临时补充

- **第一模块是「演进式教学」**：02~08 每章都是在前一章代码上演进，强烈建议按顺序看。核心演进链：硬编码 useState → Schema 驱动 → OOP 状态模型 → 发布订阅 → Proxy 响应式 → observer HOC → 校验 → 联动。每步都在解决上一步的痛点，跳着看会不理解「为什么要这么做」。
- **06 章课件重复问题**：第 06 章的 `课件.md` 内容与第 05 章完全相同（都是响应式原理），但 05 的课件末尾实际已包含 observer HOC / useObserver 的内容，06 的知识点（observer/useObserver）实际在 05 课件里已经讲到了。复习 observer 时直接看 05 课件的 5.2~5.6 节即可。
- **工程目录差异**：第一模块（02~08）每章是独立的 `MyFormily` 工程；第二、三模块（09~20）基本是同一个 `formily_guide` 工程，用 step1/step2/... 逐步累加组件，看某章内容直接找该章新增的 step 目录。第 19 章 POMS 是独立工程。
- **目录编号有缺口**：无 01 章（可能是课程介绍/前置），14 之后跳到 17（缺 15/16），属正常。
- **第一模块的遗留问题**：第 08 章末尾指出联动 `notify → 全量遍历 fields` 是 O(N²)，真实框架会构建依赖索引图只触发相关字段，这是有兴趣可以继续深化的优化方向。
- **Formily 的心智模型**：Formily 的核心是「**领域模型（Form/Field）+ 响应式（@formily/reactive）+ 协议（JSON Schema）**」，第一模块手写的 MyFormily 几乎是这个心智模型的简化版，理解了第一模块，Formily 的官方文档会非常好读。
