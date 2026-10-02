---
name: ai-agent-infra
description: |
  基于《AI Agent 手册》（阿里云）蒸馏的企业级 Agent 工程方法论。当用户要判断业务该用 Workflow 还是 Agent、选择多高的自主性与成熟度、选 Harness 构建入口（高代码框架/产品化 Harness/Managed Agent/云产品）、设计任务状态机与完成验证、设计 Context 管线与压缩、区分 Session/Task State/Workspace/Memory/Knowledge、设计受控行动与权限 HITL、选沙箱与运行时、做状态存储分层、设计 AI 网关（LLM/MCP/Agent 三语义）治理、设计异步任务与完成语义、组织多 Agent 团队与通信、建可观测与审计、做 Agent 安全防护、治理 Prompt/Skill/MCP/Agent 资产、做上线前仿真验证、做调优归因（环境→Harness→模型）与 SFT/RL/蒸馏选型、组织轨迹数据与 Pipeline、建黄金数据集与 Badcase 闭环、设计受控自进化、或做边缘全球优化时使用。不用于模型训练本身的算法研究、单一工具的产品操作手册、或无运行系统可观测数据的纯概念讨论。
metadata:
  cangjie.generated-by: cangjie-tools v2.5.0
  cangjie.variant: single
  cangjie.bundle-id: bundle.ai-agent-handbook
  cangjie.capability-count: 22
  cangjie.entrypoint-count: 1
---
# AI Agent 手册（AI Agent HandBook） — 全书能力入口

## 触发与不触发

**适用**：与本书能力域相关的咨询与任务（见下方路由表的意图列）。
**不适用**：
- 模型结构与训练算法本身的研究细节（只保留选型/验收口径）
- 具体云产品控制台操作步骤（以通用机制表述，产品名为示例）
- 无运行系统、无任何可观测数据时的臆测式诊断
- 个人提效/单次对话式 AI 使用（对象是可运营的 Agent 系统）
- 组织流程与人事管理（对象是工程系统；HITL 只到授权与审批机制层）

## 核心原则（常驻速览，概览类问题读到这里即可回答）

1. Agent = Model + Harness：模型之外的工程系统（上下文/状态/工具/环境/评估/风险）才是企业设计对象；可靠性不能寄托于模型本身
2. 最低充分架构：形态与成熟度不由产品名称决定，自主性必须与任务风险、权限、可撤销范围和可验证性匹配
3. 完成由环境证据判定，不由模型停止判定；关键事实先落权威状态，Context 只是视图
4. 能力发现与执行授权分开设计：知道工具存在 ≠ 有权执行；权限取交集、逐次校验、高危二次授权
5. 任务是需要长期管理的对象：状态必须外置，长于请求、宽于进程、可暂停可恢复
6. 任何 Agent Release（Prompt/Skill/工具/模型/Harness）都需可复现评估结果作为发布依据
7. 调优归因次序：先排除环境故障→再查 Harness 信息与控制→最后才归因模型；用模型训练弥补系统工程问题是代价最高的误判
8. 数据飞轮：Trace→Trajectory→Pipeline→黄金数据集→评估实验→Badcase 修复→受控自进化，证据先于改动

## 能力路由（先读本表，按意图加载 1 张能力卡）

| 用户意图 | 先读 | 补读/备注 |
|---|---|---|
| 立项时判断要不要 Agent 及什么形态（Workflow/Agent/Hybrid/长程/多智能体）；评估当前成熟度（L1-L4）与是否满足升级门槛；评审"直接上多智能体/全自主"类提议；评估采购产品的真实 Agentic 程度 | references/capabilities/arch-selection.md | references/capabilities/harness-entry.md、references/capabilities/task-contract.md、references/capabilities/action-plane.md、references/capabilities/observability.md |
| 选择构建入口（高代码框架/产品化 Harness·SDK/Managed Agent/云产品）；分配八个构建对象的责任人；明确托管/云产品路径下企业必须保留的责任；评估多来源 Agent 的统一纳管条件 | references/capabilities/harness-entry.md | references/capabilities/arch-selection.md、references/capabilities/task-contract.md、references/capabilities/action-plane.md |
| 设计任务状态机与"什么算完成"的验收标准；防死循环、防预算失控、防模型自称完成；设计子任务委派契约与失败策略；定义长任务断点恢复与失败→恢复映射 | references/capabilities/task-contract.md | references/capabilities/context-engineering.md、references/capabilities/state-layering.md、references/capabilities/action-plane.md、references/capabilities/async-completion.md |
| 设计每轮模型输入的装配管线与 System/Task Context 分层；处理长会话压缩、丢关键事实、越聊越笨；卸载大结果与按需取回、设计 Reset/续行包；审计"模型为何看见/遗漏/泄漏某信息" | references/capabilities/context-engineering.md | references/capabilities/state-layering.md、references/capabilities/task-contract.md、references/capabilities/skill-asset.md、references/capabilities/asset-governance.md |
| 设计状态存储分层模型（Call/Session/Task/三表示/接口）；制定记忆写入、冲突与遗忘策略，区分记忆库与知识库；规划 Workspace 分区与 Artifact 状态机；用信息五分法决定"这条信息写到哪" | references/capabilities/state-layering.md | references/capabilities/context-engineering.md、references/capabilities/skill-asset.md、references/capabilities/task-contract.md、references/capabilities/state-storage.md |
| 从成功轨迹沉淀可复用 Skill（清洗四步+打包）；打包、版本化与回滚能力资产；治理大量 Skill 的选择、披露与误选；建立资产变更的反退化发布链 | references/capabilities/skill-asset.md | references/capabilities/state-layering.md、references/capabilities/context-engineering.md、references/capabilities/arch-selection.md、references/capabilities/asset-governance.md |
| 设计工具权限模型与审批流（ALLOW/DENY/ASK）；给高危操作加人工确认与幂等补偿（PACV）；防注入、越权与权限语义滑移；设计运行中干预（Steering/中断）与对外事件推送 | references/capabilities/action-plane.md | references/capabilities/task-contract.md、references/capabilities/harness-entry.md、references/capabilities/arch-selection.md、references/capabilities/agent-security.md |
| 为执行不可信代码或长时任务的 Agent 选型沙箱/运行时环境；降低长任务等待期的资源占用并保证唤醒续行；设计实例故障后的接管流程，防止旧实例双写；沙箱上线前做时延/成本基线与故障场景验收 | references/capabilities/runtime-sandbox.md | references/capabilities/state-storage.md、references/capabilities/async-completion.md、references/capabilities/ai-gateway.md |
| 规划 Agent 状态外置方案（Event Log/Checkpoint/工作区/Artifact/记忆/知识/本体）；为状态对象做存储选型与验收；设计多租户隔离、一致性分级与成本保留策略；设计恢复流程并演练容灾 | references/capabilities/state-storage.md | references/capabilities/runtime-sandbox.md、references/capabilities/async-completion.md、references/capabilities/multi-agent-org.md、references/capabilities/state-layering.md |
| 规划 LLM/MCP/Agent 三语义网关职责与统一治理；设计重试预算、超时分层与预算档位（含硬上限）；设计 MCP 工具准入、参数级授权与审批状态机；建立任务成本归因账本与受控变更闭环 | references/capabilities/ai-gateway.md | references/capabilities/runtime-sandbox.md、references/capabilities/async-completion.md、references/capabilities/multi-agent-org.md、references/capabilities/agent-security.md |
| 设计异步/定时/工作流任务模型与状态机；定义任务完成标准（区分执行状态与业务 Outcome）；划分恢复与幂等责任并选错过补跑策略；在自主决策与预定义工作流间做编排选型 | references/capabilities/async-completion.md | references/capabilities/state-storage.md、references/capabilities/multi-agent-org.md、references/capabilities/agent-comm.md、references/capabilities/task-contract.md |
| 判定任务用单 Agent 还是 Agent 团队；接入异构/个人工作区 Agent 并验证协作链路；设计团队拓扑、角色与协作四阶段；制定团队级停止条件、聚合判定与权限模型 | references/capabilities/multi-agent-org.md | references/capabilities/async-completion.md、references/capabilities/agent-comm.md、references/capabilities/state-storage.md |
| 为某段链路选交互语义（请求-响应/流式/任务句柄/持久通道）；评估 MCP/A2A 等协议在本场景缺什么；设计断线重连续传与状态承载位置；治理海量会话通道与手脑分离架构 | references/capabilities/agent-comm.md | references/capabilities/multi-agent-org.md、references/capabilities/async-completion.md、references/capabilities/ai-gateway.md |
| 为已上线 Agent 设计可观测性接入方案（看什么/看哪里/怎么采）；排障定位：从聚合指标下钻到具体步骤的错误首次出现位置与延迟来源；设计满足合规的 Agent 审计证据链与风险研判处置闭环；把 Token 成本归集到会话/任务并在 Trace 上做任务效果三层评价 | references/capabilities/observability.md | references/capabilities/agent-security.md、references/capabilities/asset-governance.md、references/capabilities/agent-simulation.md、references/capabilities/trajectory-pipeline.md、references/capabilities/tuning-attribution.md、references/capabilities/arch-selection.md |
| 为接入内部系统与外部依赖的 Agent 做威胁建模与防护清单；设计 Agent 身份、凭据与双向授权（Token Vault/Token Exchange/OBO）；落实数据全生命周期六阶段与网络/计算层隔离控制；事件后补防线与合规审计所需的权限治理证据 | references/capabilities/agent-security.md | references/capabilities/observability.md、references/capabilities/asset-governance.md、references/capabilities/agent-simulation.md、references/capabilities/action-plane.md、references/capabilities/ai-gateway.md |
| 为多 Agent 共用的 Prompt/Skill/MCP 建立注册、版本与回滚机制；设计带安全与评测节点的资产发布评审 Pipeline（含外部 Skill 准入）；规划能力发现（ARD/RAD）与 Context 受控装配、Lock/Context Manifest；评估组织资产治理成熟度并给出分阶段演进路线 | references/capabilities/asset-governance.md | references/capabilities/agent-security.md、references/capabilities/observability.md、references/capabilities/agent-simulation.md、references/capabilities/skill-asset.md、references/capabilities/context-engineering.md |
| 为高风险流程 Agent 设计上线前的仿真验证方案（场景/替身/故障注入）；构建用户模拟器与环境模拟并做质控与事实一致性检查；建立可复现的回归证据链（配置/事件/统计三层复现）；正确解读仿真结论的统计边界与假设过期信号 | references/capabilities/agent-simulation.md | references/capabilities/observability.md、references/capabilities/asset-governance.md、references/capabilities/agent-security.md、references/capabilities/golden-badcase.md |
| 判断 Agent 效果问题该修环境、改 Harness 还是训模型；在 SFT、Agentic RL、模型蒸馏之间选训练路线并核算成本收益；为训练类改动设计验收、发布门禁与灰度回滚 | references/capabilities/tuning-attribution.md | references/capabilities/trajectory-pipeline.md、references/capabilities/golden-badcase.md、references/capabilities/controlled-evolution.md、references/capabilities/observability.md |
| 排查某次具体执行为什么出错或遗漏；定位耗时/Tokens 偏高与工具失败的处理问题；把运行记录加工成业务可读的日常质检数据集与候选样本 | references/capabilities/trajectory-pipeline.md | references/capabilities/golden-badcase.md、references/capabilities/tuning-attribution.md、references/capabilities/controlled-evolution.md、references/capabilities/observability.md |
| 建立团队共同的标准并构建黄金集/题库；判断低分样本是不是真 Badcase、该修 Agent 还是修评估器；用同题新旧对照实验验证一次修改并决定是否采用 | references/capabilities/golden-badcase.md | references/capabilities/trajectory-pipeline.md、references/capabilities/tuning-attribution.md、references/capabilities/controlled-evolution.md、references/capabilities/agent-simulation.md |
| 把反复探索的排障经验沉淀为经验库或可复用资产；对已确认 Badcase 做 Skill/Harness 局部修改并验证；决定调优自动化到什么档位并治理自进化风险 | references/capabilities/controlled-evolution.md | references/capabilities/golden-badcase.md、references/capabilities/tuning-attribution.md、references/capabilities/trajectory-pipeline.md |
| 解决远距离用户延迟高但 Region 内指标正常的问题；设计语义缓存与数据驻留合规方案；判断边缘优化该到哪一阶段并建全球指标准入 | references/capabilities/edge-optimization.md | references/capabilities/tuning-attribution.md、references/capabilities/golden-badcase.md、references/capabilities/trajectory-pipeline.md |

**非能力类查询**：
- 书名/作者/章节/整书概览 → references/overview.md
- 术语解释 → references/glossary.md
- 决策规则速查（不需要原文依据时） → references/cheatsheet.md
- 完整意图与关键词索引（本表未覆盖的意图先查这里） → references/capability-index.md

## 加载规则

- 每次任务先读本文件，再按路由表加载 **1** 张能力卡；任务明确跨域时最多加载 2 张。
- 概览/书名类问题不加载能力卡，用「核心原则」与 overview.md 回答。
- 路由表与 capability-index.md 都无法命中的意图，明确告知超出本书范围，不要硬套。

## 边界与判停

- 用户系统尚无 Trace/日志等任何运行事实时，如实说明"不可观测"，先建观测再谈调优
- 涉及真实资金/生产数据的高危操作设计时，只给机制与检查清单，执行决定权在人
- 书中定量数字（如成本比例、性能倍数）均标注为原书示例，需以企业自身基线校准后使用
