# framework extractor 分区1：第 1-6 章

> 来源：《AI Agent 手册》第 1-6 章（文件 14-57，含架构篇导读）。id 前缀 f1-。不做筛选，边界模糊（偏原则/清单）者已标注，交阶段 1.5 去重。

```yaml
- id: f1-01
  title: Agent = Model + Harness 责任公式
  type: framework
  source_chapter: 第 1 章 1.3（1.1/第 2 章反复引用）
  source_quote: |
    "Agent = Model + Harness……Model 提供理解、推理、生成与决策能力，但模型本身不能独立承担上下文准备、任务状态维护、工具执行、权限控制、故障恢复和结果验证。"
  summary: |
    把 Agent 理解为两个责任对等的部分：模型是可替换的概率性认知核心；Harness 是组织、约束并承载执行的工程系统。
    用它判断"什么问题归模型、什么问题归工程"：可靠性不能寄托于模型本身，模型能力不足常是 Harness 问题的误判。
    落地时 Harness 再拆为 Agent Loop、Context/State/Memory、Tool/Skill/Protocol、Runtime/Sandbox、Observability/Evaluation、Security/Governance。
  tags: [mental-model, architecture, responsibility-boundary]

- id: f1-02
  title: 应用形态选型决策（Chat/RAG→Workflow→Copilot→Single-Agent→Long-Horizon/Multi-Agent）
  type: framework
  source_chapter: 第 1 章 1.2.1
  source_quote: |
    "这些形态的根本区别，不在于界面是否采用对话方式，而在于任务循环由谁掌握、执行路径在什么阶段确定、系统能够在多大程度上影响外部环境，以及任务是否需要跨会话持续运行或由多个 Agent 分工协作。"
  summary: |
    形态不是线性替代链。用四个维度判断：任务循环由谁掌握、执行路径何时确定、对外部环境的影响程度、是否需要跨会话持续或多人协作。
    每种形态有典型适用条件：Workflow 适合路径可穷举、错误代价高；Agent 适合路径难预知、环境可反馈、结果可验证。
    企业主流形态是 Workflow 与 Agent 结合的 Hybrid：确定性流程固定边界，Agent 处理不确定段落。
  tags: [decision, form-factor, selection]
  inputs: 业务任务结构、确定性需求、风险等级、持续时间、协作结构
  outputs: 形态决策（Workflow/Agent/Hybrid/长程/多 Agent）+理由
  steps: 判断任务循环归属→判断路径可预知度→评估环境影响与验证手段→按"收益覆盖新增复杂度"决定是否扩展
  missing_conditions: 未给出量化权衡（如复杂度成本的具体度量）

- id: f1-03
  title: 最低充分架构决策（自主性与任务风险匹配）
  type: framework
  source_chapter: 第 1 章 1.4.1（1.2.2/1.5/2.1.3 反复出现）
  source_quote: |
    "企业应当结合任务价值、风险、运行规模和工程成本，为每类任务选择成本与风险可接受的最低充分架构。这是贯穿本章的决策原则。"
  summary: |
    反"越智能越好"：自主性授予必须与权限、可撤销范围、可验证性匹配。低风险可逆操作可给高自主性；高风险不可逆操作需确定性检查与人工审批。
    只有当更复杂形态能解决当前架构无法以可接受风险成本交付的问题时，升级才有必要。
    判断输入：任务价值、风险、运行规模、工程成本四要素。
  tags: [decision, architecture, minimal-sufficiency]

- id: f1-04
  title: Agentic Application 六特征判定
  type: framework
  source_chapter: 第 1 章 1.3.2
  source_quote: |
    "第一，以目标和任务结果为中心……第六，通过可观测与评估持续改进。这六项特征是判断应用架构形态的依据，而不是必须同时启用的功能清单。"
  summary: |
    判断一个应用是否进入 Agentic 阶段的六个特征：(1)以目标和任务结果为中心；(2)运行时决定部分执行路径；(3)能对外部环境产生作用；(4)维持跨步骤跨请求的任务状态；(5)自主行为受确定性机制约束；(6)通过可观测与评估持续改进。
    用作形态判定依据，不是必须全启用的功能清单。
  tags: [checklist, assessment, definition]

- id: f1-05
  title: 企业成熟度四层模型与升级判断
  type: framework
  source_chapter: 第 1 章 1.4.1
  source_quote: |
    "Agentic Application 的成熟度不能由产品名称或技术标签决定，而应从任务循环、执行跨度、环境影响和生产治理四个维度进行综合判断。"
  summary: |
    L1 辅助生成（人闭环）→L2 受控自动化（Workflow+局部 Agent）→L3 Agentic Execution（Agent 持有任务循环）→L4 规模运营与持续优化（多任务多租户统一治理）。
    每级有典型局限与升级门槛；成熟度不由产品名称决定，形态（哪种 Agent）与成熟度（运行治理完备度）是两个独立判断。
    升级条件：当前架构无法以可接受风险成本交付任务，且新增能力有明确价值。
  tags: [maturity-model, assessment, upgrade-decision]
  inputs: 应用的任务循环、执行跨度、环境影响、生产治理现状
  outputs: 当前成熟度定位 + 升级门槛清单
  steps: 按四维定位层级→识别该级典型局限→核对升级门槛→按需升级
  missing_conditions: 无

- id: f1-06
  title: 企业级五架构问题序列
  type: framework
  source_chapter: 第 1 章 1.4.2
  source_quote: |
    "架构设计是构建、运行、治理和调优的前置决策，后四者则形成持续迭代的工程闭环。"
  summary: |
    架构设计先于构建时依次回答五个衔接问题：如何设计架构（形态/自主度/边界）→如何构建（能力组合/固化边界）→如何运行（调度/持久化/隔离/恢复）→如何治理（身份/审批/观测）→如何调优（质量判断/归因/回归灰度回滚）。
    第一个是前置决策，后四个构成持续迭代闭环。与第 2 章五阶段生命周期闭环对应。
  tags: [decision-sequence, lifecycle, architecture]

- id: f1-07
  title: 传统软件 vs Agentic 应用管理假设对比（操作系统视角）
  type: framework
  source_chapter: 第 1 章 1.4.3
  source_quote: |
    "管理职责的名字没有变——仍然是调度、隔离、资源、授权、恢复、审计——但每一项的假设都被改写了。"
  summary: |
    六维对比思维模型：执行单元（进程 vs Run，路径编写时定 vs 运行时定）；状态（随进程存活 vs 外置持久可恢复）；能力接口（启动时确定 vs 运行时动态接入）；授权（一次性授予 vs 每次高影响操作按策略判定且可撤销）；故障恢复（进程退出即回收 vs 任务级检查点+幂等+补偿）；审计（系统调用粒度 vs 意图-工具-反馈-结果关联链路）。
    用于识别规模化运行多 Agent 时缺失的公共承载层（Agentic OS 问题域）。
  tags: [mental-model, comparison, os-perspective]

- id: f1-08
  title: Coding Agent 六大工程问题识别框架
  type: framework
  source_chapter: 第 1 章 1.1.2
  source_quote: |
    "Coding Agent 的价值不只在于软件研发本身是一个高价值场景，更在于它集中暴露了 Agentic Application 进入生产必须处理的工程问题。"
  summary: |
    用六个问题检验任何 Agentic 场景的工程完备度：(1)信息超出上下文窗口→检索/筛选/摘要/外置；(2)跨步骤跨调用→计划/进度/待办维护；(3)工具真实修改环境→权限/隔离/不可逆审批；(4)中间尝试失败→观测/重试/检查点/恢复；(5)结果不能模型自评→测试/构建/Diff/独立评估器；(6)人无法逐 Token 监督→关键节点 Steering 与 HITL。
    这些能力要求可从专用场景迁移到通用架构。
  tags: [checklist, problem-identification, engineering]

- id: f1-09
  title: Long-Horizon 与 Multi-Agent 扩展判断（两维独立扩展）
  type: framework
  source_chapter: 第 1 章 1.2.3/1.3.3
  source_quote: |
    "Long-Horizon Agent 与 Multi-Agent 不是同一维度。前者解决任务如何跨时间持续，后者解决多个执行主体如何协作。"
  summary: |
    Single-Agent 是基础形态；沿时间跨度扩展为 Long-Horizon（核心是跨时间保持目标、状态、责任和结果一致性，不是"运行更久"），沿协作结构扩展为 Multi-Agent（核心是角色/权限/上下文隔离与相互校验成为可设计结构）。
    两者可独立采用或组合，不代表更高智能或成熟度。引入条件：单个 Agent 受到时间跨度、上下文容量、职责冲突、权限隔离或并行效率限制，且收益覆盖新增复杂度。
  tags: [decision, scaling, multi-agent, long-horizon]

- id: f1-10
  title: 三视图架构理解法（组件/平台/生命周期）
  type: framework
  source_chapter: 第 2 章 2.1.1
  source_quote: |
    "三个视图不能互相推导。组件齐全并不意味着责任清晰，责任清晰也不意味着变更可控。因此，在讨论某个能力时，需要明确当前处于哪个视图。"
  summary: |
    用三个正交视图审视同一系统：组件视图（系统由什么组成，避免能力遗漏）、平台视图（在哪里运行、由谁负责，避免责任混乱）、生命周期视图（如何从决策走向运行并改进，避免把演示当可运营系统）。
    讨论任何能力前先确认所处视图；同一 State Store 在三视图中有三种身份（持久化能力/数据资源面/调优证据源）。
  tags: [mental-model, architecture, multi-view]

- id: f1-11
  title: 五个能力责任域架构组织
  type: framework
  source_chapter: 第 2 章 2.1.2
  source_quote: |
    "业务与应用层定义系统为什么存在……Agent 构建与编排层……生产运行层……治理与控制层回答哪些版本和动作被允许发生。调优层从运行中获得 Trace……形成候选变更与验证证据。"
  summary: |
    把企业 Agent 系统组织为五个责任域：业务与应用层（目标与验收）、Agent 构建与编排层（任务循环组合）、生产运行层（调度/状态/隔离/恢复）、治理与控制层（策略/版本/审计）、调优层（证据→候选变更）。
    Security 横跨五层；调优不直接改生产，变更须回流构建层经门禁再入运行。"层"是责任组织不是调用栈或建设顺序。
  tags: [architecture, responsibility-domains]

- id: f1-12
  title: 企业级架构五设计原则（边界模糊：偏原则清单）
  type: framework
  source_chapter: 第 2 章 2.1.3
  source_quote: |
    "凡是被授予的自主性，都必须有与之匹配的边界、证据和验证手段；凡是暂时不需要的能力，可以不建设，但需要明确在什么条件下必须补齐。"
  summary: |
    五项稳定设计原则作为架构决策框架：(1)Model 与 Harness 解耦（放在一起评估但实现不强绑定）；(2)编排层与 Runtime 解耦（接口是任务与状态）；(3)状态外置、执行实例可替换；(4)自主性与权限和可验证性匹配（最小权限/短期凭证/出站控制/沙箱/审批）；(5)可观测、安全和评估是运行前提而非上线补丁。
    原则不要求做大系统，只要求对等能力。
  tags: [principles, architecture, boundary-note]

- id: f1-13
  title: Model 五维评估与路由决策
  type: framework
  source_chapter: 第 2 章 2.2.1
  source_quote: |
    "前三项决定 Agent 能否完成任务，后两项决定它能否在企业内规模化运行。"
  summary: |
    模型选择是持续决策而非一次性选型，五维评估：(1)领域与任务基础能力；(2)长上下文指令遵循/信息筛选/抗干扰；(3)工具参数生成与多步调用稳定性；(4)延迟/价格/上下文成本/并发/区域；(5)数据保留/隐私/合规/私有化。
    复杂应用可按任务类型、预算、数据边界、负载做 Model Router 路由，但每条路由/降级策略必须与完整 Harness 一起回归评估——防"协议兼容却结果错误"的降级失败。
  tags: [decision, model-selection, routing]
  inputs: 任务类型、预算、数据边界、负载、合规要求
  outputs: 模型选择/路由策略 + 回归评估要求
  steps: 五维打分→确定单模型或路由拓扑→路由与降级策略纳入 Harness 回归
  missing_conditions: 无

- id: f1-14
  title: 五类信息对象区分（Context/State/Memory/Knowledge/Skill）
  type: framework
  source_chapter: 第 2 章 2.2.3
  source_quote: |
    "把两者放进同一个向量库，往往会同时失去这两类治理能力。"
  summary: |
    不要用一个上下文包含一切，按"主要问题+生命周期+一致性要求"区分五类：Context（本轮看见什么，不作为事实来源）、State（任务发生过什么、从哪恢复，强一致持久）、Memory（过去经验，写入门槛与遗忘）、Knowledge（组织外部知识，权限过滤与时效）、Skill（任务方法，版本化资产）。
    Memory 与 Knowledge 分列的核心原因是写入方式与治理责任不同。
  tags: [mental-model, information-architecture, classification]

- id: f1-15
  title: 受控行动六环节
  type: framework
  source_chapter: 第 2 章 2.2.4
  source_quote: |
    "一次受控行动至少需要经过六个环节：能力选择、身份与权限判定、参数校验、隔离环境执行、结果校验，以及状态与审计记录。"
  summary: |
    把"模型建议的动作"变成"系统执行的事实"必须经过六环节：能力选择→身份与权限判定→参数校验→隔离环境执行→结果校验→状态与审计记录。
    三个防混淆前提：能力被发现≠已授权；参数符合 Schema≠意图符合目标；Sandbox 中运行成功≠结果可影响生产。任何环节缺失都会让模型建议直接变成系统事实。
  tags: [procedure, action-control, security]
  inputs: 模型行动意图、身份、策略、环境
  outputs: 带审计记录的受控行动结果
  steps: 能力选择→权限判定→参数校验→隔离执行→结果校验→状态与审计
  missing_conditions: 无

- id: f1-16
  title: 三平面责任划分（执行面/数据与资源面/控制面）
  type: framework
  source_chapter: 第 2 章 2.3.2
  source_quote: |
    "本白皮书因此将企业 Agent Platform 划分为执行面、数据与资源面，以及控制面。"
  summary: |
    平台视图的核心划分：执行面（真实运行 Loop 与工具，可弹性可失败，不持有唯一真实状态）、数据与资源面（可恢复事实与能力，须定义一致性/所有权/保留期/隐私/租户边界）、控制面（版本/身份/策略/配额，集中定义、版本化、可审计、判定点可分布）。
    用于回答"能力在哪里运行、由谁托管"。
  tags: [architecture, platform-view, responsibility]

- id: f1-17
  title: 策略集中定义、判定点分布式执行
  type: framework
  source_chapter: 第 2 章 2.3.2
  source_quote: |
    "一种更稳健的模式是策略集中定义，判定点分布式执行……这样既能保持统一治理，又能避免控制面故障直接中断所有 Agent 任务。"
  summary: |
    控制面不做每次调用的强同步依赖：控制面管理策略/版本/发布，Harness Middleware、Gateway、Runtime、Sandbox 在请求路径上按已下发策略判定，审计事件异步回传。
    代价是策略传播延迟，高风险策略（凭证吊销、租户封禁）需辅以强制刷新、短期凭证或集中校验兜底。
  tags: [mechanism, governance, control-plane]

- id: f1-18
  title: AI 网关三层治理语义（LLM/MCP/Agent Gateway）
  type: framework
  source_chapter: 第 2 章 2.3.4
  source_quote: |
    "在具体实现中，三者可以是同一 Gateway 的不同能力，但治理对象和粒度必须区分，否则模型成本、工具权限和任务预算会在同一处策略中互相覆盖。"
  summary: |
    按治理对象粒度分三层：LLM Gateway 以模型调用为粒度（协议适配/路由/限流/降级/容灾/Token 成本）；MCP Gateway 以工具调用为粒度（Server Registry/凭证托管/工具级 ACL/审计）；Agent Gateway 以任务为粒度（Agent 路由/会话连续性/租户配额/预算）。
    实现可合并，但语义与粒度必须区分。
  tags: [architecture, gateway, governance-granularity]

- id: f1-19
  title: 部署拓扑四形态选型
  type: framework
  source_chapter: 第 2 章 2.3.5
  source_quote: |
    "参考架构不要求企业从第一天就建设一个大而全的 Agent Platform。表 2-5 列出四种常见的部署形态。"
  summary: |
    四种部署形态按"适用场景/优势/代价"选择：(1)应用内嵌 SDK 直连（原型低风险）；(2)托管 Harness 与 Runtime（快速进生产）；(3)托管控制面+私有执行面（管理共享但数据留在域内）；(4)全自托管/混合云（高合规大规模，成本最高）。
  tags: [decision, deployment, topology]
  inputs: 数据边界要求、合规等级、规模、平台团队投入
  outputs: 部署形态选择 + 代价认知
  steps: 判断合规与数据边界→判断规模与团队→对照四形态表选择
  missing_conditions: 无

- id: f1-20
  title: 差异化资产 vs 共享平台能力判断
  type: framework
  source_chapter: 第 2 章 2.3.5
  source_quote: |
    "平台提供安全且可运营的默认路径，应用保留对模型、Harness 编排和业务能力的组合选择权。"
  summary: |
    选型时先判断哪些能力是企业差异化资产（业务特有 Prompt/Skill/Tool/评估标准/人机协作流程），哪些是横切平台能力（模型接入/任务托管/沙箱/状态存储/身份/观测/评估基建/安全策略）。
    两个失败方向：平台封装一切→退化为最低共同需求、业务绕开自建；平台只给计算无策略→每应用重复解决治理问题。
  tags: [decision, platform-strategy, make-or-buy]

- id: f1-21
  title: Agent Release 六要素绑定（最小可复现发布单元）
  type: framework
  source_chapter: 第 2 章 2.4.1
  source_quote: |
    "生命周期的最小发布单元不应只是一份应用代码，而应是一个可复现的 Agent Release。"
  summary: |
    Agent 行为取决于组合而非仅代码，发布单元须绑定六类要素：(1)模型及版本/参数/推理预算/路由策略；(2)Harness 编排代码/配置/循环控制/Middleware/终止条件；(3)Prompt/Context Policy/Memory Policy/Skill 与 Knowledge 版本；(4)Tool/MCP/Agent 能力清单/权限策略/凭证范围；(5)Runtime/Sandbox/资源/网络/数据保留配置；(6)评估数据集/Evaluator/基线/准入阈值/已知风险。
    只有全部可追踪，才能复现失败并证明变更带来改进。
  tags: [release-management, versioning, reproducibility]

- id: f1-22
  title: 五阶段生命周期闭环（架构设计→构建→运行→治理→调优）
  type: framework
  source_chapter: 第 2 章 2.4.2
  source_quote: |
    "这不是一条只向前的瀑布流程，而是一个由生产事实驱动新版本、并在必要时回到架构决策的循环。"
  summary: |
    五阶段各有核心问题/输入/产物/准入机制：架构设计（形态与边界决策+架构评审）；构建（可复现 Release+离线评估与发布门禁）；运行（可靠隔离执行+状态机/沙箱/恢复）；治理（看见约束审批追责+最小权限与行为监测）；调优（复现问题生成改进+回归对比实验灰度回滚）。
    架构设计单列为第一阶段（锁定后续成本区间）；治理和调优有指回架构设计的回溯路径——风险来自形态或权限模型时重新设计架构而非叠补丁。
  tags: [lifecycle, process, closed-loop]
  inputs: 业务目标、任务结构、风险等级、生产 Trace 与反馈
  outputs: 形态决策、Approved Release、运行事实、治理动作、候选变更
  steps: 架构设计→构建→运行→治理→调优→（回流构建或架构设计）
  missing_conditions: 无

- id: f1-23
  title: 治理落位自检标准
  type: framework
  source_chapter: 第 2 章 2.4.3
  source_quote: |
    "如果某项治理要求只能在上线后通过流程和人工补齐，而不能在架构和构建阶段落到策略、接口或数据结构上，那么它在规模化之后很可能会失效。"
  summary: |
    检验治理要求是否真正落位：必须在架构/构建阶段落到策略、接口或数据结构上；只能上线后靠流程人工补齐的治理要求，规模化后大概率失效。
    治理既是持续运营活动也是贯穿全生命周期的架构约束（各阶段形态：设计定权限模型→构建实现审批点→运行执行策略→治理检测→调优验证）。
  tags: [self-check, governance, testability]

- id: f1-24
  title: 从生产证据到受控变更回流机制
  type: framework
  source_chapter: 第 2 章 2.4.4
  source_quote: |
    "任何由模型、反馈或轨迹生成的 Prompt、Memory、Skill 或 Harness 补丁，都应被视为候选变更，需要经过可复现评估、安全检查、版本化、灰度和可回滚发布之后才能进入生产。"
  summary: |
    调优≠让 Agent 在生产自由修改自己。模型/反馈/轨迹产生的一切补丁都是候选变更，回流构建阶段、过准入门禁后再入生产；在线评估只是观测通道不是发布通道。
    运行必须产出结构化 Trace/状态/成本事实，否则调优退化为人工调参；观测、评估与发布三者构成同一条链路。
  tags: [process, change-management, release-gate]
  inputs: 生产 Trace、失败案例、用户反馈
  outputs: 经过门禁的新 Agent 版本
  steps: 收集证据→生成候选变更→可复现评估+安全检查→版本化灰度发布→回滚预案
  missing_conditions: 无

- id: f1-25
  title: Harness 三辨析与五层责任分层
  type: framework
  source_chapter: 第 3 章 3.1.1
  source_quote: |
    "Harness 因而要把概率性的模型判断嵌入确定性的系统边界。"
  summary: |
    三个否定式辨析定位 Harness：不是更长的 System Prompt（只是输入之一）；不是某 Agent Framework 的同义词（描述系统层而非产品形态）；不是 Runtime 或 Sandbox（决定推进 vs 承载 vs 限制）。
    五层责任表：Model（能理解推理到什么程度）/Harness 编排（如何工作）/Runtime（如何持续运行）/Sandbox（在哪行动影响多大）/Agent Platform（如何规模化交付治理）。逻辑边界不一定对应独立部署单元，但架构设计必须保留以判断故障归属、数据位置、迁移成本。
  tags: [mental-model, layered-responsibility, definition]

- id: f1-26
  title: Harness 八构建对象检查表
  type: framework
  source_chapter: 第 3 章 3.1.2（与 2.2.2 表 2-2 呼应）
  source_quote: |
    "一个最小 Agent 可以只实现其中一部分，但进入企业生产环境后，这些问题都必须有明确责任人。"
  summary: |
    构建 Harness = 让八个工程对象在同一任务生命周期协同：Agent Contract（为谁工作/目标/允许禁止）、Execution（Loop/状态机/预算/Verifier）、Context & State（每轮看见什么/事实存哪）、Capability（Tool/MCP/Skill/Subagent）、Environment（在哪运行）、Control（身份/拒绝/审批）、Interaction（进度/干预/恢复）、Quality（完成证明/版本比较）。
    可归纳为执行与编排、上下文与状态、行动与反馈三能力域（第 4-6 章展开）。选择构建路径本质是决定各对象由谁实现。
  tags: [checklist, build-objects, harness]

- id: f1-27
  title: 任务契约先行流程（Agent Contract 先于框架选择）
  type: framework
  source_chapter: 第 3 章 3.1.2
  source_quote: |
    "构建 Agent 不能从选择框架或打开工具开始，而应先固定任务契约（Agent Contract）。"
  summary: |
    构建顺序：先固定任务契约再选实现。契约回答：Agent 为谁工作、接受什么输入、交付什么结果、允许影响哪些系统、哪些动作必须拒绝或审批、什么证据证明完成。
    同一目标（修复高危漏洞）按契约不同可以是"只出报告""隔离环境修改+测试"或"创建合并请求"；除非契约明确授予发布权限并规定审批条件，Agent 不应把修复完成解释为已发布。
  tags: [process, contract-first, task-definition]
  inputs: 业务目标、风险边界、验收证据要求
  outputs: Agent Contract（角色指令+任务输入输出+成功标准）
  steps: 定义目标与干系人→定义输入输出→定义允许/禁止动作→定义完成证据
  missing_conditions: 无

- id: f1-28
  title: 四类构建入口选型框架
  type: framework
  source_chapter: 第 3 章 3.1.3
  source_quote: |
    "它们最显著的差异，不是模型能力，也不是应用形态，而是 Harness 的通用行为由谁实现，Runtime 与 Sandbox 由谁提供，以及企业应用需要补齐哪些控制和验收责任。"
  summary: |
    四类入口：(1)高代码 Framework 自主构建（控制 Loop/Context/Planning/验证，自担执行基础与安全）；(2)产品化 Harness/SDK（复用工作区理解与执行，自担多租户/Task/隔离/验收）；(3)基于模型构建 Managed Agent（托管 Harness+Session+隔离执行，自担身份映射/审批/数据边界/验收）；(4)云产品快速构建（预置资源组合，自担业务 Task/权限边界/评估）。
    不是成熟度阶梯、不必互斥，可组合。选择判据：任务效果是否依赖修改 Loop/Context、数据与环境能否托管、团队是否愿维护恢复与 Sandbox、交付对象形态。
  tags: [decision, build-path, selection]
  inputs: 任务结构、定制深度、数据边界、团队能力、交付方式
  outputs: 入口选择+企业责任清单
  steps: 按四判据逐项判断→对照入口对比表→可组合多入口
  missing_conditions: 无

- id: f1-29
  title: 从任务成功标准反推 Harness 能力组合
  type: framework
  source_chapter: 第 3 章 3.2（3.2.1）
  source_quote: |
    "接下来不应一次性打开所有能力，而应从任务成功标准反推需要的 Harness。"
  summary: |
    Framework 路径的能力组合法：先建立最小边界（模型+Workspace+用户/Session 身份），再从任务成功标准反推能力——需要读取/修改/测试→文件与执行；计划未经确认不能改码→Plan Mode+Permission；分析评审可并行→Subagent；任务跨多次调用→外置状态+可恢复 Workspace。
    分层原则：稳定确定性步骤沉淀为 Tool/脚本/策略；需理解权衡的留在 Loop；影响外部世界的统一过权限与 Sandbox。构建交付物含可测试 Harness 代码、Contract、状态 Schema、Context Policy、Tool/Skill 清单、Environment Contract、Permission Policy、Verifier、事件模型、回归用例。
  tags: [process, capability-composition, framework]
  inputs: 任务成功标准、任务步骤需求
  outputs: 分层后的 Harness 能力组合 + 十项构建交付物
  steps: 定最小边界→从成功标准反推能力→按稳定性分层（Tool/Loop/权限）→形成交付物
  missing_conditions: 无

- id: f1-30
  title: SDK 审批回调与预授权集成模式
  type: framework
  source_chapter: 第 3 章 3.3.2
  source_quote: |
    "tools 限定可用工具集合，allowedTools 仅预授权读取和检索工具；需要确认的文件修改或命令调用，由 canUseTool 交给应用审批。"
  summary: |
    把产品化 Harness 嵌入业务应用的受控集成模式：tools 限定能力全集、allowedTools 预授权低风险操作、canUseTool 回调把需确认动作交给宿主应用审批（拒绝/取消/超时均终止该操作）。
    配套约束：cwd 指向受控 Workspace、不注入生产凭证、用身份与网络策略阻断生产通路；result 只作运行结果，业务 Outcome 须依变更文件/测试/扫描/业务状态另行验收。
  tags: [process, sdk-integration, approval]
  inputs: 目标 Prompt、工具集合、预授权范围、审批界面
  outputs: 受控执行流 + 运行结果与业务 Outcome 分离的验收
  steps: 设置 cwd 与工具集→配置预授权→实现审批回调→消费事件流→独立验收 Outcome
  missing_conditions: 无

- id: f1-31
  title: Worker 失效后的恢复决策机制
  type: framework
  source_chapter: 第 3 章 3.3.2
  source_quote: |
    "Worker 失效后，任务服务先确认原行动和工作区状态，再决定 Resume、Retry 或转人工，不能因为 Session 能被读取就直接重放最后一次工具调用。"
  summary: |
    排障式恢复路径：Worker 失效→先核对幂等键、外部系统状态、已生成 Artifact→再决定 Resume/Retry/转人工。
    共享 Session Store 的前提条件：租户与项目键隔离、同键追加顺序、幂等写入、并发控制；Session Transcript ≠ 完整企业任务存储（不含认证状态/配置/Checkpoint/保留策略）。
  tags: [troubleshooting, recovery, distributed]

- id: f1-32
  title: 托管任务四对象与五步构建流程
  type: framework
  source_chapter: 第 3 章 3.4.1/3.4.2
  source_quote: |
    "构建流程可以归纳为五步：准备访问身份，创建 Environment，定义 Agent，绑定二者创建 Session，最后接通事件通道并发送 user.message。"
  summary: |
    Managed Agent 用四对象组织：Agent（可复用定义：模型/Prompt/工具/Skill）、Environment（容器运行环境：依赖/资源/凭证）、Session（一次任务实例）、Event（输入与执行事件）。
    五步构建：准备访问身份→创建 Environment→定义 Agent→绑定创建 Session→接通事件通道并提交任务。应用侧须保存业务 task_id 与 session_id 映射，关联 Artifact/审批/Outcome。
  tags: [process, managed-agent, session]
  inputs: 模型与行为定义、容器环境配置、业务任务
  outputs: 可委派的托管 Session + 事件流
  steps: 准备身份→创建 Environment→定义 Agent→创建 Session→接通事件并发送任务
  missing_conditions: 无

- id: f1-33
  title: SSE 事件流消费与断线续接机制
  type: framework
  source_chapter: 第 3 章 3.4.2（6.5.5 补充）
  source_quote: |
    "应用应在事件处理成功后保存事件 ID，断线时通过 Last-Event-ID 续接，并按事件 ID 去重；已有或遗漏的事件通过 List Events 分页读取。"
  summary: |
    托管事件消费机制：先确认 SSE 建连再提交任务（新连接只收建连后事件）；事件处理成功后保存事件 ID；断线用 Last-Event-ID 续接；按 ID 幂等去重；遗漏事件用 List Events 分页补齐。
    6.5.5 补充：idle 不等于完成，须读 stop_reason.type=requires_action 与 stop_reason.event_ids 处理待响应动作；工具确认用 user.tool_confirmation 关联 tool_use_id 回传。
  tags: [process, event-stream, resumption]
  inputs: SSE 事件流、Session ID
  outputs: 完整且幂等的事件消费 + 待响应动作处理
  steps: 建连确认→提交任务→保存事件 ID→断线续接→去重补齐→处理 stop_reason
  missing_conditions: 无

- id: f1-34
  title: 责任边界写成机器可执行契约（Managed Agent 集成思维）
  type: framework
  source_chapter: 第 3 章 3.4.3
  source_quote: |
    "Managed Agents 的开发重点是把责任边界写成机器可执行契约：Agent 能看见什么工具，Environment 能访问什么资源，凭证以谁的身份发放，哪些事件需要人工参与，什么 Outcome 才能使企业 Task 完成。"
  summary: |
    托管越多越要把边界写成机器可执行契约的五个问题：能看见什么工具、环境能访问什么资源、凭证以谁的身份发放、哪些事件需人工参与、什么 Outcome 使 Task 完成。
    平台能推进 Harness 与产生 Event，但不能替企业定义业务正确性——发布权、验收权始终在企业。
  tags: [mental-model, contract-first, managed-agent]

- id: f1-35
  title: 云产品资源组合反推流程
  type: framework
  source_chapter: 第 3 章 3.5.2/3.5.3
  source_quote: |
    "企业在云产品中构建 Agent 时，应先从任务契约反推资源组合，而不是先把所有可用模型、知识和工具都加入 Agent。"
  summary: |
    云产品构建法：从任务契约反推资源组合（Agent 定义与版本、Model/Knowledge/Memory、Tool/MCP/Skill、Credential/Identity、Runtime/Sandbox/Channel、Event/Trace/Evaluation 六组对象及企业须确定项）。
    发布状态≠业务完成：须经版本化、评估、准入后进业务入口；建业务 Task 与平台执行对象映射；会改外部系统的 Agent 还要落实审批/幂等/超时/补偿/人工接管为确定性机制。
  tags: [process, cloud-product, resource-composition]
  inputs: 任务契约、平台能力目录
  outputs: 资源组合 + 映射与回滚方案
  steps: 契约反推资源→配置对象与责任→版本化评估准入→建 Task 映射→落实变更控制机制
  missing_conditions: 无

- id: f1-36
  title: 多源 Agent 统一管理三条件
  type: framework
  source_chapter: 第 3 章 3.6.2
  source_quote: |
    "远程 Agent 不能只返回自然语言结论，还应提供可验收的 Artifact、事件或环境事实，否则平台无法把它纳入统一质量闭环。"
  summary: |
    Agent Platform 纳管多源 Agent（原生/高代码/SDK/托管/SaaS）的三个必要条件：(1)版本与归属可追溯（知道实际模型、Harness 配置、能力、环境版本）；(2)Event/Artifact/Trace 关联 Task、Identity 与 Tenant（支持跨系统审计与成本归因）；(3)远程 Agent 提供可验收证据而非自然语言结论。
    平台统一的是公共对象与质量事实，不抹平 Harness 实现差异。
  tags: [assessment-criteria, platform, multi-source]

- id: f1-37
  title: Agent Loop 五阶段状态机
  type: framework
  source_chapter: 第 4 章 4.1.1
  source_quote: |
    "允许模型根据当前目标和环境反馈，重复执行'判断—行动—观察—再判断'，直到任务被验证完成、进入等待、失败或取消。"
  summary: |
    最小完整 Loop 五阶段：Prepare（读权威状态、定本轮目标、请求 Context）→Model（判断行动/委派/询问/等待/申请完成）→Act（交 Action Plane 完成参数/身份/策略/审批/执行）→Observe（标准化结果并更新任务事实）→Verify（用环境事实或规则验收，不接受"我已经完成"）。
    附加迁移：Verify 未通过回 Prepare 产生新缺口；等待输入/审批/事件进 Waiting；不可恢复错误或预算耗尽进 Failed。核心 Loop 稳定，能力经扩展点加入。
  tags: [process, agent-loop, state-machine]
  inputs: 权威任务状态、环境反馈、预算
  outputs: 结构化 Observation、任务事实更新、终态
  steps: Prepare→Model→Act→Observe→（Verify→Completed | 继续 | Waiting | Failed）
  missing_conditions: 无

- id: f1-38
  title: 权威状态驱动任务（消息历史≠任务状态）
  type: framework
  source_chapter: 第 4 章 4.1.2
  source_quote: |
    "消息历史记录了模型和用户曾经交换的内容，却不应成为任务状态的唯一来源。"
  summary: |
    企业 Harness 须维护可机读权威状态：目标、当前阶段、Plan/Todo、已确认事实、阻塞项、子任务、Artifact、剩余预算、等待原因、完成依据。
    模型看到的是由该状态生成的当前任务视图，而非从历史消息猜测进度。模型输出"已完成"只能进 VERIFYING，验收器补齐证据才能 COMPLETED。
  tags: [mental-model, state-management, single-source-of-truth]

- id: f1-39
  title: 任务十状态模型
  type: framework
  source_chapter: 第 4 章 4.1.2
  source_quote: |
    "WAITING 不是失败，PAUSED 也不是结束。只有把这些状态显式化，上层 Runtime 才能在等待期间释放计算资源并准确恢复。"
  summary: |
    任务状态机十态：CREATED/RUNNING/WAITING_INPUT/WAITING_APPROVAL/WAITING_EVENT/PAUSED/VERIFYING/COMPLETED/FAILED/CANCELLED，每态定义语义与允许的下一步。
    显式化的价值：Runtime 能在等待期释放资源并准确恢复；交互界面能说明 Agent 在等什么；观域能区分执行慢/审批慢/工具慢。
  tags: [state-machine, task-management]

- id: f1-40
  title: 预算与外部终止边界
  type: framework
  source_chapter: 第 4 章 4.1.2
  source_quote: |
    "预算耗尽时，则应产生明确终态与未完成清单，而不是悄然截断。"
  summary: |
    Loop 必须有外部终止边界：步骤数、总时长、Token 与费用、工具调用次数、子任务并发数、高风险动作次数都进预算。
    接近阈值时 Harness 可要求模型收敛范围、停止新委派、优先完成可交付部分或请求用户选择；耗尽时产生明确终态与未完成清单。
  tags: [budget-control, termination, safety]

- id: f1-41
  title: 计划控制三模式选型与修订规则
  type: framework
  source_chapter: 第 4 章 4.2.1
  source_quote: |
    "Planning 的价值不是展示模型隐藏的思考过程，而是把任务结构外部化为 Harness 和用户都能读取、修改和验证的控制对象。"
  summary: |
    按任务复杂度三模式：轻量 Todo（目标明确步骤少反馈快）、Plan Mode（只读探索→计划→确认后执行，适合影响面大需审阅）、Planner-Executor（维护阶段与依赖，适合长任务多依赖可并行）。
    修订规则：每次重规划须说明触发事实并保留已完成项，不能改写目标掩盖失败；Todo 只记录改变可交付状态的事项，保持唯一当前项或明确并行分组。有效阶段描述必须回答"输出是什么、证据在哪里、谁来确认"。
  tags: [selection, planning, todo]
  inputs: 任务复杂度、影响面、反馈速度
  outputs: 外部化计划对象（Plan/Todo）
  steps: 选模式→初始计划→触发事实驱动的显式重规划→保留已完成项
  missing_conditions: 无

- id: f1-42
  title: 阶段门禁机制
  type: framework
  source_chapter: 第 4 章 4.2.2
  source_quote: |
    "长任务不应直到最后才验证。"
  summary: |
    把长任务划分为带门禁的阶段（如漏洞修复五阶段：影响分析→修复规划→变更实施→独立验证→发布准备），每阶段定义主要产物与进入下一阶段的门禁条件（如"影响范围可追溯、版本事实已核验""计划获批准、写权限被放开"）。
    价值：降低错误方向上的继续投入；为 Context 压缩、人工接管、跨窗口续行提供稳定边界。
  tags: [process, stage-gate, verification]
  inputs: 阶段产物、门禁条件
  outputs: 通过/不通过决策与阶段边界
  steps: 划分阶段→定义每阶段产物→定义门禁→在门禁处验收
  missing_conditions: 无

- id: f1-43
  title: Plan Mode 探索-计划-确认-执行分离
  type: framework
  source_chapter: 第 4 章 4.2.3
  source_quote: |
    "只有权限模式、工具白名单、持久状态与 HITL 共同生效，系统才真正具备'计划获批前不可写'的约束。"
  summary: |
    四段分离流程：只读探索（只开放只读工具+计划工具 plan_enter/plan_write/plan_exit/todo_write）→写入计划（持久化到 plans/PLAN.md，跨调用恢复）→人工确认（plan_exit 触发 HITL）→进入可修改执行阶段。
    核心论点：Prompt 可以要求"先规划再修改"，但只有权限模式+工具白名单+持久状态+HITL 共同生效才构成真约束。
  tags: [process, plan-mode, hitl]
  inputs: 任务目标、工作区、审批人
  outputs: 获批计划 + 受控执行阶段切换
  steps: 只读探索→计划写入→人工确认→解锁写权限执行
  missing_conditions: 无

- id: f1-44
  title: Subagent 委派价值判断
  type: framework
  source_chapter: 第 4 章 4.3.1
  source_quote: |
    "只有隔离、专业化或并行收益超过这些成本时，Subagent 才有价值。"
  summary: |
    委派解决三个具体问题：上下文隔离（子任务只加载相关内容）、能力隔离（不同模型/指令/Skill/权限）、并行执行（互不依赖的工作缩短墙钟时间）。
    反向条件：任务很短、步骤高度依赖或共享对象频繁变化时，委派只增加通信与合并成本。判断式：隔离/专业化/并行收益 > 通信合并成本才委派。
  tags: [decision, delegation, cost-benefit]

- id: f1-45
  title: 委派契约七要素
  type: framework
  source_chapter: 第 4 章 4.3.2
  source_quote: |
    "主 Agent 负责全局目标、计划、预算、依赖和最终结果，不应把'任务完成'的责任一并交出去。"
  summary: |
    每个子任务携带可机读契约，七要素：目标与边界（交付什么、范围内外）、已知上下文（已确认事实、不可自行改变的决定）、能力与权限（可用模型/Skill/Tool/环境）、预算（时间/步骤/Token/费用/并发）、输出与证据（结构、来源附带）、失败语义（重试/部分结果/升级/终止）、验收条件（父 Agent 采用标准）。
    子代理默认不继承父任务全部上下文与权限；父有权委派≠子自动获得同等授权。
  tags: [contract, delegation, subagent]

- id: f1-46
  title: Delegation 与 Handoff 区分
  type: framework
  source_chapter: 第 4 章 4.3.2
  source_quote: |
    "Delegation 是父任务保留责任，将有边界的子任务委派出去……Handoff 则是任务控制权发生转移，接收者成为当前责任人。"
  summary: |
    两种任务流转机制的选择思维模型：Delegation（父保留全局责任，子任务有边界，结果返回后父整合验收）vs Handoff（控制权与责任转移，接收者获得目标、状态和恢复位置）。
    二者都不能只靠一条自然语言消息实现，至少要有任务关系、状态与责任变更记录。
  tags: [mental-model, task-transfer, responsibility]

- id: f1-47
  title: 子任务失败策略与结果合并验收
  type: framework
  source_chapter: 第 4 章 4.3.4
  source_quote: |
    "Subagent 的产物不是主 Agent 可以直接复述的'答案'，而是新的 Observation。"
  summary: |
    失败策略四类按子任务性质选择：FAIL_FAST（关键分析失败）、BEST_EFFORT（非关键探索）、RETRY_OR_REASSIGN（瞬时故障）、ESCALATE（需业务决定）。
    合并规则：校验输入版本与证据时间，避免采用基于旧代码/旧业务状态的结论；子产物须经 Schema 校验、版本检查、父任务验收后才能进入权威状态。多执行者同改一工作区须隔离分支/对象级锁/补丁合并，不靠"大家小心"。
  tags: [failure-policy, result-merging, verification]

- id: f1-48
  title: 异步任务与长程续行机制
  type: framework
  source_chapter: 第 4 章 4.4.1
  source_quote: |
    "任务进入等待或后台运行时，Runtime 可以释放当前计算资源；条件满足后，从权威状态恢复，而不是依赖原进程仍然存在。"
  summary: |
    任务身份与当前连接分离：提交任务得稳定 task_id + event cursor，可断开；等待时释放资源；条件满足从权威状态恢复。
    统一契约字段：任务 ID/父任务 ID/状态/创建者执行者/输入与 Artifact 引用/事件序号/超时取消幂等/结果位置/错误分类/Continuation。
    暂停前：停止新行动、处理可中断操作、保存最新状态。恢复时六项重检查：目标、外部条件、工具是否实际执行、权限是否有效、工作区是否变化、剩余预算。取消沿父子传播，已发生外部副作用须保留事实并必要时补偿。
  tags: [process, async-task, continuation]
  inputs: 长任务、外部事件、审批
  outputs: 可恢复的异步任务 + Continuation
  steps: 提交得 task_id→进 WAITING 释放资源→事件触发恢复→重建 Context→续行
  missing_conditions: 无

- id: f1-49
  title: 稳定内核 + 可插拔 Middleware 架构模式
  type: framework
  source_chapter: 第 4 章 4.5.1
  source_quote: |
    "更稳健的结构是'稳定内核 + 可插拔能力'：核心 Loop 只定义阶段与状态迁移；Middleware、Hook 或 Ability 在明确的生命周期点读取 Runtime Context，返回放行、修改、短路或追加行为。"
  summary: |
    防 Loop 腐化的演进结构：核心只管阶段与状态迁移；扩展在明确生命周期点（Task 创建前后/Prepare 前后/Model 调用前后/Action 前后/State 变化前后/Verify 前后）返回放行/修改/短路/追加。
    扩展自身需定义顺序、读写范围、冲突规则、失败语义、可观测性；扩展返回结构化 Decision 或 Patch 由核心统一提交，不任意改共享对象。
  tags: [architecture-pattern, extensibility, middleware]

- id: f1-50
  title: 执行内核四类契约测试
  type: framework
  source_chapter: 第 4 章 4.5.2
  source_quote: |
    "Harness 测试不能只看最终回答。"
  summary: |
    确定性部分至少四类测试：(1)状态迁移测试（每状态只接受合法事件，暂停/取消/失败正确传播）；(2)扩展顺序测试（Middleware 确定时机运行，冲突与短路稳定）；(3)恢复测试（任意安全点中断后重建且不重复副作用，如"补丁已写但测试未返回"时中断，恢复后先查原测试任务）；(4)预算与边界测试（达阈值后按设计收敛）。
    模型输出用固定样本/录制回放/模拟器替代；端到端效果归 Evaluation。
  tags: [testing, contract-testing, determinism]

- id: f1-51
  title: 失败类型→恢复策略映射
  type: framework
  source_chapter: 第 4 章 4.6.1
  source_quote: |
    "'Agent 失败'不是一个可执行的诊断。"
  summary: |
    排障映射：瞬时模型/网络错误→有界退避；参数错误→修正；环境缺失→重建；权限拒绝→等待或终止；重复探索→重新规划；业务条件不满足→明确缺口。盲目重试只增加成本和风险。
    降级路径（切换模型/工具/环境）也要被记录，因为能力、权限和结果质量可能已变化。
  tags: [troubleshooting, failure-classification, recovery]

- id: f1-52
  title: 副作用行动的幂等重试决策
  type: framework
  source_chapter: 第 4 章 4.6.1
  source_quote: |
    "有副作用的行动在重试前必须先回答'上一次究竟有没有发生'。"
  summary: |
    对发布、通知、写数据库等操作：超时可能只是响应丢失。决策顺序：Harness 生成幂等键→记录请求与结果→优先查询状态→无法证明未执行时不直接重复。
  tags: [decision, idempotency, retry]
  inputs: 行动类型、幂等键、上次执行记录
  outputs: 重试/查询/放弃决策
  steps: 生成幂等键→超时后先查状态→证明未执行才重试
  missing_conditions: 无

- id: f1-53
  title: 无进展检测机制
  type: framework
  source_chapter: 第 4 章 4.6.1
  source_quote: |
    "无进展检测比单纯的最大步数更早发现问题。"
  summary: |
    信号：连续调用相同工具且参数高度相似、反复得到同一错误、Plan 长时间不变、工作区无新增事实、模型在少数行动间循环。
    检测后渐进处置：先要求模型根据结构化证据重新规划→缩小任务→切换能力→创建独立评审→转人工。
  tags: [troubleshooting, loop-detection, escalation]

- id: f1-54
  title: 完成验证五级（验证强度与风险匹配）
  type: framework
  source_chapter: 第 4 章 4.6.2/4.6.3
  source_quote: |
    "模型只能提出完成申请，Harness 才能提交完成状态。"
  summary: |
    五级验证按风险递增选择：结构验证（Schema/必填/格式）→环境验证（查真实系统/Diff/执行结果）→确定性验证（测试/规则/静态检查/业务校验）→独立模型验证（独立 Context 查质量遗漏）→人工验收（签署批准）。
    Verifier 返回结构化缺口、失败证据和可修复性；验证失败是新 Observation（继续修复/重新规划/转交/失败）。完成结果是一组目标相关事实（evidence 引用 + remaining_actions），不是一句"已完成"——目标只到"可审批变更"时不得把"尚未发布"误判为未完成。
  tags: [process, completion-verification, evidence]
  inputs: 任务契约、成功标准、风险等级
  outputs: COMPLETED + 证据集，或新缺口 Observation
  steps: 模型申请完成→按风险选验证层级→运行验收器→通过提交/失败转 Observation
  missing_conditions: 无

- id: f1-55
  title: System Context 动态编译分层
  type: framework
  source_chapter: 第 5 章 5.1
  source_quote: |
    "其角色更接近一次'编译'：多个来源按优先级合并，冲突被处理，超出预算的内容被压缩或移除，最终得到本轮可执行的模型视图。"
  summary: |
    System Prompt 不是仓库里的长字符串，而是按 Agent 版本/任务阶段/用户身份/工作区规则/预算/工具/Skill 动态编译。System Context 六层：Platform Policy→Agent Contract→Tenant/Project Rule→Runtime Reminder→Selected Skill→Tool Descriptors。
    分层解决责任问题：平台策略不被项目文档覆盖、用户最新要求不能突破安全边界、Memory 不能替代当前事实、工具返回内容不能升为系统指令。
    Task Context 七层：User Goal/Steering、Plan/Todo、Recent Interaction、Compacted History、Retrieved Memory/Knowledge、Workspace References。
  tags: [context-engineering, layered-structure, dynamic-compilation]

- id: f1-56
  title: Context Builder 八步确定性管线
  type: framework
  source_chapter: 第 5 章 5.1
  source_quote: |
    "管线应先做身份和权限过滤，再做相关性排序，不能为了排序方便先把跨租户内容交给检索器或模型。"
  summary: |
    每次模型调用前的确定性管线：读取 Task State（目标/阶段/预算）→解析身份与作用域→收集候选 Context（规则/历史/状态/资产）→权限与可信度过滤→相关性排序与去重→分配 Token 预算→压缩截断与引用化→按层级编译模型输入→生成 Context Manifest。
    外部内容保留来源与信任等级，防止检索内容伪装成高优先级指令。
  tags: [process, context-builder, pipeline]
  inputs: Task State、身份与作用域、候选信息源（配置/Session/Workspace/Memory/Knowledge/Skill/Tool Registry）
  outputs: 编译后的模型输入 + Context Manifest
  steps: 读状态→解身份→收集→权限过滤→排序去重→预算分配→压缩引用化→编译→出 Manifest
  missing_conditions: 无

- id: f1-57
  title: Context 优先级函数七因子
  type: framework
  source_chapter: 第 5 章 5.1
  source_quote: |
    "Context 构建不能只按相似度排序。"
  summary: |
    优先级综合七因子：约束强度（平台政策>经验建议）、任务相关性（是否影响当前阶段判断行动）、时间有效性（当前环境事实>过期结论）、来源可信度（权威系统事实>未确认模型摘要）、执行依赖（即将调用的工具说明与验收条件优先）、信息增量（重复信息合并或移除）、Token 成本（同等价值优先紧凑可引用表达）。
  tags: [decision, prioritization, context-engineering]

- id: f1-58
  title: Token 预算分区机制
  type: framework
  source_chapter: 第 5 章 5.1
  source_quote: |
    "预算不是静态百分比：当任务进入工具密集阶段时，工具定义和环境状态权重上升；进入最终综合阶段时，证据和验收条件更重要。"
  summary: |
    把可用窗口划为预算区：不可覆盖规则/当前目标与状态保留固定下限；最近交互/检索知识/Skill/工具 Schema 分动态额度；预留模型输出与后续 Observation 空间。
    预算随任务阶段动态调整（工具密集期工具定义权重升，综合期证据验收权重升）。
  tags: [mechanism, budget, context-engineering]

- id: f1-59
  title: Context Manifest 记录机制
  type: framework
  source_chapter: 第 5 章 5.1
  source_quote: |
    "每次模型调用都应生成一份 Context Manifest，记录模型究竟看到了什么，而不只是保存最终拼接文本。"
  summary: |
    Manifest 字段：source_type/source_id、scope（Global/Tenant/Project/User/Session/Task）、version、trust_level、permission_basis、selected_reason、token_count、transform（原文/摘要/截断/去重/引用化）、content_hash，外加 omitted 及原因。
    支撑三类工作：开发时解释模型为何遗漏信息、评估时比较两版本 Context 差异、安全审计时确认敏感内容为何进入输入。Context Policy（层级/检索范围/预算/阈值/披露/敏感处理）与可运行 Agent 版本绑定。
  tags: [mechanism, observability, audit, reproducibility]

- id: f1-60
  title: Active Context 结构与"最近"的任务语义定义
  type: framework
  source_chapter: 第 5 章 5.2
  source_quote: |
    "'最近'不只按时间定义。用户对目标的最新修改、尚未解决的工具错误、待审批动作和验收失败证据，即使产生得更早，也应被视为活动状态。"
  summary: |
    Active Context 六层：Stable goal and constraints（长期稳定不可遗漏）、Current task state（阶段/Plan/Todo/预算/阻塞）、Recent verbatim turns（需精确理解的最近交互）、Structured history summary、Retrieved facts、Artifact references（可按需读取）。
    关键思维：按任务意义而非时间定义"活动"——未解决错误、待审批、验收失败证据即使更早也是活动状态；已由 Artifact 证明的探索过程可退出窗口。
  tags: [mental-model, context-structure, recency]

- id: f1-61
  title: 压缩信息保留清单与 Commit-Compact-Rebuild-Validate 四步
  type: framework
  source_chapter: 第 5 章 5.2
  source_quote: |
    "对话压缩不是普通摘要。它要支持下一轮继续执行。"
  summary: |
    压缩须保留八类：原始目标与不可变约束；已确认事实及来源（区分事实/假设/模型建议）；已做决定与被否决方案；已执行行动与副作用；当前 Plan/Todo/阻塞；Artifact 与外部对象 ID；用户偏好/审批结果/权限模式；失败尝试及避免重复原因。
    四步生命周期：Commit（结构化事实入 Task State/Workspace）→Compact（可叙述历史转摘要，附来源与覆盖范围）→Rebuild（新摘要+状态+最近消息重建并检查关键约束）→Validate（完整性检查+续行用例验证无漂移）。
    高风险事实从 Task State/工具结果/审批记录确定性提取，模型只压缩叙述性内容。
  tags: [process, compaction, lifecycle]
  inputs: 原始历史、Task State、Artifact
  outputs: 结构化摘要 + 重建后的 Context + 完整性验证
  steps: Commit→Compact→Rebuild→Validate
  missing_conditions: 无

- id: f1-62
  title: 大结果卸载与按需取回
  type: framework
  source_chapter: 第 5 章 5.2
  source_quote: |
    "大结果不应反复进入每轮 Context。"
  summary: |
    数万行的日志/网页/搜索/分析结果写入 Workspace 或 Artifact Store，Context 只留摘要、首尾或关键片段、内容类型、大小、生成工具、权限范围和可继续读取的引用。
    需要细节时用搜索/范围读取/分页工具取回；引用必须稳定且受权限保护；结果会变化时记录读取时版本/时间/快照标识。节省的不只是单次输入，而是后续每轮不再重复携带。
  tags: [mechanism, offloading, artifact-management]

- id: f1-63
  title: 关键事实先进权威状态再移出活动上下文
  type: framework
  source_chapter: 第 5 章 5.2
  source_quote: |
    "任何会影响后续行动的事实，都应先进入权威状态，再允许从活动上下文中移除。"
  summary: |
    移出规则（if-then）：会影响后续行动的事实（审批、工具提交结果、Plan 状态、Artifact、外部对象 ID、预算消耗、用户变更）必须先写入权威状态，才允许从活动窗口移除。
    否则一次不准确的摘要就可能改变任务真实状态，任务失去可恢复性。
  tags: [rule, state-management, safety]

- id: f1-64
  title: 压缩质量五指标
  type: framework
  source_chapter: 第 5 章 5.2
  source_quote: |
    "压缩质量不能只看节省多少 Token。"
  summary: |
    五个同时衡量的指标：事实保留率（关键事实/约束/决定是否完整）、继续成功率（压缩或 Reset 后能否不重复大量探索地继续）、矛盾率（摘要是否与工具事实/Task State/最新指令冲突）、引用可用率（外部 Artifact 与分页引用是否仍可访问）、成本收益（省的 Token 与额外压缩调用/读取轮次的平衡）。
    压缩器、摘要 Schema 和阈值须版本化并进入回归范围。
  tags: [evaluation, metrics, compression]

- id: f1-65
  title: Context Reset 与 Continuation Package（文件式交接）
  type: framework
  source_chapter: 第 5 章 5.2（呼应 4.4.1）
  source_quote: |
    "新窗口不需要重放全部对话，而是从 Continuation Package、权威 Task State 和当前环境重新构建。"
  summary: |
    多次压缩仍不足或任务进入新大阶段时执行 Reset，前提是已形成一致继续点。Continuation Package 八项：Goal & Acceptance Criteria、Current Plan/Todo/Blockers、Structured Facts & Decisions、Workspace/Artifact Manifest、Active Async Tasks & Approvals、Relevant Memory/Knowledge References、Permission & Budget Snapshot、Next-step Brief。
    文件式交接适合工作区 Agent：计划/进度/发现/测试结果可由人和 Agent 共同检查，不因一次上下文结束而消失。
  tags: [process, context-reset, handoff]
  inputs: 权威 Task State、当前环境
  outputs: 可重新加载的信息包 + 新窗口重建的 Context
  steps: 形成一致继续点→打包八项→重置窗口→从 Package+State+环境重建
  missing_conditions: 无

- id: f1-66
  title: Call / Session / Task 三分
  type: framework
  source_chapter: 第 5 章 5.3
  source_quote: |
    "将 Task ID 绑定为消息线程 ID，会限制后台执行、多人协作和跨渠道续接。"
  summary: |
    三个不同生命周期的边界：Call（一次请求或恢复动作，秒到分钟，请求 ID/身份/凭证/游标）、Session（用户与 Agent 连续交互边界，分钟到数天，参与者/Channel/消息/偏好）、Task（围绕可验收目标的执行对象，可跨 Call/Session/进程/节点，目标/状态/计划/预算/Artifact/证据）。
    一个 Session 可发起多个 Task；长 Task 可在多个 Session 中查看干预恢复；两者分别保留并显式记录关联。
  tags: [mental-model, boundary, lifecycle]

- id: f1-67
  title: Event Log / Snapshot / Checkpoint 三种状态表示
  type: framework
  source_chapter: 第 5 章 5.3
  source_quote: |
    "三者不能互相替代。只有 Event Log，恢复成本会随任务长度增长；只有 Snapshot，无法解释状态如何形成。"
  summary: |
    互补三表示：Event Log（发生过什么，适合因果追踪/审计/重建）、Snapshot（某时刻聚合状态，适合快速读取当前视图）、Checkpoint（可安全恢复执行的位置=Snapshot+Continuation+幂等+环境依赖）。
    Harness 定义状态 Schema、事件到状态归并规则、乐观并发版本、安全点语义；面向逻辑状态接口编程（append_event/load_task_state/commit_task_patch/save_snapshot/put_artifact/create_checkpoint/search_workspace 等）而非绑定本地内存或某数据库。
  tags: [mental-model, state-representation, interface-design]

- id: f1-68
  title: Workspace 外部工作记忆模型
  type: framework
  source_chapter: 第 5 章 5.3
  source_quote: |
    "Workspace 为 Agent 提供可寻址、可检查、可逐步修改的外部工作空间。"
  summary: |
    目录约定（形式可变、逻辑边界必须）：inputs/（保持来源）、scratch/（可清理）、state/（Harness 管理 Plan/Todo/Continuation）、artifacts/（可交付产物）、evidence/（完成证明）、manifest（来源/版本/权限/保留策略）。
    与 Memory 区别：Workspace 服务当前任务显式工作过程、用户可直接查看编辑；Memory 是跨任务选择性保留的经验。不能因同在文件系统就用同样的保留与权限策略。
  tags: [structure, workspace, external-memory]

- id: f1-69
  title: Artifact 生命周期状态机
  type: framework
  source_chapter: 第 5 章 5.3
  source_quote: |
    "模型写出文件不等于产物已经完成。"
  summary: |
    Artifact 状态流转：Draft→Validating→Ready→Published/Rejected→Archived/Deleted。
    进 Ready 前须过格式/测试/业务验收；进 Published 往往还需权限审批与提交动作。Workspace 对象至少记录：稳定 ID、路径/对象引用、内容类型、创建者、来源、版本、权限范围、所属任务、状态、校验摘要、保留期限。
  tags: [state-machine, artifact-management]

- id: f1-70
  title: 四类 Memory 分类框架
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "Memory 不是一个无限增长的历史数据库。它是 Harness 有选择地写入、检索、更新和遗忘的信息。"
  summary: |
    按功能四类：Working Memory（当前任务临时事实，来自 Task State/Workspace）、Episodic（过去任务经验片段，按相似性与结果质量检索，须保留当时条件和 Outcome）、Semantic（稳定事实/偏好/实体关系，须来源与更新时间）、Procedural（已验证方法，稳定后应升级为受版本治理的 Skill 而非停留自由文本）。
  tags: [classification, memory, taxonomy]

- id: f1-71
  title: Memory 写入前六问
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "'每次任务结束自动总结并写入 Memory'很容易造成污染。"
  summary: |
    写入前判断六问：(1)是否会在未来任务产生可预期价值；(2)是环境验证的事实还是模型推测/用户临时表达；(3)属于哪个用户/项目/租户和保留周期；(4)是否含敏感或依法不应长期保存的数据；(5)已存在时应新增/合并/更新还是标记冲突；(6)未来错误时谁可纠正删除、派生索引如何清理。
    高价值 Memory 须带元数据：来源事件、证据、可信度、适用条件、作用域、创建者、最近验证时间、使用次数、成败反馈、过期策略、版本。
  tags: [checklist, memory-hygiene, write-policy]

- id: f1-72
  title: Memory 冲突处理与遗忘机制
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "当新信息与旧 Memory 冲突时，Harness 不应静默覆盖。"
  summary: |
    冲突处理：保留多个版本与来源，按时间或权威性选当前值，高影响场景请求确认。
    遗忘分层：长期未用/未验证/持续致错的 Memory 先衰减权重、进复核或遗忘；遗忘不是只删向量——须同时处理原文、摘要、索引、缓存、派生实体关系和可能引用它的 Skill/评估样本。
    检索侧：语义相关性+实体匹配+时间+作用域+可信度+历史效果综合；被检索到≠可直接写入 Context，须再过滤并标记为"历史经验"而非当前事实。
  tags: [mechanism, memory-governance, conflict-resolution]

- id: f1-73
  title: Knowledge 与 Memory 责任区分
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "企业 Knowledge 是由组织维护、具有来源和时效的业务事实……Memory 是 Agent 从任务和用户交互中选择性积累的经验或个体信息。"
  summary: |
    六维对比：来源（权威文档系统 vs 任务与反馈）、权威责任（内容所有者 vs Harness/用户/项目责任人）、更新方式（同步/发布/索引刷新 vs 写入/合并/纠正/衰减/遗忘）、使用风险（过期/权限泄漏/来源冲突 vs 污染/错误固化/跨用户混淆）、进入 Context 方式（带来源/权限/时间/版本 vs 带来源/作用域/可信度/适用条件）。
    实操规则：实时经营数据查权威工具而非离线索引；稳定文档用索引定位再读原始来源；任何检索结果进模型前完成租户与用户权限过滤。知识接口返回结构化 Evidence（content/source/version/owner/scope/reason/freshness/citation）。
  tags: [mental-model, knowledge-management, governance]

- id: f1-74
  title: 双层记忆实现（原始流水+整理记忆+Flush）
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "把'原始记忆流水'和'已整理长期记忆'分开。"
  summary: |
    实用实现：当日抽取的事实追加到原始流水（memory/YYYY-MM-DD.md），周期性合并去重到整理记忆（MEMORY.md）；前者保留来源过程，后者按策略进 System Context。
    对话压缩前先 Flush 关键事实，避免摘要把可复用经验一起抹掉。
  tags: [mechanism, memory, implementation-pattern]

- id: f1-75
  title: 信息去向五分法（一次任务产出写到哪里）
  type: framework
  source_chapter: 第 5 章 5.4
  source_quote: |
    "这一区分可以阻止最常见的记忆误用：把一次任务的临时状态当成长期经验，把模型总结当成企业事实。"
  summary: |
    任务产出的去向判断：当前补丁/测试状态/待审批→Task State/Workspace（须精确恢复）；可能复用的约束发现→Project Memory 候选（须来源与验证时间）；企业制度→Knowledge（制度所有者维护，Agent 不改写）；已验证方法→Skill 候选（测试、版本化、发布）；未验证的模型推测→不写入长期 Memory。
  tags: [decision, information-routing, memory]
  inputs: 一次任务中的各类信息
  outputs: 信息去向决策
  steps: 判断时效与复用性→验证状态→按五分法路由
  missing_conditions: 无

- id: f1-76
  title: Skill 能力资产模型
  type: framework
  source_chapter: 第 5 章 5.5
  source_quote: |
    "Tool 告诉 Agent'能做什么动作'，Skill 告诉 Agent'在某类任务中如何正确使用若干动作'。"
  summary: |
    Skill=封装验证过的任务方法，由 manifest（名称/版本/所有者/描述/适用性/所需工具权限环境/输入输出验收契约）+instructions+scripts+templates+examples+references+tests 组成。
    三个不等式：不等于一段 Prompt、不等于 Tool 别名（含模型判断步骤+确定性脚本）、不直接拥有额外权限（只在当前用户/任务/环境允许时才能执行）。
  tags: [asset-model, skill, packaging]

- id: f1-77
  title: Skill 渐进式能力披露三层
  type: framework
  source_chapter: 第 5 章 5.5
  source_quote: |
    "当企业积累数百个 Skill 时，全部注入每轮 Context 会迅速耗尽窗口，也会让模型选择错误能力。"
  summary: |
    三层披露：发现层（模型只见名称/简述/适用条件/主要风险）→选择层（Harness 按任务/权限/环境解析候选并加载完整 Manifest）→执行层（真正需要某步时才读详细指令/脚本/模板/参考）。
    选择不只靠语义匹配：还查模型兼容性、工具依赖、环境条件、租户许可、数据范围、版本状态——要求写生产系统而任务处于只读探索阶段时，Skill 可被发现但不能进入可执行状态。
  tags: [mechanism, progressive-disclosure, skill]

- id: f1-78
  title: 确定性步骤与模型判断的边界划分
  type: framework
  source_chapter: 第 5 章 5.5（3.2.1 亦出现）
  source_quote: |
    "模型负责语义判断，确定性组件负责可以明确表达的执行。"
  summary: |
    Skill 内步骤二分：稳定、重复、可编码且失败代价高的步骤（格式转换、固定校验、权限查询、测试执行）沉淀为脚本/工具/规则；需理解模糊目标、比较方案、解释异常、按新证据调整方向的保留为模型指令。
    价值：降低成本与方差。约束：脚本不能藏在说明文本中不受控运行，仍须经 Action Plane、环境与权限契约。
  tags: [decision, boundary, determinism]

- id: f1-79
  title: 从 Trace 沉淀 Skill 流程
  type: framework
  source_chapter: 第 5 章 5.5
  source_quote: |
    "一次任务成功，不意味着它已经成为可复用能力。"
  summary: |
    沉淀四步：从 Trace 提取稳定步骤→移除特定任务 ID、临时路径和一次性判断→为脚本和模板补充测试→形成 Skill（描述决定何时进入候选集，正文讲模型判断步骤，脚本承担确定性动作，参考目录存项目规范）。
    约束：若当前用户没有写权限或任务处于 Plan Mode，Skill 仍不能绕过 Action Plane 获得写能力。
  tags: [process, skill-creation, distillation]
  inputs: 成功任务的 Trace
  outputs: 可发布版本化的 Skill
  steps: 提取稳定步骤→去特定化→补测试→打包发布
  missing_conditions: 无

- id: f1-80
  title: Skill 发布生命周期与效果评估
  type: framework
  source_chapter: 第 5 章 5.5
  source_quote: |
    "Skill 的评估不应只看是否被模型选中。"
  summary: |
    生命周期：Draft（编写本地试验）→Test（脚本、契约与任务用例）→Review（安全/权限/领域评审）→Publish（进入允许作用域）→Observe（使用率、成功率、失败模式）→Update/Deprecate/Rollback。
    效果评估维度：启用前后任务成功率、步骤数、工具错误、人工修改量、成本、安全事件；很少被采用或持续降低效果的 Skill 调整描述、缩小范围或下线。Trace 必须定位到具体 Skill 版本（latest 指针不适合生产）；Agent 版本锁定允许的 Skill 集与版本范围。
  tags: [process, skill-release, lifecycle]
  inputs: Skill 包、测试用例、评审人
  outputs: 受治理的 Skill 版本 + 使用效果数据
  steps: Draft→Test→Review→Publish→Observe→Update/Deprecate/Rollback
  missing_conditions: 无

- id: f1-81
  title: 六层作用域模型（资产可见性边界）
  type: framework
  source_chapter: 第 5 章 5.6
  source_quote: |
    "不要先跨作用域召回再在生成端'提醒模型不要泄漏'，因为内容进入模型输入时，隔离已经失败。"
  summary: |
    资产作用域六层：Global（平台安全策略、通用 Skill，只读）、Tenant（企业制度/知识/Skill）、Project（项目规则/规范/Memory）、User（个人偏好/历史）、Session（当前交互临时输入）、Task（Plan/Todo/Scratch/Artifact/证据，含获授权父子任务）。
    检索与 Context 构建必须先确定调用身份、租户、项目和任务，再查询允许作用域；每项资产携带最小治理元数据（来源/所有者/作用域/版本/权限/敏感级别/保留周期/派生关系/状态）。
  tags: [scope-model, multi-tenancy, access-control]

- id: f1-82
  title: 三类污染的两端防护
  type: framework
  source_chapter: 第 5 章 5.6
  source_quote: |
    "防护需要覆盖写入和读取两端。"
  summary: |
    污染三型：记忆污染（错误推测/失败轨迹/恶意输入被长期写入当经验）、指令污染（外部文档/工具结果/Memory 文字被提升为高优先级行为规则）、跨租户污染（索引/缓存/摘要/Artifact 把一租户信息带入另一租户）。
    写入端：来源识别、验证、敏感数据检测、作用域绑定、冲突检查（高风险 Memory 先入候选区，人工或规则验证后发布）。读取端：身份过滤、信任标记、指令与数据分离、最小披露、输出审查。
  tags: [security, contamination, defense-in-depth]

- id: f1-83
  title: 派生链删除流程（真删除）
  type: framework
  source_chapter: 第 5 章 5.6
  source_quote: |
    "需要撤回的数据不能只通过降低检索分数来处理。"
  summary: |
    从稳定资产 ID 追踪派生链（原始文档→切片/向量/摘要/实体；Memory 是否被合并到高层总结；Skill 是否引用已下线模板）。删除时六步：禁止新的读取与 Context 注入→删除或隔离原始内容→清理索引/缓存/摘要/派生数据→更新引用该资产的 Manifest 与 Skill→保留法律允许且最小化的审计证明→触发受影响 Agent 版本的验证或重新发布。
    "遗忘"分权重衰减与彻底删除两种，须在策略中区分。
  tags: [process, deletion, compliance]
  inputs: 删除请求/保留期到期/来源失效、派生链记录
  outputs: 合规的完整删除 + 审计证明
  steps: 禁读→删原文→清派生→更新引用→留审计→触发重验证
  missing_conditions: 无

- id: f1-84
  title: 资产变更反退化闭环
  type: framework
  source_chapter: 第 5 章 5.6
  source_quote: |
    "Context Policy、Memory、Knowledge 和 Skill 的任何更新都可能改变 Agent 行为。企业应把资产变更纳入与代码相同的发布链。"
  summary: |
    资产变更走与代码相同的发布链：资产变更→结构与权限校验→受影响 Agent/用例分析→离线回放与安全评估→灰度进入新 Agent 版本→Trace 与 Outcome 监测→保留、修订或回滚。
    监测不只成功率：成本、延迟、引用准确性、跨租户泄漏、错误 Memory 使用、Skill 误选、上下文膨胀；评估按场景、租户、风险和任务复杂度分层（一个资产可能提升某类任务却损害另一类）。
  tags: [process, anti-regression, release]
  inputs: 资产变更（Context Policy/Memory/Knowledge/Skill）
  outputs: 灰度后的新 Agent 版本或回滚
  steps: 校验→影响分析→离线回放评估→灰度→监测→保留/修订/回滚
  missing_conditions: 无

- id: f1-85
  title: 三种构建路径的信息能力责任对比
  type: framework
  source_chapter: 第 5 章 5.6
  source_quote: |
    "Managed Agents 托管更多 Session 与执行基础，却不会替企业决定哪些知识可见、哪些记忆可写、哪些 Skill 可以在某个租户中使用。"
  summary: |
    按 Context 装配、Session/Task、Workspace/Artifact、Memory/Knowledge、Skill 五个能力域对比 Framework（企业全责，调优空间最大）/SDK（复用成熟 Harness，企业补多租户隔离、共享存储、验收）/Managed（平台托管 Session 与 Event，企业保存任务映射与结果治理）三条路径的责任边界。
    用于选路径时核对"哪些信息责任必须留在企业"。
  tags: [decision, path-comparison, responsibility]

- id: f1-86
  title: 行动链七步（从模型意图到环境事实）
  type: framework
  source_chapter: 第 6 章 6.1.1
  source_quote: |
    "一个完整的行动链不应从'调用 Tool'开始，也不应在'返回文本'处结束。"
  summary: |
    完整行动链：Model Intent→Schema Validation（名称/参数/约束）→Identity Binding（用户/Agent/任务身份）→Policy Decision（ALLOW/DENY/ASK）→Approval/HITL（必要时等待确认）→Execution（Tool/Sandbox/Remote Agent）→Observation（结构化结果/错误/Artifact）→State/Trace（任务事实与执行证据，回流 Model）。
  tags: [process, action-chain, action-plane]
  inputs: 模型行动意图、身份、策略、审批状态
  outputs: 结构化 Observation + State/Trace 证据
  steps: 意图→校验→身份→策略→审批→执行→观察→记录
  missing_conditions: 无

- id: f1-87
  title: 看见 / 注册 / 授权三分
  type: framework
  source_chapter: 第 6 章 6.1.1
  source_quote: |
    "若'出现在 Tool Schema 中'就等价于'可以调用'，最小权限、用户委派和阶段性只读模式都无法成立。"
  summary: |
    三个必须区分的事实：模型看见某个工具（Tool 描述进入 Context）≠Harness 注册某个工具（系统知道如何调用解析）≠当前用户和任务获得执行授权（这次行动可以发生）。
    企业可在 Registry 注册大量能力却只向当前模型披露少数；模型能描述高风险动作仍需 Policy 和审批决定是否执行。能力发现与执行授权必须分开设计。
  tags: [mental-model, authorization, capability-disclosure]

- id: f1-88
  title: 统一 Action Request / Action Result 契约
  type: framework
  source_chapter: 第 6 章 6.1.2
  source_quote: |
    "统一请求使预算、权限、审计、重试和 Trace 不必为每一种连接方式重新实现。"
  summary: |
    把 Function Calling/Shell/浏览器/Computer Use/远程 Agent 等不同表达统一为 Action Request：action_id/task_id、capability_id+版本、参数与输出 Schema、actor 身份、purpose 与阶段、环境与资源范围、副作用/可逆性/风险分级、幂等键与超时、审批与审计要求。
    Action Result 至少含：成败/未知/部分状态、结构化数据与紧凑 Observation、原始结果稳定引用、错误类别与可重试性、是否已产生副作用、实际身份/环境/时间/版本/成本、对 Task State 的候选 Patch、Verifier 可用证据。自由文本说明只作 purpose 候选输入，名称/参数/影响/身份由确定性代码校验；Harness 统一提交 State Patch。
  tags: [contract, unification, action-plane]

- id: f1-89
  title: Action 生命周期状态机
  type: framework
  source_chapter: 第 6 章 6.1.2
  source_quote: |
    "一个 Tool 失败不一定使整个 Task 失败，一个 Task 取消也可能需要等待已提交 Action 返回后再补偿。"
  summary: |
    Action 状态流转：REQUESTED→VALIDATED→AUTHORIZED/WAITING_APPROVAL→RUNNING→SUCCEEDED/FAILED/CANCELLED；外部异步系统还可能进入 ACCEPTED 或 WAITING_RESULT。
    任务状态与 Action 状态相关但不能混为一谈。案例原则："创建变更单"与"发布生产"是两个 Action（身份/风险/可逆性/审批要求不同），不能包装成一个模糊工具，即使它们调用同一平台。
  tags: [state-machine, action-management]

- id: f1-90
  title: Function Calling / MCP / A2A 职责边界
  type: framework
  source_chapter: 第 6 章 6.2.1
  source_quote: |
    "协议不会替 Harness 完成授权、租户隔离、业务语义验证和效果评估。"
  summary: |
    三层连接思维模型：Function Calling 是模型与 Harness 之间的意图接口（结构化表达想调用什么）；MCP 是 Harness 与能力提供方之间的连接协议（标准发现与连接工具/资源）；A2A 面向拥有独立任务循环、状态和自主性的远程 Agent（传递任务/消息/状态/Artifact，不只是执行函数）。
    共同边界：协议不替你做授权、隔离、语义验证、效果评估——MCP Server 能描述工具不代表调用方有权访问底层数据；A2A Agent 声明完成仍需委派方按契约验收。
  tags: [mental-model, protocol, layering]

- id: f1-91
  title: 面向 Agent 的 Tool 设计要点
  type: framework
  source_chapter: 第 6 章 6.2.2
  source_quote: |
    "合理粒度通常对应一个可描述、可授权、可观察和可验证的业务动作。"
  summary: |
    六个设计要点：名称描述说明业务目的/适用条件/非目标；输入 Schema 用明确类型/枚举/边界/示例；输出区分结构化结果/模型摘要/原始证据引用；明确是否只读/副作用/可逆/预览/幂等；错误用稳定分类（可重试/需修参/转人工）；认证与 Secret 留在执行侧不进描述或 Context。
    粒度权衡：过粗则影响范围大、审批验证难精确；过细则模型编排大量低级步骤增错误成本。
  tags: [design-guidelines, tool-design, granularity]

- id: f1-92
  title: Registry + Gateway + 渐进披露的能力治理架构
  type: framework
  source_chapter: 第 6 章 6.2.3
  source_quote: |
    "Registry 负责能力元数据、所有者、版本、健康、作用域和依赖；Gateway 负责协议入口、身份、凭证、路由、限流、审计和策略执行；Harness 则根据任务选择候选能力，并向模型渐进式披露。"
  summary: |
    三角色分工：Registry（目录与元数据治理）、Gateway（策略执行点）、Harness（按任务选择并披露）。
    直连 vs 治理的判断：能力少且信任边界简单可 Harness 直连；多 Agent/框架/团队共享大量能力时用 Registry+Gateway 避免凭证与治理逻辑在每套 Harness 重复。
  tags: [architecture, capability-governance, registry]

- id: f1-93
  title: Tool / MCP Server / Skill / Subagent / Remote Agent 五对象边界
  type: framework
  source_chapter: 第 6 章 6.2.4
  source_quote: |
    "把固定 API 包装成'Agent'不会自动获得规划和恢复能力；把复杂远程 Agent 当作同步 Tool，则会丢失任务状态、异步事件和 Artifact 语义。"
  summary: |
    五对象按"是否有独立 Agent Loop/封装什么/状态责任/使用方式"区分：Tool（无 Loop，一个动作，调用方组合验收）、MCP Server（协议不要求 Loop，一组能力，Server 实现 Host 集成）、Skill（无 Loop，任务方法，当前 Agent 保留 Loop 与结果）、Subagent（有 Loop，同 Harness 承载，父 Agent 保留总体责任，本地 Delegation）、Remote Agent（独立部署治理，对任务承诺负责、委派方最终采用，A2A/Agent API）。
  tags: [classification, boundary, capability-objects]

- id: f1-94
  title: 连接协议选择决策（Tool vs MCP vs A2A）
  type: framework
  source_chapter: 第 6 章 6.2.5
  source_quote: |
    "协议选择由能力是否拥有独立任务循环、是否需要异步状态和 Artifact 决定，而不是由协议的新旧或流行程度决定。"
  summary: |
    选择规则：短时、参数明确、结果立即返回→Tool/Function Calling；需跨 Host 复用工具或数据能力→MCP；能力拥有自己的任务循环、需异步状态/进度/消息/Artifact→A2A 或等价 Agent API。
    企业可保留内部专有协议，但应在 Harness 边界转换为统一 Action 与 Event 语义，避免协议差异侵入核心 Loop。
  tags: [decision, protocol-selection, connectivity]
  inputs: 能力特征（时长/状态/循环归属/异步需求）
  outputs: 协议选择
  steps: 判断是否独立任务循环→判断是否异步状态与 Artifact→按规则选协议
  missing_conditions: 无

- id: f1-95
  title: 远程 Agent 互操作契约
  type: framework
  source_chapter: 第 6 章 6.2.5
  source_quote: |
    "远程 Agent 不应获得父 Agent 的全部 Context。委派方发送最小必要信息，并将数据使用限制作为机器可执行策略一并传递。"
  summary: |
    跨系统委派七项互操作契约：对方身份/能力声明/版本/服务边界；任务目标/输入/上下文引用/数据使用限制；remote_task_id 与本地 task_id 关联；状态/进度/消息/Artifact/错误映射；超时/取消/幂等/重试/回调语义；凭证委派/租户边界/可审计代表关系；结果 Schema/证据/验收标准。
    接收方返回的消息与 Artifact 是外部输入，须经 Schema、权限和内容安全检查，不因来源是另一个 Agent 就视为可信系统指令。
  tags: [contract, interop, remote-agent]

- id: f1-96
  title: 环境能力风险控制矩阵
  type: framework
  source_chapter: 第 6 章 6.3
  source_quote: |
    "能用窄 Tool 完成的高风险动作，通常不应优先开放通用 Shell 或 Computer Use。"
  summary: |
    五种环境能力按"典型用途/主要风险/必要控制"治理：File（越界读写/路径穿越→根目录、读写范围、版本变更集）、Shell（任意代码/进程逃逸→命令策略、用户权限、资源网络隔离）、Code Interpreter（不受控依赖/资源耗尽→临时环境、包策略、CPU 内存时间限制）、Browser（Prompt Injection/会话劫持→域名策略、下载隔离、操作确认、内容信任标记）、Computer Use（影响难预测/不可逆→应用范围、屏幕输入隔离、预览与人工确认）。
    原则：窄 Tool 优先，通用能力用 Sandbox 限制在可接受边界内。
  tags: [risk-control, environment, security]

- id: f1-97
  title: Environment Contract（环境契约）
  type: framework
  source_chapter: 第 6 章 6.3.1
  source_quote: |
    "Harness 不应假定'本机一定有某目录、某版本依赖或可访问公网'，而应提交 Environment Contract。"
  summary: |
    契约字段：镜像/OS/架构、文件系统挂载与读写范围、网络出入站策略、Secret 引用与委派身份、所需工具包版本、CPU/内存/存储/GPU 配额、超时/空闲/并发、持久化/快照要求、审计与清理策略。
    兑现机制：Runtime 按契约选择环境，Sandbox 落实隔离；环境无法满足时 Action 应在执行前失败或请求降级，不让模型进入不确定状态。环境生命周期：创建→准备→挂载输入→运行→快照→恢复→清理→销毁（销毁前确认 Artifact 已转存、临时凭证已撤销；恢复时验证镜像/依赖/文件版本与 Checkpoint 一致）。
  tags: [contract, environment, sandbox]
  inputs: 任务所需能力与风险等级
  outputs: 可验证可移植的环境要求
  steps: 声明契约→Runtime 选型→Sandbox 落实→不满足即前置失败
  missing_conditions: 无

- id: f1-98
  title: 环境形态四选型与隔离粒度
  type: framework
  source_chapter: 第 6 章 6.3.1
  source_quote: |
    "托管 Harness 与自托管 Sandbox 可以组合：推理和任务编排由平台管理，实际工具执行留在企业环境。"
  summary: |
    四形态按优点/限制/适合场景选择：本地工作区（真实文件低延迟，难集中治理）、共享远程环境（复用基建，隔离与残留风险高）、托管隔离环境（按 Session/Task 创建生命周期清晰，数据边界需评估）、企业自托管 Sandbox（留企业网络，企业担容量与隔离质量）。可组合托管编排+自托管执行。
    隔离粒度按 Agent/Session/Task/Action 选择：粒度越细污染与横向移动风险越低，但创建与状态传递成本越高；在线多租户至少按 Task 或 Session 隔离；长期工作区把共享基础镜像与私有可写层分开。
  tags: [decision, environment-selection, isolation-granularity]

- id: f1-99
  title: Secret 与数据出站控制
  type: framework
  source_chapter: 第 6 章 6.3.3
  source_quote: |
    "Secret 不应出现在 System Prompt、Tool Schema、Task State 或模型可见环境变量中。"
  summary: |
    Secret 原则：不出现在模型可见位置；执行侧按 Action、身份和目的获取短时凭证，只注入目标工具或进程，记录使用而不记录密文。
    网络与出站：默认限制出站目标、协议和数据量；网页/下载/工具返回标记为不可信内容；高敏数据出站需额外策略或审批。Sandbox 防进程越界，Policy 定业务是否允许，缺一不可。
  tags: [security, secrets, egress-control]

- id: f1-100
  title: ALLOW / DENY / ASK 权限决策框架
  type: framework
  source_chapter: 第 6 章 6.4.1
  source_quote: |
    "仅按 Tool 名称做静态白名单，无法区分'读取一条测试记录'和'导出整个生产库'。"
  summary: |
    权限决策至少考虑：主体、租户、任务目的、能力、参数、目标资源、环境、当前阶段、数据敏感度、影响范围、可逆性、预算、历史审批。
    三类结果：ALLOW（直接执行并记录依据）、DENY（无论模型如何解释都不执行，返回结构化原因与替代项）、ASK（可执行但需指定人或系统确认）。ASK 不是默认兜底——优先用更窄 Tool、参数约束、预览、资源范围、Sandbox 降风险，只在目的或后果无法由策略充分判断时请求人工。
    风险映射示例：读项目内非敏感文件 ALLOW；改工作区未提交文件 ALLOW+Diff 可撤销；外部消息/发布/支付/删生产数据 ASK 或 DENY；跨租户/绕过安全/长期 Secret DENY。
  tags: [decision, permission, policy]
  inputs: 行动请求（主体/能力/参数/资源/风险）
  outputs: ALLOW/DENY/ASK + 决策依据
  steps: 收集决策要素→按风险映射评估→输出三类结果之一
  missing_conditions: 无

- id: f1-101
  title: HITL 三层次
  type: framework
  source_chapter: 第 6 章 6.4.2
  source_quote: |
    "HITL 可以出现在三个层次：Plan 审批……Action 审批……结果验收。"
  summary: |
    人在回路的三个介入层次：Plan 审批（执行前确认目标/范围/方案/影响面）、Action 审批（对具体 Tool/环境/远程 Agent 调用批准/拒绝/修改）、结果验收（对高影响 Artifact 或业务结果最终签署）。
    审批请求要素：想做什么、为什么、以谁身份、作用于什么对象、预计影响、参数与差异、是否可逆、失败处理、批准范围（仅本次/当前 Task/一类受限动作）。用户批准后 Harness 仍要重新校验对象版本和策略（防等待期间环境变化）。
  tags: [hitl, approval, human-oversight]

- id: f1-102
  title: Preview—Approve—Commit—Verify 高影响动作模式
  type: framework
  source_chapter: 第 6 章 6.4.2
  source_quote: |
    "不可逆或高影响动作可以统一采用'Preview—Approve—Commit—Verify'模式。"
  summary: |
    四步：Preview（展示接近实际提交的内容和影响，无法回滚的动作必须明示）→Approve（绑定身份、范围和对象版本）→Commit（使用幂等键执行）→Verify（查询真实系统状态并交付证据）。
    验证失败进入 Repair、Compensate 或 Escalate，而不是把已发送的请求当作成功。
  tags: [process, approval, high-risk-action]
  inputs: 不可逆/高影响动作请求
  outputs: 已验证的环境事实或补偿/升级路径
  steps: Preview→Approve→Commit（幂等）→Verify→（失败）Repair/Compensate/Escalate
  missing_conditions: 无

- id: f1-103
  title: 审批回调接入企业界面机制
  type: framework
  source_chapter: 第 6 章 6.4.3
  source_quote: |
    "回调只是交互入口，企业后端仍应依据当前登录身份、租户、资源版本和 Policy 再做一次确定性判断。"
  summary: |
    把 Harness 的工具授权回调接到自有审批界面（如 canUseTool/showApprovalDialog），展示关联任务、工具与参数；拒绝时返回 deny 并保留草稿说明未完成项。
    两条铁律：回调只是交互入口，后端须按当前身份/租户/资源版本/Policy 再做确定性判断；审批结果生成短时、限定目标的授权，而不是把整个 Session 切成无条件放行模式。
  tags: [mechanism, approval-integration, sdk]

- id: f1-104
  title: Steering / Interrupt / Resume 处理机制
  type: framework
  source_chapter: 第 6 章 6.4.3
  source_quote: |
    "直接把一条用户消息追加到长历史末尾，可能无法覆盖已经进入执行队列的旧计划。"
  summary: |
    运行中人工参与机制：Steering 表示为高优先级任务事件，由状态机在安全点处理；紧急中断取消可中断行动并阻止新 Action。
    恢复前流程：把用户新要求提交到 Task State→判断现有 Plan、权限和后台任务是否仍有效→重新构建 Context。不能简单追加消息到长历史末尾。
  tags: [process, steering, interruption]
  inputs: 用户追加信息/优先级变更/暂停/取消
  outputs: 状态机安全点处理后的任务调整
  steps: Steering 入队→安全点处理→提交 Task State→校验 Plan/权限/后台任务→重建 Context
  missing_conditions: 无

- id: f1-105
  title: Guardrail 分层部署与指令/数据区分
  type: framework
  source_chapter: 第 6 章 6.4.4
  source_quote: |
    "Prompt Injection 的关键防线是区分指令与数据。"
  summary: |
    Guardrail 可部署在输入、Context 构建、Action 请求、Tool 结果、输出五个位置，但不是唯一安全边界：确定可编码的权限与资源限制用 Policy 与 Sandbox；模型/分类器识别复杂语义风险、敏感内容、可疑意图，结果作额外信号。
    Prompt Injection 防线：网页/邮件/文档/MCP Server/工具结果/远程 Agent 返回均为不可信输入，不能改变平台政策、授权范围或当前 Tool Set；保留内容来源、限制外部文本进入高优先级指令层、对数据外传和高风险 Action 独立授权、必要时隔离读取与执行阶段。
  tags: [security, guardrail, prompt-injection]

- id: f1-106
  title: 八类外部语义事件模型
  type: framework
  source_chapter: 第 6 章 6.5.1
  source_quote: |
    "模型内部的隐藏推理不需要通过 Streaming 暴露。用户真正需要的是任务状态、可见说明、行动、证据和可操作选项。"
  summary: |
    Harness 产生与内部事件对应、经脱敏的外部语义事件八类：Text（文本增量/最终说明）、Progress（阶段/Todo/里程碑）、Tool（意图/执行状态/紧凑结果）、State（Running/Waiting/Paused/Completed 驱动上层状态机）、Approval（预览/风险/选项/决策）、Artifact（文件/报告/Diff/链接/版本）、Error（分类/可重试性/下一步）、Usage（Token/费用/预算）。
  tags: [event-model, streaming, ux]

- id: f1-107
  title: Channel 六职责适配层
  type: framework
  source_chapter: 第 6 章 6.5.2
  source_quote: |
    "Channel 是 Agent 与 Web、App、IDE、CLI、企业 IM 或其他入口之间的适配层。它不负责重写 Harness。"
  summary: |
    Channel 六职责：外部身份映射到平台身份；消息线程映射 Session 并选择/创建 Task；附件/回复/按钮/命令转统一输入事件；Harness 事件转渠道支持的消息/卡片/状态；跨入口切换保持任务连续；实施渠道级内容限制、速率、脱敏和审计。
    约束：Session/Task 分离在此尤其重要（IM 线程可看后台 Task、IDE 任务可在 Web 审批）；Channel 不应成为唯一状态存储。
  tags: [architecture, channel, adaptation]

- id: f1-108
  title: AG-UI 与 A2UI 区分选型
  type: framework
  source_chapter: 第 6 章 6.5.3
  source_quote: |
    "A2UI 描述'界面是什么'，AG-UI 处理'Agent 与应用如何交换事件'；两者并不互相替代。"
  summary: |
    AG-UI 表达 Agent 与应用之间的双向流式交互事件（生命周期/文本/工具/状态/中断语义），使前端不依赖某 Framework 内部对象；采用时企业仍需定义内部事件到外部事件的映射、字段脱敏、身份绑定和恢复游标。
    A2UI 让 Agent 输出声明式界面（表单/卡片/列表/动作），客户端用本地受信组件目录渲染而非执行模型生成代码。A2UI Payload 可通过 AG-UI 或其他传输发送。
  tags: [protocol-selection, ui, interaction]

- id: f1-109
  title: 事件流续传、限流与按身份视图机制
  type: framework
  source_chapter: 第 6 章 6.5.4
  source_quote: |
    "事件流必须假定网络会断开、客户端会重复连接、消费者速度不同。"
  summary: |
    可靠事件流机制：每事件有单调序号或可恢复游标；重连从最后确认位置续传，服务端支持去重；快消费者收增量，慢消费者先读状态快照再补关键事件。
    背压策略：高频 Token/细粒度日志可合并、采样或仅调试模式发送；状态、审批、Artifact、终态事件不能因背压丢弃。Cancel 后区分"已收到取消"与"底层 Action 已安全停止"。
    视图：同一 Task 在不同 Channel 按接收者身份与 Channel 能力生成视图（开发控制台看详细 Trace，客户应用只看业务进度，审批人看影响对象），不把内部 Trace 原样广播。
  tags: [mechanism, streaming, resilience, rbac-view]

- id: f1-110
  title: Task Trace 因果关联结构
  type: framework
  source_chapter: 第 6 章 6.6.1
  source_quote: |
    "Trace 需要因果关系，而不只是按时间排列的日志。"
  summary: |
    一次 Task Trace 至少关联：Agent/Harness 版本、Session/Task/父子拓扑、Context Manifest 与压缩决策、Plan/Todo/状态迁移、模型调用（延迟/Token/成本）、Action 请求与策略审批、工具/环境/远程 Agent 结果、Workspace 变更与 Artifact、重试降级与失败分类、Verifier 结果与完成证据、业务 Outcome 与用户反馈。
    Build 阶段为每个 Loop 阶段/Middleware/Tool/Subagent/Environment/Verifier 定义 Span；明确默认记录 vs 调试记录、敏感字段分类脱敏、Trace 关联版本与 Manifest、Outcome 写回来源、采样保留错误与长尾、跨系统传递 Trace Context。Build 时不埋稳定事件、版本和关联 ID，后续平台无法补出可信 Trace。
  tags: [observability, trace, causality]

- id: f1-111
  title: Agent Harness 与 Evaluation Harness 区分
  type: framework
  source_chapter: 第 6 章 6.6.2
  source_quote: |
    "Evaluation Harness 必须固定或披露模型、推理设置、Harness、工具版本、预算、重试、环境和评分规则。"
  summary: |
    两套系统职责不同：Agent Harness 在真实或测试环境完成一次任务（输入目标/Context/能力/环境/策略，输出轨迹/Artifact/完成证据/Outcome）；Evaluation Harness 以一致方式运行、回放和比较版本（输入数据集/环境/预算/评分器/待测版本，输出指标/失败聚类/版本差异/发布建议）。
    不固定运行条件，两版本分数差异可能来自条件而非所评估的 Patch。Verifier 是单次任务门禁（运行在 Agent Harness 内）；Evaluation 判断相对改进、退化分布和长尾风险，不把昂贵发布评估器嵌入每次线上任务；人工反馈非天然真值，需与环境证据结合解释。
  tags: [mental-model, evaluation, separation-of-concerns]

- id: f1-112
  title: 三层评测对象（单步/轨迹/最终结果）
  type: framework
  source_chapter: 第 6 章 6.6.2
  source_quote: |
    "只评最终结果可能掩盖高成本或高风险轨迹；只评单步又可能惩罚有效探索。"
  summary: |
    评测三层：单步（某次 Context/模型判断/Tool 调用——工具是否选对、参数是否正确、检索是否含关键证据；用规则/Schema/标注/局部模型评分）、轨迹（开始到结束的状态与行动序列——是否绕路、重复、越权、错误委派、过度消耗；用轨迹规则/序列比较/专家或模型评审）、最终结果（Artifact/环境终态/业务 Outcome——用测试/业务查询/人工验收/独立 Evaluator）。
    指标集：任务成功率、完成质量、人工接管率、工具错误、权限事件、延迟、成本、业务价值，按任务类型和风险分层。
  tags: [evaluation, multi-level, metrics]

- id: f1-113
  title: Trace → Harness Patch 效果闭环
  type: framework
  source_chapter: 第 6 章 6.6.2/6.6.3
  source_quote: |
    "效果闭环不应从单条失败直接改 Prompt。"
  summary: |
    六步闭环：Trace+Outcome→Failure Cluster（按症状与根因聚类）→Diagnosis（归因到 Model/Context/State/Tool/Policy/Environment/Loop）→Harness Patch（最小针对性变更）→Regression（成功、成本与安全回归）→Release Gate（灰度或拒绝）→新 Agent 版本。
    Patch 与根因一一对应：缺事实→调 Context/Knowledge；错误经验反复→修 Memory；不会稳定方法→新增修订 Skill；工具误用→改 Tool Schema 或权限；无进展→调 Loop/Plan/模型；环境不一致→修 Environment Contract；完成误判→强化 Verifier；只有确定模型能力不足才换模型或路由。回归集含原失败用例+相邻正常用例+安全对抗用例；模型升级后重检并删除失效的旧补偿逻辑。
  tags: [process, tuning-loop, harness-patch]
  inputs: Trace、Outcome、用户反馈
  outputs: 经回归与门禁的新 Agent 版本
  steps: 聚类→诊断→最小 Patch→三类回归→发布门禁→新版本
  missing_conditions: 无

- id: f1-114
  title: 四种构建路径的反馈闭环责任对照
  type: framework
  source_chapter: 第 6 章 6.6.4
  source_quote: |
    "构建路径决定谁承载执行，统一评估闭环决定系统是否真的变好。"
  summary: |
    按路径核对"实现重点 vs 企业必须保留的责任"：高代码 Framework（自定义 Tool/Sandbox/Permission Middleware/Trace；保留 Action Contract、隔离质量、Verifier）、产品化 Harness（复用工具循环+canUseTool+Session Store；保留租户入口、审批后端、Outcome 回流）、基于模型构建（Agent/Environment/Session/Event 托管+SSE 接入+Outcome 评估反馈修订；保留配置、数据边界、验收标准）、云产品预置能力（平台内组合+平台评估准入灰度；保留定义归属、授权范围、准入阈值）。
    统一评估平台位于四路径之上，对带版本的 Trace/Outcome/用例/评分器做一致比较。
  tags: [responsibility-mapping, path-comparison, feedback-loop]
```
