# AI 编程训练营 · 知识手册

> 本手册提炼自陈天「AI 编程训练营」开营直播 + 3 场直播答疑(共 4 期)。
> 原始内容为口语直播转录,本手册已纠正语音识别错误(如 Claude Code / Cursor / Gemini / Nushell / SurrealDB / git worktree 等)、去除口语冗余,按主题重构为可通读、可检索的书面知识手册。关键观点保留原话作为引语,并标注来源期次。
>
> 来源标记:[开营] = 第 1 期开营直播;[答疑1] = 直播答疑 1;[答疑2] = 直播答疑 2;[答疑3] = 直播答疑 3。

---

## 目录

- [核心思想](#核心思想)
- [一、Spec-Driven 开发范式](#一spec-driven-开发范式)
- [二、给 Coding Agent 设约束](#二给-coding-agent-设约束)
- [三、完整交付流程与验证体系](#三完整交付流程与验证体系)
- [四、Claude Code 并行工作流(git worktree)](#四claude-code-并行工作流git-worktree)
- [五、Skills 编排与自动化](#五skills-编排与自动化)
- [六、向 AI 学习的方法论](#六向-ai-学习的方法论)
- [七、知识盲区、上下文与依赖处理](#七知识盲区上下文与依赖处理)
- [八、实战排障:识别并打破 Agent 的困境](#八实战排障识别并打破-agent-的困境)
- [九、超定制化与新交互模式](#九超定制化与新交互模式)
- [附录:术语对照与开发环境](#附录术语对照与开发环境)

---

## 核心思想

**一句话总纲**:把 Coding Agent 当成能力很强的新人工程师,用对待团队成员的方式去编排它——提供愿景与方向、给足上下文、设好约束、让它独立完成并自我验证,最后人来做 review。

> [开营] "我们作为一个工程师所做的一切努力和准备,对 Coding Agent 来讲,他也应该做这些事情。唯有他把所有步骤都做完,他写出来的代码的质量和正确性才是有所保障的。"

这带来工程师角色的根本转变:

| 旧时代 | AI 时代 |
|---|---|
| **执行者(Executor)** | **指挥者(Orchestrator)** |
| 亲手写代码 | 给 Agent 分配任务、提供上下文、纠偏 |
| 事无巨细(Micromanagement) | 提供愿景(Vision)、监控状态、替团队 Unblock |
| 关注"怎么写" | 关注"做什么、为什么、如何验证" |

> [开营] "你的角色不是去事无巨细地对团队每个人的工作进行 micromanagement,而是提供一个 vision、一个方向,定期监控检查工作状态,替他们 unblock 事情,最后检查交付结果。"

> [答疑1] "人应该聚焦于问题的发现、定义和探索解决思路——这是工作的内核,而具体的任务是可以被 delegate 的。"

---

## 一、Spec-Driven 开发范式

软件开发正在从「需求 → 写代码」过渡到 **Spec-Driven(规范/文档驱动)** 时代。其核心是把传统大型团队里产品经理、架构师、子系统负责人各司其职的流程,复刻到「人 + Agent」的协作上。

### 1.1 完整流程链

```
idea / 想法
   │  (Gemini deep research + 与 AI 交流)
   ▼
PRD / 产品需求文档
   │  (review:保留符合需求的部分,删除无用部分)
   ▼
设计规范 (Design Spec)  ← 在此注入「约束」
   │  (review:修补技术选型,如 WebSocket → SSE)
   ▼
实施计划 (Implementation Plan)  ← 含测试计划
   │
   ▼
编码 → 构建 → 测量 → 学习 (构建-测量-学习循环)
   │
   ▼
PR (提交给人 review)
```

每一步的产出物都是文档,都由 Coding Agent 协助生成,人负责 review 与纠偏。

### 1.2 关键环节

- **PRD**:用 Gemini deep research 把几句话的想法,充实成参考业界各种方案的详实需求文档。
- **设计规范**:以 PRD 为基石,让 Agent 出设计。**关键在于注入约束**(见第二章)——"任何软件系统都是有约束的",不加约束的"通用最优设计"往往不合口味。
- **实施计划**:把设计文档拆成可分阶段执行的计划,同时定义验证方法。

> [开营] "软件不是天马行空,你的限制和约束越明晰,代码最终交付的成果会越好。"

### 1.3 把 Agent 当人看待

整套流程背后是一个贯穿始终的原则:**凡是人能做的,Agent 也应该做;凡是人做的准备,Agent 也该做。**

> [开营] "总之我们所做的一切,都是如何让 Coding Agent 看起来像一个人。"

这也是为什么资深、架构能力强的工程师,在 Agent 时代反而加速效应更强——他们的工程技能积累能更好地指挥 Agent。

---

## 二、给 Coding Agent 设约束

约束是 Spec-Driven 的灵魂。一个项目需要在多个层次为 Agent 设定边界,类似 `spec-kit` 这类工具的第一步就是设置 **Constitution(章程)**。

### 2.1 约束的三个层次

1. **Constitution(章程)**:项目级的高层规则(High-level Rules)。
2. **工作流程 / 规则(Workflow & Rules)**:必须使用哪些设计模式、最佳实践。
3. **验证方法(Verification)**:如何确认代码正确、达到质量标准。

### 2.2 具体约束示例

讲师本人的约束偏好(可作为模板):

- **Rust 项目**:禁止使用 `Mutex`、`RwLock`;一旦发现需要,改用 **Actor Model** 模式,把数据结构放在单一线程内,通过 actor 对外提供服务。
- **多读少变更的配置数据**:使用 `Arc` + `RwLock`(原文:arcs wrap)处理。
- **通用原则**:必须符合 **SOLID 原则**、**DRY(Don't Repeat Yourself)**;先设计 trait 再实现 trait。
- **实时更新**:倾向用 **HTTP/2 + SSE** 而非更复杂的 WebSocket。
- **序列化命名**:Rust 最终序列化为 JSON 时,需 `serde` + `rename_all = "camel_case"`(原文被误识为 "thirty rename all")。

### 2.3 约束写在哪里

- 写入 `CLAUDE.md` / `AGENTS.md`:如前后端 JSON 命名约定(camelCase vs snake_case)、依赖偏好、文档组织约定。
- 复杂项目的 Constitution、工作流、规则集中沉淀,Agent 在生成代码时遵循。

> [开营] "当你把这些 best practice 都设置好,所有这些东西都可以让 Agent 在生成代码时注意。"

---

## 三、完整交付流程与验证体系

### 3.1 构建与质量门禁

Agent 交付的代码,应在提交前完成全套自动化验证:

- 格式化、代码检查(Lint)
- 编译(Rust `clippy`、Swift 等编译型语言可拦截大部分错误)
- 单元测试(Unit Test)
- 集成测试(Integration Test)
- 端到端测试(E2E Test)
- 模糊测试(Fuzzing)、安全测试、性能测试
- 人工测试

> [开营] "在 PR 值得人去 review 之前,没有合适验证的代码都不是合格的提交。"

### 3.2 测试要"有意义",而非刷指标

人写测试时知道什么有意义,而 Agent 可能为了完成覆盖率指标堆砌大量无意义测试。因此需**明确定义什么样的测试是有意义的**:

- **Unit Test**:定义覆盖率目标,同时定义"有意义"的标准。
- **Integration Test**:涉及数据库时,定义如何使用 test database。
- **E2E Test**(关键一步):让 Agent 设计 fixture data,通过 `curl` / `grpcurl` 依次调用 API,验证完整用户流程。因为很多场景单元/集成测试都过了,但完整 user flow 会暴露更广的问题。

### 3.3 接手老项目的前置工作

迭代老项目新功能前,必须先做和「人接手老项目」一样的前置准备:

1. 让 Agent 先了解项目现状。
2. 撰写当前项目的设计文档、验证文档。
3. 补足 Unit Test 覆盖率,确保已有功能正常工作。
4. 在此基础上再做新功能迭代 / 重构。

> [答疑3] "先要保证已有功能的完整性和测试覆盖率,你才有足够信心在加新功能、做 refactor 时不破坏原有功能。"

---

## 四、Claude Code 并行工作流(git worktree)

> [答疑2] 关于「同时开十个窗口并行解决问题」,业界比较统一的方案是 **git worktree**。

### 4.1 目录约定

- 在当前 repo 下创建 `.trees/` 目录,**并加入 `.gitignore`**。
- 推荐放在当前 repo 下(而非家目录隐藏目录),好处:能更好地共享 `git config`(尤其使用 local `git config` 时)。

### 4.2 完整工作流步骤

```
1. git pull,确保基于最新的 default branch
2. 与 Claude Code 探讨问题,让它寻找 root cause / solution
   ⚠️ 关键:让它「只解释,不要动手」
      —— Claude Code 做事很激进(explain 完往往直接开始改)
3. 满意方案后,让它:
   - 为设计创建 branch
   - 创建相应 git worktree(放在 .trees/ 下)
4. 启动 subagent,让其 think ultra-hard,生成详细设计文档
   放到 .specs/ 下合适目录(由 CLAUDE.md 指导组织)
5. 人工 review 设计文档(接口设计、并发设计、依赖选型等)
6. 启动 subagent,按设计文档分 phase 一步一步实现
   每个 phase 完成即提交,再进下一 phase
7. 所有 phase 完成后,启动 subagent 用 codex skill
   review 该 branch 下所有代码,生成 code review report
8. 查阅 report,合理项找方案修复、提交
9. 手工运行验证文档中的命令(后端用 curl/grpcurl,
   前端用 Playwright MCP,移动端用 simulator)
10. 创建 PR
11. 合并后,告知 Claude 清理 branch 和 worktree
```

### 4.3 并行数量建议

- **建议不超过 3 个并行 worktree**(不是工具限制,而是冲突管理)。
- 原因:多个 feature 在同一 repo 工作且隔离度不高时,会产生大量 **merge conflict**,你还得让 Agent 花时间解决,并行效果大打折扣。
- 类比:就像大团队在同一 repo 上工作,提交速度快就会互相冲突。

### 4.4 注意事项

- 每次创建 worktree 必须从**最新** branch branch out,否则基于老代码会引入大量 merge 工作。
- git conflict 也可交给 Agent 处理,但前提是有**足够的测试覆盖率**来保证 merge 不出错。
- 复杂系统还应让 Agent 提供**手工验证文档**。

### 4.5 关于用量(Max 订阅)

- Claude Code **Max 最高档**订阅:不做自动化很难用完;做了自动化(从 intent 一路走到 PR)才能充分发挥。
- Max 按周 rate limit(限速);并发任务多时容易触发。讲师经验:同时跑几个 feature,常在 4-5 个 repo 工作,会触及周限额。
- 可将整套并行流程封装成一个 **Skill** 让 Agent 自动执行。

---

## 五、Skills 编排与自动化

### 5.1 自动化的三个层次

> [开营] 复杂工作自动化有三种思路:

1. **脚本层**:让 Agent 生成 shell / Python 脚本完成固定任务。
   - 例:每周 B 站视频 → 抓音频 → 转字幕 → 浓缩出若干 title / description / status update 供人工挑选,再用 GPT image 模型生成题图。
2. **Claude Skill 层**:把要做的封装成 Claude Skill,让 Claude Code 直接调用。
   - 例:把访问 DataDog 监控封装成 Skill,再写脚本定期(每 10 分钟)扫描十几个 dashboard,列出潜在风险并总结成文档。
3. **完整开发链**:需求 → 设计 → 计划 → 交付(PR),并可嵌套(复杂 CLI → 封装 Skill → 脚本定期调用)。

### 5.2 Skill 的正确用法:不只是描述性文档

> [答疑2] "如果 Skill 只是一个描述性文档,你还没有发挥它最大的能力。"

进阶做法(以 DataDog 监控为例):

1. **基础**:Skill 描述 DataDog CLI 怎么用。
2. **预处理**:先把公司所有重要 dashboard / monitor / alert 预处理成 reference,下次启动不用重新探索。
3. **固化逻辑**:把需要多条 CLI 命令 + 判断逻辑的探查,固化成 bash script,Skill 里引用该 script。

### 5.3 Prompt 驱动 vs 代码固化:核心权衡

> [答疑2] "需要权衡哪些固化成代码,哪些用 prompt 完成——这是做 AI 工具时非常重要的点。"

| 全 Prompt 驱动 | 全代码实现 |
|---|---|
| 大量 token 消耗 | 模糊性工作难实现 |
| 灵活但昂贵 | 能力被代码死锁 |

**建议**:

- 起步时尽量让 Agent 完成。
- 之后审视:**哪些输入/输出耗费大量 token**,把它们用 script 减少。例如大量文本不必一次性喂给 Agent,可用 script 分批送入,让每轮上下文窗口最小(下一轮对话不会重复加载)。

### 5.4 工作流(Workflow)化与 Checkpoint

面向普通用户的产品(如微信对话入口 + Claude Code headless 执行),建议定义成**工作流**而非聊天:

- 每个阶段(stage)有明确输入/输出。
- 支持断点重启:每个 stage 完成有产出文档作下个 stage 的 input,crash 后可跳过已完成 stage。
- 支持人工介入:如 design spec 阶段设为需人工确认。
- 灵活:工具是 building block,工作流定义驱动执行,换需求只换工作流不改代码。

### 5.5 自动化测试用例生成

> [答疑2] 分可靠度三个层次:

1. **最可靠:半人工 + 半 Agent**——人写测试用例,Agent 实现代码。
2. **中等:有源码自主探索**——Agent 读源码 + Playwright / 模拟器,按代码路径操作、截图,生成测试用例;测试代码需固化(能反复跑、每次 PR 都跑)。
3. **最不可靠:无源码只有产品**——移动端可用 `AutoGLM` / `Phone 9B` 等 APP 自动化专调模型;Web 端用 AI + Playwright 探索。

---

## 六、向 AI 学习的方法论

这一章是手册中**最具个人增量价值**的部分——它讲的不是"用 AI 干活",而是"用 AI 帮自己成长"。

### 6.1 信息获取:Deep Research + NotebookLM 分层过滤

面对海量内容,用工具逐层过滤:

1. **Deep Research 定向**(如 Gemini):非常具体地框定范围,例:"探索 AWS re:Invent 2025 最值得查看的 infra 相关视频"。
2. **NotebookLM 生成摘要**:从几段简短文字生成 slides,快速判断是否值得深入。
3. **重点深入**:最终只对几个到十来个视频做深度探索,但已建立全局 picture。

> [答疑1] "很多时候你想面对海量数据,最大的困境在于如何快速找到想要的东西。现阶段 Agent 是你最好最好的老师。"

### 6.2 把 AI 当苏格拉底,而非维基百科

> [答疑1] "把 AI 当成苏格拉底,而不是维基百科。"

遇到新概念(MCP 协议、Redis 延迟优化等),不要只问"这是什么":

1. **做类比与连接**:让 AI 把新概念与已有概念做类比,看是否有关联、什么更有关联。大脑在产生联系时,记忆与理解会上一个层次。
2. **问优缺点**:任何技术都是权衡,不存在 silver bullet。
3. **问"何时不用"**:
   > [答疑1] "只有当你知道什么时候不用一个技术的时候,你才算真正掌握了这个技术。"
4. **理解 Why**:理解技术为解决什么痛点、做了什么权衡、为什么被创造出来。长此以往,自己思考问题也会从这些角度出发。

### 6.3 主动补盲区

看 Agent 生成的设计文档时,必然会触发知识盲区:

> [开营] "当你看到 Agent 设计里用了你不了解的 database(如 SurrealDB),千万不要盲目 approve,而应让 Gemini 做 deep research 看它有什么特点、与你所知的差异、应用场景、为何被制造、解决什么问题、有哪些同类产品。"

- 单凭一两篇文章不会深入理解,但至少建立概念与场景认知。
- 随代码展开再扎实学习(如 SurrealDB 的 schema 定义、关系定义、数据插入)。
- **不要被动让 Agent 完成所有事**,要跟着它的节奏学习才能跟上(catch up)。

### 6.4 学习资料的消费方式

英文资料不再是障碍:

- 把《Fundamentals of Software Architecture》这类书的 PDF 放进 NotebookLM,以对话方式学其核心、架构特性、设计模式。
- 论文、英文书籍都可交给 LLM / Coding Agent 以中文交互。
- GitHub Trending 项目:让 Gemini deep research 推荐方向,clone 下来让 Claude Code 分析结构、产品要素、架构、子系统划分、模块通信方式。

> [答疑3] "问对问题可能比找到答案更重要,尤其是跟 AI 交互时——一个很好的问题会引发 AI 很好的思考和回答。"

### 6.5 能力的基石:想象力与好奇心

> [开营] "除去硬技能之外,最重要的两个能力基石是**想象力**和**好奇心**。"

- **好奇心**:看到新鲜事物第一反应是"用 Gemini deep research 调研一下"。
- **想象力**:构思未知的未知事物,思考"如果有一天要做这个产品,需要什么技术"——即使没机会亲自做,这种思考本身有价值。

---

## 七、知识盲区、上下文与依赖处理

### 7.1 上下文组织 > 模型智商

> [答疑1] "写代码需要很好的上下文组织和强大的工具调用能力,这两个的重要性大于模型的智商。"

- 头部模型智商大差不差,差距在**上下文组织**与**工具调用**。
- 强模型 + 差上下文/工具,也得不到好结果。
- Claude Code 优秀之处:很好地适配了工具能力,激活模型潜能。

> [答疑1] "当你给 Agent 一个错误的 context 时,可能还不如不给。"

### 7.2 向量索引 vs 人工索引:代码场景的取舍

> [答疑1] 对代码/文档做向量化,要分场景:

- **文档级 markdown**:量级处于可处理范围,**人工索引优于自动向量索引**——因为人类知道逻辑边界在哪,向量数据库不知道。
- **代码向量化意义有限**:代码是线性逻辑,切片会破坏上下文(一个长函数切两半,带 signature 的上半有意义,下半无意义);切片大小受 embedding 模型限制(4K / 8K / 32K),带来信息不连贯和局部性。
- 若要对代码切片,需用 **tree-sitter** 分析代码,把局部性相关内容放进同一切片。
- 更重要:**代码调用关系图(Graph)的索引,远重要于把代码切片做 embedding。**

### 7.3 让 Agent 像人一样探索代码库

> [答疑1] "让 Agent 尽可能靠近人处理工作的过程。人脑也有限,不可能加载所有信息,也没有 embedding。"

人探索代码库的方式:读 README → 看 dependency → 通过命令行层层递进。

对应到 Spec 文件夹:即使有上百上千文件,Claude Code 没法一次性加载也没关系——

- 提供一个 **index / master plan 全局索引**,告诉 Agent 有哪些文档。
- Agent 先读 index,根据当前需求判断该读哪个文档(和人一样)。
- 单个 markdown 文档应保持小而精,而非一个大而全的文档。

### 7.4 大模型知识库更新不及时:四种处理

> [答疑3] 处理 Agent 不熟悉的新依赖/库:

1. **让模型自己探索**:效率低,文档可能不及时、例子有问题。
2. **Clone 源码到本地**,告诉路径让模型探索 example、结构、public API(和人在文档不全时的做法一样)。
3. **放 vendors 目录**:把依赖源码放进当前 repo 的 `vendors/` 目录,整体 `gitignore`,告诉模型依赖在此。
4. **Submodule**:用完再清理。

> [答疑3] **避免**:MCP(如 Context7)会占用大量上下文,常得不偿失;讲师尝试后迅速放弃。

### 7.5 高频依赖 → 生成可复用 Skill

> [答疑3] 对于反复使用的库(如 SurrealDB、Claude Agent SDK):

- 让模型探索一次后,生成一个 **Skill** 沉淀到 Skill 库。
- Skill 加载时只载入 description,可写得很简短,甚至显式指定"使用某 skill"。
- **企业团队价值**:把各项目依赖的使用总结成 Skill,集中分发给整个软件团队,形成统一应用方式。

### 7.6 关于 Agent 与 Skill 的反思

> [答疑3] 业界在反思:是不是需要那么多 Agent?很多 Agent 是不是伪命题?

- 旧思路:每个方向生成一个 Agent(法务 Agent、财务 Agent、编码 Agent……),底层复用相同逻辑,只是技能不同。
- 新思路:同一个 Agent,加载不同**技能树(Skill)**即可切换身份(像人写代码调编码技能、炒菜调炒菜技能)。
- Skill 会成为知识整理与落地的关键能力。

---

## 八、实战排障:识别并打破 Agent 的困境

### 8.1 「隧道视角」陷阱

> [答疑3] "Agent 非常聪明,但有时又非常傻。当你让它做一件事,它会陷入**隧道视角**——好像被关进隧道,所有思考都局限于如何让当前 case 通过。"

### 8.2 识别 Agent 卡住的信号

当发现 Agent 以下行为,说明它已陷入困境:

- 持续做非常局部的优化,反复来回改某些特定文件。
- 不断添加代码、删除代码。
- 甚至重命名文件后重写。

### 8.3 处理方法

1. **果断 stop Agent**。
2. **rollback** 之前所有修改。
3. 让它 **review 整个 design、review 整个 plan**。
4. 换一个角度问问题,引导它做**全局思考**而非局部修补。
5. 若知道问题方向,让它做该方向的 research。

### 8.4 「我不知道我不知道」怎么办

> [答疑3] 例:语音识别 APP 识别率低,却不知如何让 AI 改。

- **把困惑直接告诉 deep research**:"识别率很低,即便用好模型,业界 best practice 是什么?推荐什么开源模型?如何处理输入?"
- 让 deep research 挖出采样率、噪音等前置要求(而非只让 Claude Code 做局部修改)。
- **核心:避免让 Claude Code 只做局部修改**,引导它跳出来全局思考。

---

## 九、超定制化与新交互模式

### 9.1 从「软件及服务」到「超定制化」

> [答疑1] 讲师有更激进的观点:传统 SaaS 因团队能承载功能上限有限,让用户适应软件(抽取公共需求、标准化、拒绝定制需求)。未来会走向**软件适应用户**的超定制化。

- 以前:面向消费者的软件若没十万百万级用户,不值得做。
- 未来:一个工程师能做的事变多,可能为几百人的需求专门做软件;一家公司在一个 foundation 上支持成百上千个面向不同群组的系统。
- SaaS 也会提供深度定制功能(取决于 AI 发展程度)。

### 9.2 交互模式变革

> [答疑1] "以前一个专业团队负责的业务,现在可能一两个人加一群 Agent 就能完成。"

- 人聚焦:发现问题、定义问题、探索解决思路(工作内核)。
- Agent 负责:具体执行(delegatable)。
- 范式转换(Paradigm Shift)需要时间:Agent 能力、方法论储备、客户信任都要积累,届时水到渠成。

### 9.3 处理复杂项目的前后端

> [答疑1] 前后端分离项目如何让 Agent 同时参考前后端代码:

1. **聚合工作区**:不管 monorepo 还是 polyrepo,把多个 repo 放在 Claude Code 运行目录下(VS Code 等 IDE 有 workspace 能力)。
2. **核心是契约(Contract)**:和人做前后端分离一样,定义好 API spec——清晰表达前后端交互的输入输出,给好例子。
3. 命名约定写进 `CLAUDE.md`(如前端 camelCase、后端 snake_case,Rust 用 `serde` `rename_all`)。
4. **原则:对人有意义的事,对 Agent 一定也有意义。**

---

## 附录:术语对照与开发环境

### A.1 语音识别错误纠正表

转录文本含大量识别错误,本手册已按下表纠正:

| 原文(误) | 正确 | 说明 |
|---|---|---|
| clock code / 克劳扣 / coco / 克拉扣 / 靠扣 / CC | **Claude Code** | Anthropic 的 coding agent |
| 科色 | **Cursor** | AI IDE |
| 专门来 / 专门来的 | **Gemini** | Google 大模型 |
| now shell / nos / nau sal | **Nushell** | 现代 shell |
| sara db / sarah db | **SurrealDB** | 多模型数据库 |
| git HUB tree / gate work tree | **git worktree** | Git 并行工作流 |
| thirty rename all | **serde `rename_all`** | Rust 序列化重命名 |
| cloud scales / cloud scale | **Claude Skills** | 已捐给开源组织 |
| re limit | **rate limit** | 限速 |
| 10x / 20x / max | **Claude Code Max 订阅档** | 按周限速 |
| tree sitter | **tree-sitter** | 代码解析工具 |
| arks wrap | **Arc + RwLock** | Rust 并发原语 |
| speck it | **spec-kit** | Spec 驱动工具 |
| open AI / gbt 5 | **OpenAI / GPT-5** | |
| item two | **iTerm2** | macOS 终端 |
| crits点儿IO | **crates.io** | Rust 包仓库 |
| rust dock | **rust doc** | Rust 文档 |
| 凹凸glm phone 9b | **AutoGLM / Phone 9B** | APP 自动化模型 |

### A.2 讲师开发环境

> [答疑2]

- **Shell**:iTerm2 + **Nushell**(用了很久;早期与 Claude Code 不兼容,现在 Agent 都直接用 bash 命令,问题不大)。
- 主要工作:打开一个 Claude Code 通过对话完成,已很少手敲具体命令。
- Nushell 优点:输出与命令管道做得不错。

### A.3 其他实用信息

- **MCP 配置**(如 Playwright MCP):访问 GitHub repo 即可,对 Claude Code 一句话添加,对 Cursor 一段配置或一键脚本——并非 rocket science。
- **语音录入软件**:Handy。
- **学习推荐书**:《Fundamentals of Software Architecture》。
- **看开源项目**:GitHub Trending,让 deep research 按方向推荐。

---

> **结语**:这份训练营的内核不是教某个工具的用法,而是教一种**思维方式**——在 AI 时代,工程师从执行者变为指挥者,用 Spec-Driven 的方法、充分的约束、可验证的流程去编排 Agent;同时保持想象力与好奇心,把 AI 当成苏格拉底,在与它的协作中持续成长。
