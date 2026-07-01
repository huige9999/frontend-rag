# BPMN 工作流（BPMN + bpmn.js + Flowable）

> 课程来源：`bpmn-workflow`。本课覆盖完整链路：**BPMN 标准认知 → bpmn.js 可视化建模库（底层原理 + 基础使用 + 全套自定义组件）→ Flowable 工作流引擎落地**。

## 1. 目录结构整理

```
bpmn-workflow
├── 02. 认识BPMN                          （理论：BPMN 2.0 标准 / 元素体系 / XML 结构 / 工作流引擎与 bpmn.js 定位）
├── 04. bpmn.js基础使用                    （实战：Vue3 接入 Viewer / Modeler / 属性面板 / 导入导出 / 汉化 / 事件监听）
├── 05. bpmn.js常见功能处理                （实战：常见功能处理，进阶用法，详见代码）
├── 06. 了解bpmn.js的底层实现              （理论：diagram-js + bpmn-moddle 分层架构 / DI 模块系统 / 核心服务 / 数据流）
├── 07. 简单引入自定义Palette处理          （自定义：把默认左侧工具栏 Palette 拷出来改）
├── 08. 自定义Palette模块实现              （自定义：把 CustomPalette 写成独立 additionalModule）
├── 09. 自定义Modeler替换                  （自定义：继承 Modeler 封装 CustomModeler，预注册自定义模块）
├── 10. 自定义renderer                     （自定义：继承 BaseRenderer 改节点绘制，tiny-svg 画图标/矩形）
├── 11. 自定义ContextPad                   （自定义：右键悬浮工具栏，增删改 + 把节点信息传出去）
├── 12. 使用仓库保存节点数据                （自定义：用 Pinia 仓库保存/同步当前选中节点数据）
├── 13. 自定义封装处理-1                   （实战代码包：无课件，进阶封装落地）
├── 14. 自定义封装处理-2                   （实战代码包：无课件，进阶封装落地）
├── 15. bpmn properties                    （属性：businessObject / modeling.updateProperties / camunda-bpmn-moddle / moddleExtension / 扩展与完全自定义属性面板）
├── 16. moddleExtension描述文件            （属性：moddleExtension JSON 描述文件结构 types/superClass/extends/isAttr）
├── 17. 自定义PropertiesPanelExtension扩展 （属性：扩展现成属性面板，自定义 Provider / 分组 / 属性项）
├── 18. 完全自定义PropertiesPanel          （属性：用 Vue 自己写属性面板，改颜色 / 换 event·task 节点类型）
├── 19. flowable工作流引擎处理             （落地：Docker 跑 Flowable，IDM 用户组权限，建模器画报销流程并发布体验）
└── 20. 通过flowable相关API实现工作流处理   （落地：Flowable REST API 速查——流程定义/实例/任务/表单/认领/提交）
```

> 注 1：本课章节编号从 02 起（**无 01、无 03**），保留原编号不重排。
>
> 注 2：07/08/09 三章共用同一份「自定义Palette」课件，11/12 共用同一份「自定义ContextPad」课件，15/16/17/18 共用同一份「bpmn properties」课件——即多章共享一份大课件，**实际每章的差异化内容落在各自的「代码」目录**里（如 16 重点在 moddleExtension 描述文件、17 重点在扩展属性面板、18 重点在完全自定义 Vue 面板）。13、14 两章只有代码包、无课件。

---

## 2. 章节逻辑说明

这门课是一条**「认识 BPMN → 吃透 bpmn.js 底层 → 基础集成 → 自定义全套建模组件 → 接 Flowable 引擎真正跑起来」**的主线，本质是教你怎么从零做出一个**属于自己的 BPMN 流程设计器，并对接真实工作流引擎**。整体可拆成三大段：理论地基（02、06）、bpmn.js 使用与定制（04~18）、引擎落地（19、20）。

### 先讲什么：BPMN 标准认知（02 认识BPMN）

开篇是纯理论，建立「BPMN 到底是什么」的认知，不写代码。涵盖：

- **什么是 BPMN**：OMG 标准、BPMN 2.0 历史、定位（一套业务流程的图形化 + 可执行规范）。
- **核心元素体系**：Flow Objects（Events/Activities/Gateways 事件·活动·网关）、Data、Connecting Objects（Sequence/Message Flow、Association）、Swimlanes（Pool/Lane 泳道）、Artifacts。
- **BPMN 2.0 与 XML**：`<definitions>` → `<process>`/`<collaboration>` + `bpmndi:BPMNDiagram`（DI/DC 坐标描述）的 XML 结构，讲清「语义」与「图形坐标」是分开存的。
- **工作流引擎**：jBPM / Activiti / Camunda / Flowable 都能解析运行 BPMN 文件。
- **bpmn.js 定位**：Viewer（只看）/ NavigatedViewer（看 + 平移缩放）/ Modeler（可编辑）三种形态，但**只做可视化与编辑，不负责执行**（执行交给引擎）。

**为什么先讲**：BPMN 是后面所有内容的「共同语言」。不懂元素体系和 XML 结构，后面无论是建模、自定义渲染还是对接 Flowable，都无从下手。先建立「图形 ↔ XML ↔ 元素类型」的对应关系，是整门课的地基。

### 再讲什么：bpmn.js 基础使用（04、05）

理论打底后，立刻进入 Vue3 + TS 把 bpmn.js 跑起来。涵盖：

- **示例 bpmn 文件**：最小 `startEvent → userTask → endEvent` 的 XML 模板。
- **Viewer**：挂载 `BpmnViewer`、`canvas.zoom("fit-viewport")`、`importXML`。
- **Modeler**：`BpmnModeler` 可编辑形态。
  - 加入 `properties-panel`：`bpmn-js-properties-panel` + `@bpmn-io/properties-panel` 作为 `additionalModules`。
  - 新建 / 导入 / 导出：`saveXML`/`saveSVG`、文件导入处理。
  - 汉化模块：自定义 `customTranslate` + `zh.ts` 字典（大小写不敏感查找）。
- **事件监听**：监听 modeler 的 `shape.added`/`shape.move.end`/`shape.removed`，以及通过 `eventBus` 监听 `element.click`/`element.changed`，并用 `elementRegistry.get(id)` 解析节点（过滤掉 `bpmn:Process` 根元素）。
- TS 配置坑：`tsconfig.app.json` 的 `skipLibCheck`/`noImplicitAny` 处理。

05「常见功能处理」是在此基础上的进阶常见用法，差异内容主要在代码里。

**为什么这么安排**：先让大家「先把图渲染出来、能编辑、能存取」，建立成就感与可操作环境，再谈底层和定制。地基（02）→ 跑通（04/05）是符合学习曲线的顺序。

### 然后讲什么：bpmn.js 底层实现（06 了解bpmn.js的底层实现）

在「会用」之后，回头讲「它怎么做到的」，是承上启下的关键理论章。涵盖：

- **三层架构**：`diagram-js`（图表交互 / 建模）+ `bpmn-moddle`（BPMN 元模型）+ `bpmn-js`（整合）。
- **diagram-js 模块系统**：基于 `didi` 的依赖注入，`__depends__`/`__init__`/`$inject`（含 `MyLoggingPlugin` 示例）。
- **核心服务**：`Canvas`、`EventBus`、`ElementFactory`、`ElementRegistry`、`GraphicsFactory`。
- **数据模型**：shape/connection 的 `parent`/`children`/`incoming`/`outgoing`。
- **辅助服务（工具箱）**：`CommandStack`（撤销/重做）、`ContextPad`、`Overlays`、`Modeling`、`Palette`。
- **bpmn-moddle**：元模型、`fromXML`/`toXML`、校验、`businessObject`。
- **数据流 + 交互流**：import → render → edit → export 的完整生命周期，以及 `BpmnRules`/`BpmnUpdater`。

**为什么放在这里**：这一章是后面整个「自定义系列（07~18）」的钥匙——自定义 Palette/Renderer/ContextPad/Properties 本质上都是「注册一个 additionalModule，往 DI 容器里塞自定义的服务」。不懂模块系统、EventBus、ElementRegistry、Modeling 这些，自定义根本写不动。所以它必须卡在「基础使用」和「自定义」之间。

### 接着讲什么：自定义全套建模组件（07 ~ 18）

这是全课最长、最核心的实战段，围绕「把默认设计器改造成自己产品要的样子」逐个组件突破，顺序是 **Palette → Modeler 封装 → Renderer → ContextPad → 数据仓库 → Properties**：

| 章节 | 自定义对象 | 核心做法 |
|------|-----------|---------|
| 07/08/09 | **Palette（左侧工具栏）** | 从 diagram-js 的 `Palette.js` + bpmn-js 的 `PaletteProvider.js` 拷出改写；`CustomPalette` 用 `$inject`（`bpmnFactory, create, elementFactory, palette, translate`）+ `palette.registerProvider(this)`；`getPaletteEntries` 返回 `group/className/title/action.dragstart·click`；`elementFactory.createShape({type:"bpmn:Task"})` + `create.start`；自定义 CSS 图标；最后用 `inherits-browser` 继承 Modeler 封装 `CustomModeler`，预注册模块并加连线工具 `globalConnect.toggle`。 |
| 10 | **Renderer（节点绘制）** | 继承 `BaseRenderer`，高优先级覆盖 `canRender`/`drawShape`/`getShapePath`，用 `tiny-svg`（`svgCreate`/`svgAppend`）画自定义图标或手绘矩形；也给出完全自定义 `CustomModeler` 的写法。 |
| 11/12 | **ContextPad（右键悬浮工具栏）** | `contextPad.registerProvider(this)` + `getContextPadEntries`；加编辑/删除动作（`modeling.removeElements`）；用 Pinia + element-plus 把节点信息传到弹窗（`useBpmnStore`、组件外 `setActivePinia` 激活 Pinia、`el-dialog`）。12「使用仓库保存节点数据」即聚焦 Pinia 同步选中节点。 |
| 13/14 | **进阶封装处理** | 仅有代码包、无课件，是把前面的自定义能力进一步工程化封装。 |
| 15/16/17/18 | **Properties（属性面板 + moddle 扩展）** | 15 总览：`businessObject` 与图形元素关系、`modeling.updateProperties()` + `elementRegistry` 改属性、装 `camunda-bpmn-moddle` + `BpmnPropertiesPanelModule`；16 聚焦 **moddleExtension 描述文件**（JSON 的 `name/uri/prefix/xml/types`、`superClass/extends/isAttr/isBody`、`SelfDescriptor.json`）；17 聚焦 **扩展现成属性面板**（Magic/spell 示例，Preact + `htm` 写 Provider/分组/属性项 + `magic.json` moddle 扩展）；18 聚焦 **完全自定义 Vue 属性面板**（`PropertiesView.vue`、`modeling.setColor` 改色、`bpmnReplace.replaceElement` 换 event/task 节点类型）。 |

**为什么是这个顺序**：遵循「UI 入口 → 节点外观 → 节点交互 → 节点数据」的改造路径——先改「从哪创建节点」（Palette），再把 Modeler 封装好，接着改「节点长什么样」（Renderer），再改「点节点弹出什么」（ContextPad），最后处理「节点的属性怎么存怎么改」（Properties + moddle）。Properties 放最后，是因为它最依赖 `businessObject`/moddle 元模型，难度也最高。

### 最后讲什么：接 Flowable 工作流引擎落地（19、20）

前面 18 章做出的是「能画图、能编辑、能存」的设计器，但 BPMN 图要真正流转起来必须靠**引擎**。最后两章把闭环补上：

- **19 flowable 工作流引擎处理**：用 Docker 跑 Flowable all-in-one 镜像（默认 admin/test 登录）；在 IDM 里管用户/组/权限；在 Flowable Modeler 里画一条「报销审批」流程（表单、排他网关 + `${money > 1000}` 流转条件、审批结果按钮）；发布应用；在 Flowable Task 里走完「发起 → 认领 → 审批/驳回（驳回回到起点）」全生命周期。UI/截图驱动，无代码。
- **20 通过 flowable 相关 API 实现工作流处理**：Flowable REST API（`process-api`、`form-api`）速查——流程定义列表、流程实例列表、启动流程、删除/终止流程实例、获取流程 XML、获取执行中的活跃节点、任务列表、实例关联表单、表单模板数据、提交任务表单、任务认领、简单 outcome 提交（基于 `form_..._outcome` key 的审批/驳回）。每个接口给 HTTP 方法 + 请求体 + JSON 响应示例。

**为什么放最后**：19、20 完成了从「画图工具」到「可运行工作流系统」的跨越——先用 Flowable 自带 UI 感受「一个流程怎么跑」（19），再用 REST API 把流程能力集成进自己的前端/后端（20）。这也是整门课的收口与价值落点。

### 主线一句话总结

**认知 BPMN（02）→ 跑通 bpmn.js（04/05）→ 吃透底层（06）→ 自定义全套组件 Palette/Renderer/ContextPad/Properties（07~18）→ 接 Flowable 引擎真正落地（19/20）**，目标是独立做出一个「能画、能改、能存、能跑」的 BPMN 工作流设计器并对接真实引擎。

---

## 3. 使用场景等元信息沉淀

- **学完能用在哪**：自研审批/工单/请假/报销类流程设计器；低代码/无代码平台的流程编排模块；把流程图可视化集成进企业后台；对接 Flowable/Activiti/Camunda 类引擎做端到端工作流。
- **和实际项目的关系**：Palette/Renderer/ContextPad/Properties 的自定义是「产品化一个流程设计器」的必经之路；moddleExtension 决定了你能不能存「业务自定义字段」（如审批人、抄送人、金额上限）；Flowable REST API 是把前端设计器和后端引擎打通的标准接口。
- **和其他课程的关系**：偏「可视化编辑器 + 工作流」方向。与《Vue3》《Vue2》强相关（Modeler 组件化、属性面板用 Vue 写、Pinia 管状态）；与《node》/《Egg》/《Koa》衔接（Flowable REST 通常由后端代理或 Node BFF 转发）；与《工程化》相关（bpmn-js 的 TS 配置坑、additionalModule 工程化封装）。
- **面试/教学高频场景**：bpmn.js 的三层架构（diagram-js / bpmn-moddle / bpmn-js）与 DI 模块系统；`additionalModule` 自定义的套路（Palette/Renderer/ContextPad）；`businessObject` 与图形元素、`moddleExtension` 扩展业务字段；BPMN 图形 ↔ XML 的对应；工作流引擎（Flowable/Activiti/Camunda）的定位与选型。

---

## 4. 临时补充

- **课件共享情况要记住**：07=08=09（Palette）、11=12（ContextPad）、15=16=17=18（properties）的课件 md 是同一份大文档，多章共用；真正区分各章的内容在「代码」目录里。复习某一章时，若课件对不上章名，直接去对应代码目录看差异。
- 13、14 两章（自定义封装处理-1/2）只有代码、无课件，复习时直接看 `代码` 目录源码。
- 章节 01、03 本仓库不存在，不是漏看，保留编号断号。
- 06「底层实现」是全课的「第二地基」（仅次于 02），如果时间有限，**02 + 06 + 04 + 19/20** 是最小可用复习路径：知道 BPMN、知道 bpmn.js 怎么运作、能把图画出来、能接引擎跑起来。
- bpmn.js 版本相关：课件用 `bpmn-js@18.6.3` + `diagram-js@15.3.0`，API（`importXML`/`saveXML`/`eventBus`/`additionalModules`）在新版基本稳定，但自定义模块的具体文件路径（如 `diagram-js/lib/features/palette/Palette`）随版本可能变动，升级时需核对源码路径。
- Flowable 部分课件里的请求示例把账号密码直接拼进 URL（`http://admin:test@...`），仅适合本地调试，生产环境必须走后端代理 + 正式鉴权。
