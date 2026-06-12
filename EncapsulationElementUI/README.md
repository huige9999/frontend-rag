# 封装 Element UI 组件库 - 课程导航

> 基于 Vue 3 从零封装一个属于自己的组件库，涵盖组件设计、实现、打包、测试、发布全流程。

## 📋 课程目录

### 基础篇

| 序号 | 课程 | 说明 |
|:---:|------|------|
| 01 | [课程介绍](01.%20课程介绍.md) | 课程整体介绍、学习目标与前置知识 |
| 02 | [搭建项目和准备工作](02.%20搭建项目和准备工作.md) | 使用 Vite + Vue 3 搭建组件库开发工程、路由配置 |
| 03 | [图标的发展](03.%20图标的发展.md) | 图标技术演进：雪碧图 → 字体图标 → SVG 图标 |
| 04 | [封装Icon组件](04.%20封装Icon组件.md) | SVG 图标组件封装、props 定义、图标注册 |

### 组件封装篇

| 序号 | 课程 | 说明 |
|:---:|------|------|
| 05 | [封装Button组件](05.%20封装Button组件.md) | 按钮组件：主题/尺寸/状态切换、loading 效果 |
| 06 | [封装Card组件](06.%20封装Card组件.md) | 卡片组件：插槽设计、summary 属性 vs 默认插槽 |
| 07 | [封装Dialog组件](07.%20封装Dialog组件.md) | 对话框组件：v-model 双向绑定、过渡动画、Teleport |
| 08 | [封装Pager组件](08.%20封装Pager组件.md) | 分页组件：页码计算逻辑、事件通信 |
| 09 | [封装Collapse组件part1](09.%20封装Collapse组件part1.md) | 折叠面板(上)：provide/inject 设计、容器逻辑 |
| 10 | [封装Collapse组件part2](10.%20封装Collapse组件part2.md) | 折叠面板(下)：手风琴模式、高度过渡动画、禁用项 |
| 11 | [封装Tooltip组件](11.%20封装Tooltip组件.md) | 提示组件：@popperjs/core 定位、hover/click 触发、防抖 |
| 12 | [封装Dropdown组件](12.%20封装Dropdown组件.md) | 下拉组件：基于 Tooltip 二次封装、RenderVNode |

### 工程化篇

| 序号 | 课程 | 说明 |
|:---:|------|------|
| 13 | [组件库打包](13.%20组件库打包.md) | ES Module / UMD 双格式打包、Vite 库模式配置 |
| 14 | [本地测试组件库](14.%20本地测试组件库.md) | npm link 软链测试、ESM 和 UMD 两种引入方式 |
| 15 | [开源组件库](15.%20开源组件库.md) | Gitee 仓库创建、SSH 配置、代码推送、LICENSE |
| 16 | [发布组件库](16.%20发布组件库.md) | npm 发布流程、files 白名单、镜像源配置 |
| 17 | [课程总结](17.%20课程总结.md) | 知识点回顾、进阶方向（monorepo/TypeScript/Rollup） |

## 🛠 技术栈

- **Vue 3** + Composition API
- **Vite** 构建 + 库模式打包
- **SCSS** 样式方案 + CSS 变量
- **@popperjs/core** 定位引擎
- **npm** 包管理 + 发布

## 📁 项目结构

```
EncapsulationElementUI/
├── README.md                          # 本导航文件
├── 01. 课程介绍.md
├── 02. 搭建项目和准备工作.md
├── 03. 图标的发展.md
├── 04. 封装Icon组件.md
├── 05. 封装Button组件.md
├── 06. 封装Card组件.md
├── 07. 封装Dialog组件.md
├── 08. 封装Pager组件.md
├── 09. 封装Collapse组件part1.md
├── 10. 封装Collapse组件part2.md
├── 11. 封装Tooltip组件.md
├── 12. 封装Dropdown组件.md
├── 13. 组件库打包.md
├── 14. 本地测试组件库.md
├── 15. 开源组件库.md
├── 16. 发布组件库.md
└── 17. 课程总结.md
```
