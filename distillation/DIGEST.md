# AI Agent 手册 — 精华 (DIGEST)

> 本文由 cangjie-skill 蒸馏生成，只呈现**通过三重验证**的方法论 — 不是全书摘要，是筛过水分的精华。
> 想深入某个方法论时，点小节末尾的卡片链接；想看全貌，读 [capability-index](.cangjie/capabilities/)。
> 作者: 阿里云（github.com/aliyun/ai-agent-handbook 开源社区） | 2026-09 | 22 个能力

## 这本书在讲什么

2025 年之后，企业落地 Agent 的主要矛盾变了：不再是"能不能快速搭一个 Agent"，而是**从 Demo 到生产**之间的三道鸿沟——工程化（从概率智能到可靠生产力）、规模化（稳定/安全/性能/成本）、组织化（从 Agent 孤岛进入核心业务流程）。

这本开源白皮书给出的总答案是：**Agent = Model + Harness**。模型提供认知，但任务越长、工具越多、环境影响越大，模型之外的工程系统（上下文组织、任务状态、工具控制、环境隔离、失败恢复、结果评估、风险约束）就越关键——这套系统叫 Harness，它才是企业要设计的对象。全书沿生命周期五阶段展开：架构 → 构建 → 运行 → 治理 → 调优，每阶段都收敛为可复用的工程判据与机制。

它值得听，是因为这不是模型的想象，而是阿里云一线工程师 + 16 家企业联名案例 + 1906 份调研问卷喂出来的经验与教训；全书反复强调的三句话构成基调：**完成由环境证据判定，不由模型停止判定；自主性必须与权限和可验证性匹配；调优归因先环境、再 Harness、后模型。**

---

## 一、架构：先判断，再动手

这两个能力共同回答"方向对了没有"——方向错了，后面四个阶段都在还债。

### 形态与成熟度选型（arch-selection）

**解决什么问题**：老板说"全部换成 Agent"，供应商说"我们的是 Multi-Agent 平台"——怎么判断该用什么形态？
**核心逻辑**：应用形态不是线性替代链。用四个维度判断：任务循环由谁掌握、执行路径何时确定、对外部环境的影响程度、是否需要跨会话持续或多人协作。企业主流形态是 Workflow 与 Agent 的 Hybrid。成熟度 L1-L4 不由产品名称决定，判据是自主性能否与任务风险相匹配。
**书中用法**：2026 调研显示 Agent 优先落地的是容错空间大、能人工兜底的场景，进入核心业务流程的不到 40%——"最低充分架构"不是保守，是风险定价。
**失效边界**：给确定性流程硬上自主 Agent（过度设计），或给高风险流程放自流（能力不足），两个方向都是错。
→ 深入： [`arch-selection`](references/capabilities/arch-selection.md)

### 构建入口选择（harness-entry）

**解决什么问题**：高代码框架、产品化 Harness（Coding Agent SDK）、Managed Agent、云产品——从哪开始建？
**核心逻辑**：四类入口不构成成熟度阶梯、不互斥，按定制深度、数据边界、运行责任、交付方式四判据选择。先把任务契约（Agent 能看见什么、凭证以谁的身份发、什么 Outcome 算完成）写成机器可执行的东西，再选入口。
**书中用法**：Kitta 领域定制 Code Review Agent 走高代码路线沉淀 7 万+ 补丁经验；信永中和用 AgentCore+AI 网关一周联调上线——两条路都对，因为任务契约不同。
→ 深入： [`harness-entry`](references/capabilities/harness-entry.md)

---

## 二、构建：三类工程契约

构建篇把 Harness 拆为任务、信息、行动三类契约——这是全书方法论密度最高的部分。

### 任务契约（task-contract）

**核心逻辑**：Agent Loop 以 Prepare→Model→Act→Observe→Verify 推进，配十状态任务状态机与多维预算。**模型只能申请完成，Harness 依据环境证据提交完成**。子任务委派走七要素契约，主 Agent 不交出完成责任。
**书中用法**：PatchPilot 把内核补丁交付做成可编排、可验证的工程闭环——每个阶段有证据门禁，而非"模型说改完了"。
**失效边界**：把进程结束当任务完成；Prompt 约束不等于 Harness 约束。
→ 深入： [`task-contract`](references/capabilities/task-contract.md)

### 信息契约（context-engineering / state-layering / skill-asset）

**核心逻辑**：Context 不是仓库里的长字符串，而是每次调用动态编译的产物——权限过滤先于相关性排序，Token 分区预算，Manifest 可审计。**任何影响后续行动的事实，先落权威状态，再允许移出上下文**；压缩保八类信息，大结果卸载留引用。Call/Session/Task 三分；Memory/Knowledge 分库治理；稳定的方法沉淀为受版本管理的 Skill，渐进式披露。
**书中用法**：MiniMax 海量长周期记忆底座（性能 +3 倍、存储成本 -75%）；调研显示 90% 企业明确需要上下文与记忆管理，而痛点不是窗口大小，是检索不准（54%）与遗忘机制缺失（48%）。
→ 深入： [`context-engineering`](references/capabilities/context-engineering.md) ｜ [`state-layering`](references/capabilities/state-layering.md) ｜ [`skill-asset`](references/capabilities/skill-asset.md)

### 行动契约（action-plane）

**核心逻辑**：行动链七步（Schema→身份→Policy→审批→执行→Observation→Trace），看见/注册/授权三分——**知道工具存在 ≠ 有权执行**。权限决策要素化为 ALLOW/DENY/ASK，高影响操作走 Preview—Approve—Commit—Verify，副作用行动重试前必须先证明未发生。
**书中用法**：GOAI 大赛作品 RevGuard 渠道佣金核算：金额不由模型产生，规则引擎精算+审批令牌——行动契约的教科书式应用。
→ 深入： [`action-plane`](references/capabilities/action-plane.md)

---

## 三、运行：地基与秩序

### 运行的地基（runtime-sandbox / state-storage / ai-gateway）

**核心逻辑**：沙箱按负载交付环境契约（模板/挂载/保留/验收/释放），隔离后端按可信度与租户边界选；状态先定正确性边界再选承载——Event Log 只追加、Checkpoint 七要素、Durable Execution=最新快照+增量回放+执行租约；AI 网关收敛为三种治理语义（LLM/MCP/Agent），错误分类先于重试、预算跨层原子扣减、MCP 准入四步（认证—协议校验—授权—转发）。
**书中用法**：畅捷通四阶段演进把故障定位从 10 分钟压到 30 秒，靠的是状态外置+可观测先行；调研中多模型路由与自动降级是比例最高的单项网关需求（63%）。
→ 深入： [`runtime-sandbox`](references/capabilities/runtime-sandbox.md) ｜ [`state-storage`](references/capabilities/state-storage.md) ｜ [`ai-gateway`](references/capabilities/ai-gateway.md)

### 运行的秩序（async-completion / multi-agent-org / agent-comm）

**核心逻辑**：异步任务的完成语义分五层（执行状态→运维状态→候选证据→Evidence→Outcome），恢复与幂等是执行端责任；多 Agent 先过收益成本判断再组队，三拓扑（主管—执行/对等/分层）按决策与汇报关系选，根 Task 前定团队级停止条件；通信按四面（人机/能力/协作/内构）×四档交互语义逐段选型——**流结束 ≠ 任务结束**。
**书中用法**：多 Agent 研发小队（AgentTeam+AgentLoop）从写代码走到端到端交付；调研显示多 Agent 最大痛点是状态与上下文衰减（60%）。
→ 深入： [`async-completion`](references/capabilities/async-completion.md) ｜ [`multi-agent-org`](references/capabilities/multi-agent-org.md) ｜ [`agent-comm`](references/capabilities/agent-comm.md)

---

## 四、治理：让自主系统可信

治理四支柱：可观测、有边界、资产可管理、行为可验证。

- **可观测与审计（observability）**：先规划"看什么×看哪里"，统一 Span 层级建可聚合 Trace，排障走"聚合指标→下钻"，审计以四要素证据链（主体授权→意图指令→执行事实→结果证据）+分层研判闭环。
- **安全（agent-security）**：盾（全栈纵深防护）+缰绳（身份/逐次校验/高危二次授权）双主线——Agent 既是被攻击对象也是自主行动主体。每个 Agent 发唯一数字工牌，出站默认拒绝。
- **资产治理（asset-governance）**：Prompt/Skill/MCP/Agent 统一为"逻辑资源+不可变版本+结构化引用"，发布走带安全与评测节点的评审 Pipeline，发现与资格分离。
- **上线前仿真（agent-simulation）**：在授权与后果两判据内，用用户模拟+环境模拟反复演练，零违规结论按 3/n 置信上界表述。

**书中用法**：Agentic SOC 用 SOCBench 211 任务基线评测驱动安全产品研发；调研中轨迹自动评估仅占 6%，但有评估体系的企业任务成功率约为无评估企业的两倍——治理就是生产力的定量证据。
→ 深入： [`observability`](references/capabilities/observability.md) ｜ [`agent-security`](references/capabilities/agent-security.md) ｜ [`asset-governance`](references/capabilities/asset-governance.md) ｜ [`agent-simulation`](references/capabilities/agent-simulation.md)

---

## 五、调优：数据飞轮

### 归因先行（tuning-attribution）

**核心逻辑**：全书防最贵误判的能力。归因次序：**先排除能够独立复现的执行环境故障→再检查 Harness 是否提供了充分信息与可靠控制→最后才判断是否属于模型能力缺口**。归因到模型后，按可示范/可验证反馈/教师领先选 SFT、Agentic RL 或蒸馏，训练前后同口径复测，五门禁灰度上线。
**书中用法**："把本应由上下文、工具协议或运行控制解决的问题当成模型不行，往往是代价最高的一类误判"——17 章开篇即立此判据。
→ 深入： [`tuning-attribution`](references/capabilities/tuning-attribution.md)

### 数据飞轮四环（trajectory-pipeline / golden-badcase / controlled-evolution / edge-optimization）

**核心逻辑**：把 Trace 组织成可复用的 Trajectory（带目标、步骤、结果、判据），经声明式 Pipeline 加工成业务样本，沉淀为带判据的黄金数据集，用持续评估与单变量实验发现并验证 Badcase，验证有效的经验受控转化为 Memory/Skill/工具/运行机制。边缘优化作为独立专题：就地三条件分类请求、缓存键六维防串租户、"不优化"也是正确选项。
**书中用法**：Mamba Insight 从 500+ 需求标注出 9 个 Skill + 45 组 QA 对资产；塔斯汀用 AI 反馈三元组驱动数字员工矩阵迭代。
→ 深入： [`trajectory-pipeline`](references/capabilities/trajectory-pipeline.md) ｜ [`golden-badcase`](references/capabilities/golden-badcase.md) ｜ [`controlled-evolution`](references/capabilities/controlled-evolution.md) ｜ [`edge-optimization`](references/capabilities/edge-optimization.md)

---

## 收束：从 Agentic Application 到 Agentic OS

第 30 章把全书收拢为五项共识（架构对象是系统/任务是长期对象/发现与授权分开/自主性与边界匹配/证据是发布前提），并指出六类跨应用工程约束（任务标识/环境成本/能力元数据/上下文边界/授权派生/证据口径）指向应用之下的共享系统层——Agentic OS 的问题域。这是前瞻推测而非已验证形态，但它解释了本书为什么把"平台层"讲得这么重。

> "当思考开始变得前所未有地充沛，我们应当以怎样的架构，把它转化为真实、可靠且值得信任的价值？"——这 22 个能力，就是这本手册此刻给出的最接近工程真相的答案。
