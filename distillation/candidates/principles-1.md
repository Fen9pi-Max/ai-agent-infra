# principle extractor 分区1：第 1-5 章

```yaml
# ============ 第 1 章 AI 原生应用的新阶段 ============

- id: p1-01
  title: 模型不能独立构成 Agent，任务越长 Harness 越重要
  type: principle
  source_chapter: 第 1 章 · 章首（另见第 3 章 3.1）
  source_quote: |
    模型获得更大的任务自主权和环境操作权限，并不意味着模型可以独立构成 Agent。相反，任务越长、工具越多、环境影响越大，模型之外的工程系统就越重要。
  summary: |
    授权模型自主行动后，上下文组织、任务状态、工具控制、环境隔离、失败恢复、结果评估与风险约束必须由模型之外的工程系统（Harness）承担。
    判别式：任务长度、工具数量、环境影响三个变量越大，Harness 的工程投入越关键。
  tags: [principle, harness, responsibility]

- id: p1-02
  title: 自主性必须与任务风险相匹配（贯穿性决策原则）
  type: principle
  source_chapter: 第 1 章 · 1.2.2
  source_quote: |
    自主性与任务风险相匹配这一原则并不是某个局部控制项，而是贯穿应用形态选择与架构设计的决策原则。
  summary: |
    该原则既决定单个任务采用 Workflow、Single-Agent 还是 Hybrid，也决定是否扩展为 Long-Horizon 或 Multi-Agent。
    它不是某个安全开关，而是从形态选型到权限设计全程生效的顶层判据。
  tags: [principle, autonomy, risk]

- id: p1-03
  title: 最低充分架构原则
  type: principle
  source_chapter: 第 1 章 · 1.4.1（1.5 小结重申）
  source_quote: |
    企业应当结合任务价值、风险、运行规模和工程成本，为每类任务选择成本与风险可接受的最低充分架构。这是贯穿本章的决策原则。
  summary: |
    反"越智能越好 / 越复杂越好"：每类任务只选择足以在其风险与成本约束下交付目标的架构，不为可能的未来预先加码。
    第 2 章开篇将同一原则表述为"形成与任务风险相匹配的最低充分架构"。
  tags: [principle, architecture, minimal-sufficient]

- id: p1-04
  title: 应用形态不是线性替代链
  type: rule
  source_chapter: 第 1 章 · 1.2.1
  source_quote: |
    容易被理解为一条线性的产品代际演进链，仿佛后一种形态必然取代前一种形态。这种认识忽视了不同任务对确定性、自主性、持续时间、协作结构和风险控制的差异，容易误导企业的技术选型。
  summary: |
    判断形态的真正维度：任务循环由谁掌握、执行路径在什么阶段确定、系统能多大程度影响外部环境、是否需要跨会话持续或多人协作。
    Chat/RAG、Workflow、Copilot 与 Agentic 系列将长期并存，按任务属性选择而非按代际升级。
  tags: [rule, form-factor, selection]

- id: p1-05
  title: 任务是否正确完成必须检查环境事实
  type: rule
  source_chapter: 第 1 章 · 1.1.1
  source_quote: |
    回答是否流畅，可以通过人的阅读进行判断；但任务是否正确完成，则必须检查工具调用轨迹、文件变更、数据库状态、测试结果或其他环境事实。
  summary: |
    内容质量与任务完成是两种判定：前者可由人或模型判断，后者必须以外部环境中的可验证事实为依据。
    该规则在第 4 章 4.6（完成验证）与第 2 章五项设计原则中反复出现。
  tags: [rule, verification, environment-evidence]

- id: p1-06
  title: Agentic 生产暴露的六类工程问题清单
  type: checklist
  source_chapter: 第 1 章 · 1.1.2
  source_quote: |
    一是代码仓库的规模往往超过模型的单次上下文窗口，Agent 必须对信息进行检索、筛选、摘要与外置存储。
  summary: |
    Coding Agent 集中暴露、任何 Agentic 应用进入生产都必须解决的六类问题：
    (1) 信息超出窗口→检索/筛选/摘要/外置；(2) 跨步骤多轮→维护计划进度待办；(3) 真实副作用→权限控制、环境隔离、不可逆操作审批；
    (4) 中间失败→错误观测、重试、检查点、恢复；(5) 结果不能模型自评→测试/构建/静态检查/Diff/独立评估器验证；(6) 人无法逐 Token 监督→关键节点 Steering 与 HITL。
  tags: [checklist, engineering-problems, production]

- id: p1-07
  title: 企业主流形态是 Workflow 与 Agent 的混合（Hybrid）
  type: principle
  source_chapter: 第 1 章 · 1.2.2
  source_quote: |
    企业级应用的主流形态不会是"将所有步骤都交给 Agent"，而是 Workflow 与 Agent 相结合的混合形态（Hybrid）。
  summary: |
    两条实现路径：在业务 Workflow 中嵌入 Agent（把不确定性限制在清晰边界内）；或由 Agent 调用经过测试的 Workflow/Skill（把成熟做法从重复推理中抽离）。
    确定性强、可穷举的步骤用 Workflow；路径不可穷举、需环境反馈的部分用 Agent。
  tags: [principle, hybrid, workflow]

- id: p1-08
  title: 低风险可逆给高自主，高风险不可逆加确定性检查与人工审批
  type: rule
  source_chapter: 第 1 章 · 1.2.2
  source_quote: |
    对于低风险、可逆的操作，系统可以赋予 Agent 较高的自主性；对于高风险、不可逆的操作，则需要增加确定性检查和人工审批。
  summary: |
    自主性授予的操作性规则：以操作的风险与可逆性为分档依据，风险越高，确定性检查与人工介入越重。
  tags: [rule, autonomy, risk-tiering, hitl]

- id: p1-09
  title: 引入长程/多智能体结构的门槛条件
  type: rule
  source_chapter: 第 1 章 · 1.3.3（1.2.3 重申）
  source_quote: |
    企业应先验证边界明确的 Single-Agent 应用，只有在单个 Agent 受到时间跨度、上下文容量、职责冲突、权限隔离或并行效率限制时，才引入相应的长程或多智能体结构。
  summary: |
    触发扩展的五种具体限制：时间跨度、上下文容量、职责冲突、权限隔离、并行效率。
    另一表述：更复杂的形态只有在解决了单 Agent 无法以合理成本可靠完成的问题时才是合理选择（1.2.3）。
  tags: [rule, multi-agent, long-horizon, escalation]

- id: p1-10
  title: Long-Horizon 与 Multi-Agent 是两个正交维度
  type: rule
  source_chapter: 第 1 章 · 1.2.3
  source_quote: |
    Long-Horizon Agent 与 Multi-Agent 不是同一维度。前者解决任务如何跨时间持续，后者解决多个执行主体如何协作。
  summary: |
    单个 Agent 可以执行长任务，Multi-Agent 系统也可以只完成短时任务；二者可独立采用也可组合，不构成必经的升级阶段。
  tags: [rule, form-factor, orthogonal-dimensions]

- id: p1-11
  title: Agentic 阶段的六个判定特征
  type: checklist
  source_chapter: 第 1 章 · 1.3.2
  source_quote: |
    一个应用是否进入 Agentic 阶段，可以通过以下六个特征进行判断。
  summary: |
    (1) 以目标和任务结果为中心；(2) 在运行时决定部分执行路径；(3) 能够对外部环境产生作用；
    (4) 维持跨步骤、跨请求的任务状态；(5) 自主行为受到确定性机制约束（模型不能自行绕过）；(6) 通过可观测与评估持续改进。
  tags: [checklist, agentic-criteria, definition]

- id: p1-12
  title: 六特征是判断依据而非功能清单，不必采用最高自主性
  type: rule
  source_chapter: 第 1 章 · 1.3.2
  source_quote: |
    这六项特征是判断应用架构形态的依据，而不是必须同时启用的功能清单。Agentic Application 不需要在所有任务中采用最高自主性，也不必默认采用 Long-Horizon Agent 或 Multi-Agent。
  summary: |
    判定"是否属于 Agentic 形态"与"开启多少自主性"是两件事；形态判定不要求全量开启功能或最高档自主。
  tags: [rule, agentic-criteria, autonomy]

- id: p1-13
  title: Agent 不等于 Agent 框架
  type: rule
  source_chapter: 第 1 章 · 1.3.3
  source_quote: |
    Agent 框架可以提供 Agent Loop、Tool、Memory 等开发抽象，但框架本身不会自动形成能够围绕目标持续执行、连接业务环境并交付可验证结果的 Agent。
  summary: |
    采用框架不等于得到可生产的 Agent；上下文组织、状态管理、工具接入、权限约束、运行保障和结果评估仍需企业按场景完成。
  tags: [rule, boundary, framework]

- id: p1-14
  title: 应用形态不等于平台形态
  type: rule
  source_chapter: 第 1 章 · 1.3.3
  source_quote: |
    同一种应用形态既可以依托统一平台运行，也可以采用独立的工程实现；即使平台能力更完整，也不意味着具体应用必须采用长程或多智能体结构。
  summary: |
    Single/Long-Horizon/Multi-Agent 描述任务如何执行；Agent Platform 提供共享构建运行治理能力。两者是不同层面的选择，互不隐含。
  tags: [rule, boundary, platform]

- id: p1-15
  title: 成熟度不由产品名称决定，按四维度综合判断
  type: rule
  source_chapter: 第 1 章 · 1.4.1
  source_quote: |
    Agentic Application 的成熟度不能由产品名称或技术标签决定，而应从任务循环、执行跨度、环境影响和生产治理四个维度进行综合判断。
  summary: |
    名称含 Agent 的产品可能只有单轮生成；以 Workflow 为核心的系统可能已具备持久状态、权限、可观测与持续评估。
    四个判断维度：任务循环、执行跨度、环境影响、生产治理。
  tags: [rule, maturity, judgment]

- id: p1-16
  title: 企业成熟度四级模型（L1-L4）
  type: checklist
  source_chapter: 第 1 章 · 1.4.1（表 1-4）
  source_quote: |
    本白皮书将 Agentic Application 的企业成熟度划分为四个级别。
  summary: |
    L1 辅助生成（内容生成/知识检索，闭环由人）；L2 受控自动化（预定流程+局部动态决策+审批）；
    L3 Agentic Execution（Agent 持有任务循环：动态规划、工具与环境操作、持久状态、故障恢复）；
    L4 规模运营与持续优化（多任务多租户、策略控制、离线在线评估、灰度持续优化）。
    各级附升级门槛：L1→可复用 Prompt/知识/基础评测；L2→明确自主边界+Harness+轨迹评估；L3→Runtime/Sandbox/Observability/Security/Evaluation；L4→共享平台+组织知识+标准+自动化降治理成本。
  tags: [checklist, maturity-model, l1-l4]

- id: p1-17
  title: 应用不必逐级升级成熟度
  type: rule
  source_chapter: 第 1 章 · 1.4.1
  source_quote: |
    它们并不意味着每个应用都必须依次升级：低风险的知识助手可以长期停留在 L1，交易审批系统可以选择 L2，以确定性 Workflow 保持核心路径。
  summary: |
    成熟度级别按任务风险定格：低风险知识助手可长期 L1，交易审批可停在 L2；只有路径难以预知的场景（研发、运维、研究、复杂客服）才进入 L3；规模化运行涉多任务/多租户/高影响操作时才需 L4。
  tags: [rule, maturity, no-forced-upgrade]

- id: p1-18
  title: 升级的必要性判据
  type: rule
  source_chapter: 第 1 章 · 1.4.1
  source_quote: |
    只有在当前架构无法以可接受的风险和成本交付任务，而新增能力具有明确价值时，升级才有必要。
  summary: |
    双条件缺一不可：当前架构确实无法在可接受风险/成本内交付 + 新增能力有明确价值。否则不升级。
  tags: [rule, upgrade-criteria]

- id: p1-19
  title: 形态与成熟度分别判断
  type: rule
  source_chapter: 第 1 章 · 1.4.1（1.5 小结重申）
  source_quote: |
    Single-Agent、Long-Horizon Agent 与 Multi-Agent 都可以处于 L3 或 L4：采用哪种形态取决于任务结构，而处于哪个成熟度取决于运行与治理能力的完备程度。
  summary: |
    形态由任务结构决定，成熟度由运行与治理能力决定；同种形态可处于不同级别，二者不可混为一谈。
  tags: [rule, form-factor, maturity]

- id: p1-20
  title: 架构设计先于具体构建
  type: principle
  source_chapter: 第 1 章 · 1.4.2
  source_quote: |
    当企业将一个 AI 应用从演示推向生产时，架构设计应当先于具体构建。架构设计不是在组件清单中选择尽可能多的能力，而是根据任务目标、风险、执行跨度、协作边界和运行规模，对参考架构进行裁剪。
  summary: |
    架构设计的产出是责任边界（Model 与 Harness、确定性流程与自主决策、Single 与 Multi、人与系统），先裁剪后构建。
    第 2 章 2.4.2 把它列为生命周期第一阶段。
  tags: [principle, architecture-first]

- id: p1-21
  title: 企业级 Agentic 应用的五个架构问题清单
  type: checklist
  source_chapter: 第 1 章 · 1.4.2
  source_quote: |
    在这一前置决策之后，企业需要依次回答以下五个相互衔接的问题。
  summary: |
    (1) 如何设计架构：Workflow/Single-Agent/Hybrid？是否确有必要 Long-Horizon/Multi-Agent？任务边界、自主程度、人机协作、分工、确定性控制点？
    (2) 如何构建：Loop/Context/State/Memory/Tool/Skill 如何组合？哪些动态决策、哪些固化？
    (3) 如何运行：调度、状态持久化、工具执行环境、失败恢复、隔离？
    (4) 如何治理：谁负责、什么身份访问什么、哪些操作审批、如何观测审计？
    (5) 如何调优：如何判断质量、定位问题、验证新版本并回滚？
  tags: [checklist, architecture-questions, lifecycle]

- id: p1-22
  title: 授权必须每次高影响操作时判定，不可启动时一次授予
  type: rule
  source_chapter: 第 1 章 · 1.4.3
  source_quote: |
    授权不能只在启动时授予，而要在每次高影响操作时按策略强制判定；并且模型可能尝试绕过，边界必须是它无法自行跨越的。
  summary: |
    Agent 的授权模型区别于传统进程"创建时一次授予、越权即拒"：高影响操作逐次按策略强制判定，且可撤销；边界必须是模型无法自行绕过的确定性机制。
  tags: [rule, authorization, enforcement]

- id: p1-23
  title: 重启不等于正确恢复
  type: rule
  source_chapter: 第 1 章 · 1.4.3
  source_quote: |
    一个 Run 可能已经挂起等待审批，且已经向外部系统发出副作用，重启并不等于正确恢复。
  summary: |
    故障恢复以任务（Run）为单位而非进程单位；恢复必须考虑挂起状态、已发出的副作用，需要检查点、幂等与跨系统补偿。
  tags: [rule, recovery, run]

- id: p1-24
  title: 云与 Kubernetes 管理 Pod 而不是 Run
  type: rule
  source_chapter: 第 1 章 · 1.4.3
  source_quote: |
    区别在于它们管理的是 Pod 而不是 Run：重启一个探针失败的容器，不等于正确恢复一个已经发出副作用的任务。
  summary: |
    云平台/K8s 是 Agent 公共承载层的底座而非替代；缺的是把容器、配额、IAM 翻译成 Run、预算租约与操作记录的 Agent 原生抽象。
  tags: [rule, kubernetes, agentic-os]

# ============ 第 2 章 Agentic Application 参考架构 ============

- id: p1-25
  title: 四类责任边界不可混淆
  type: checklist
  source_chapter: 第 2 章 · 章首
  source_quote: |
    Context 与 State 不分，会将不完整的对话历史误当成可恢复的任务事实；沙箱、权限和工具描述混在一起，则会将模型知道某个工具误解为模型有权执行某个操作。
  summary: |
    四类必须划清的边界及其失败后果：
    (1) Model 与 Harness 不分→模型升级变成应用重构；(2) 编排逻辑与 Runtime 不分→任务语义与基础设施强绑定；
    (3) Context 与 State 不分→不完整对话历史被当可恢复任务事实；(4) 沙箱/权限/工具描述混在一起→"知道工具"被误解为"有权执行"。
    演示阶段不暴露，任务变长、工具变多、租户变多后集中爆发。
  tags: [checklist, boundary, failure-modes]

- id: p1-26
  title: 三个视图互相正交，不能互相推导
  type: principle
  source_chapter: 第 2 章 · 2.1.1
  source_quote: |
    三个视图不能互相推导。组件齐全并不意味着责任清晰，责任清晰也不意味着变更可控。因此，在讨论某个能力时，需要明确当前处于哪个视图。
  summary: |
    组件视图避免能力遗漏；平台视图避免责任混乱；生命周期视图避免把一次性演示当可运营系统。
    例：同一 State Store 在三视图中分别是持久化能力、数据与资源面、运行产生调优读取的证据源。讨论能力前先声明所处视图。
  tags: [principle, views, orthogonal]

- id: p1-27
  title: 五个能力责任域划分
  type: checklist
  source_chapter: 第 2 章 · 2.1.2（图 2-1）
  source_quote: |
    这里的"层"用于组织架构责任，不表示严格的调用栈或建设顺序。
  summary: |
    (1) 业务与应用层：定义目标、交互、任务组织与验收条件，不实现通用 Agent 能力；
    (2) Agent 构建与编排层：组合 Model/Harness 编排/Context 策略/能力定义成任务循环，只描述和绑定能力，实际执行由生产运行层承载；
    (3) 生产运行层：调度、状态持久化、工作区、隔离、恢复、流量治理；
    (4) 治理与控制层：谁可发布运行修改、版本流转、策略下发执行审计；
    (5) 调优层：从运行获得 Trace/Metric/成本/反馈，产出候选变更与验证证据，不直接修改生产 Agent。
  tags: [checklist, responsibility-domains, reference-architecture]

- id: p1-28
  title: 调优层不直接修改生产 Agent
  type: rule
  source_chapter: 第 2 章 · 2.1.2
  source_quote: |
    调优层不直接修改生产中的 Agent；变更必须回到 Agent 构建与编排层形成新版本，并经过治理与控制层的准入门禁后再进入生产运行层。
  summary: |
    调优产出的 Prompt/Memory/Skill/Harness 补丁只能作为候选变更回流构建阶段，经准入门禁后才进生产；观测通道（在线评估）不等于变更通道。
  tags: [rule, tuning, release-gate]

- id: p1-29
  title: Security 是横跨五层的架构责任
  type: principle
  source_chapter: 第 2 章 · 2.1.2
  source_quote: |
    Security 不是只属于调优层的单一组件，而是横跨五层的架构责任。
  summary: |
    业务层定风险与验收边界，构建层实现安全约束与验证逻辑，运行层提供隔离与执行控制，治理层定义身份/策略/审计，调优层通过安全评估、红队和仿真验证有效性。
  tags: [principle, security, cross-cutting]

- id: p1-30
  title: 设计原则一：Model 与 Harness 解耦
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    模型升级可能使某些补偿性 Prompt、工具或循环控制失效，因此模型与 Harness 必须放在一起评估，但不应在实现上强绑定。
  summary: |
    模型是可替换的认知核心，Harness 是与任务/环境/组织相关的工程外壳。评估必须成对进行（换模型可能使旧补偿机制失效），实现上不得强绑定。
  tags: [principle, decoupling, model-harness]

- id: p1-31
  title: 设计原则二：编排层与 Runtime 解耦
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    将两者分开，才能在不重写任务逻辑的前提下切换托管或自托管 Runtime，也能在 Runtime 更换进程、节点或区域时恢复同一任务。
  summary: |
    编排层定义 Agent 如何思考行动（语义决策），Runtime 定义任务在什么资源和故障条件下持续运行（物理保障）；分离后才能互换与恢复。
  tags: [principle, decoupling, runtime]

- id: p1-32
  title: 设计原则三：状态外置，执行实例可替换
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    会话、计划、待办、工具结果、工作区、记忆和制品不应只存在于模型上下文或某个 Runtime 的内存中。持久事件、Checkpoint 和外部工作区是长任务恢复与分布式运行的前提。
  summary: |
    一切任务相关状态外置于上下文窗口与 Runtime 内存之外；执行实例无状态化、可替换，是长任务恢复与分布式运行的前提。
  tags: [principle, state-externalization]

- id: p1-33
  title: 设计原则四：自主性必须与权限和可验证性匹配
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    Agent 获得的工具、网络、数据和凭证越多，潜在的影响半径（Blast Radius）越大。系统应通过最小权限、短期凭证、网络出站控制、Sandbox 隔离、结果校验和高风险动作审批共同建立边界。
  summary: |
    影响半径随工具/网络/数据/凭证增长；边界由六种手段共同建立：最小权限、短期凭证、网络出站控制、Sandbox 隔离、结果校验、高风险动作审批。
  tags: [principle, autonomy, blast-radius]

- id: p1-34
  title: 设计原则五：可观测、安全和评估是运行前提而非上线补丁
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    可观测、安全和评估是运行前提，而不是上线补丁。如果不记录轨迹和环境结果，不但无法定位问题，也无法复现评估或证明版本改进。
  summary: |
    Agent 输出取决于模型、编排、工具、上下文、预算、环境的组合；没有轨迹与环境结果记录，问题定位、评估复现、版本改进证明都不可能。
  tags: [principle, observability, evaluation]

- id: p1-35
  title: 授予的自主性必须有对等边界、证据和验证手段
  type: principle
  source_chapter: 第 2 章 · 2.1.3
  source_quote: |
    凡是被授予的自主性，都必须有与之匹配的边界、证据和验证手段；凡是暂时不需要的能力，可以不建设，但需要明确在什么条件下必须补齐。
  summary: |
    五项原则的总纲：自主性与约束对等；能力可暂缓建设但必须显式记录补齐条件（不能模糊地"以后再说"）。
  tags: [principle, autonomy, conditional-capability]

- id: p1-36
  title: 模型评估五维度清单
  type: checklist
  source_chapter: 第 2 章 · 2.2.1
  source_quote: |
    企业至少需要从五个维度评估模型：对目标领域和任务类型的基础能力；长上下文中的指令遵循、信息筛选和抗干扰能力；工具参数生成、工具结果理解、错误修正和多步调用的稳定性；延迟、价格、上下文成本、并发限制和区域可用性；以及数据保留、隐私、合规、私有化和运营边界。
  summary: |
    前三项决定 Agent 能否完成任务，后两项决定能否在企业内规模化运行。
    模型选择不是一次性厂商选型，而是与任务、Harness 和运行策略相关的持续决策。
  tags: [checklist, model-selection, evaluation-criteria]

- id: p1-37
  title: 路由与降级策略必须与完整 Harness 一起回归评估
  type: rule
  source_chapter: 第 2 章 · 2.2.1
  source_quote: |
    每一条路由和降级策略都会改变 Agent 行为，因此必须与完整 Harness 一起进行回归评估，不能把协议兼容当成能力等价。
  summary: |
    协议层能正常返回工具调用 ≠ 多步任务能力等价（降级模型可能在多步任务中丢失中间约束，产生结构合法却结果错误的执行）。
  tags: [rule, model-routing, regression]

- id: p1-38
  title: 编排层与 Runtime 的接口是任务与状态，不是函数调用细节
  type: rule
  source_chapter: 第 2 章 · 2.2.2
  source_quote: |
    二者的接口应当是任务与状态，而不是函数调用细节；一旦编排层直接依赖某个 Runtime 的内存结构，原则二就已经被破坏。
  summary: |
    分工：编排层负责语义决策（下一步做什么、是否审批、上下文如何重建、任务是否达成）；Runtime 负责物理保障（实例、排队限流、Checkpoint 恢复、资源回收）。
  tags: [rule, interface, runtime]

- id: p1-39
  title: Harness 编排层职责与七项核心能力清单
  type: checklist
  source_chapter: 第 2 章 · 2.2.2（表 2-2）
  source_quote: |
    Harness 编排层是 Agentic Core 的中枢，也是责任边界所在：它决定模型看到什么、能调用什么、什么时候停止，以及结果在交付前需要通过哪些验证。
  summary: |
    七项能力（附缺失时失效模式）：(1) Agent Loop 与终止条件——否则无终止循环/预算失控；(2) Instruction 与 Context Engineering——否则关键信息被淹没；
    (3) Planning 与 Delegation——否则复杂任务失败后无法定位续接；(4) Tool 与 Skill 管理（注册/渐进披露/参数校验/结果截断外置/失败分类）——否则工具描述挤占上下文、工具错误当模型错误；
    (5) Middleware 与 Policy Hook——否则策略只能依赖提示词、模型可绕过；(6) Compaction 与 Continuation——否则上下文耗尽中断或压缩改变任务事实；
    (7) Steering、HITL 与 Verification——否则高风险动作缺监督、"看起来完成"但不可信。
  tags: [checklist, harness, capabilities]

- id: p1-40
  title: 五类信息对象必须区分（Context/State/Memory/Knowledge/Skill）
  type: rule
  source_chapter: 第 2 章 · 2.2.3（表 2-3）
  source_quote: |
    上下文窗口是模型当前能够看到的内容，而 Agentic Application 需要管理的信息远超一个上下文窗口所能容纳的范围。架构上至少应区分五类对象。
  summary: |
    Context：单次调用级、持续重建、不作事实来源；State：任务级、需强一致事实记录与持久化；
    Memory：跨任务/会话、需写入策略/更新/遗忘/隐私治理；Knowledge：与组织内容同步、需权限过滤/时效/来源可追溯；
    Skill：版本化能力资产，可发布、复用、回滚。五者生命周期与一致性要求不同，不可用一个上下文包含一切。
  tags: [rule, information-objects, context-state]

- id: p1-41
  title: Memory 与 Knowledge 分列治理，不放同一向量库
  type: rule
  source_chapter: 第 2 章 · 2.2.3
  source_quote: |
    Memory 由 Agent 在运行中生成，风险在于错误经验被反复沿用，因此需要写入门槛与遗忘机制；Knowledge 来自组织既有内容，风险在于权限越界与信息过期。
  summary: |
    二者写入方式与治理责任不同；放进同一个向量库会同时失去两类治理能力（记忆污染治理与知识权限/时效治理）。
  tags: [rule, memory, knowledge, governance]

- id: p1-42
  title: Context Engineering 的本质是在正确时间选正确信息
  type: principle
  source_chapter: 第 2 章 · 2.2.3
  source_quote: |
    Context Engineering 的本质不是把所有内容塞进更长的窗口，而是在正确的时间选择正确的信息。
  summary: |
    手段：摘要、工具结果卸载、按需检索、Skill 渐进式披露、子 Agent 上下文隔离、文件式交接——把稀缺的模型注意力留给当前决策。
  tags: [principle, context-engineering]

- id: p1-43
  title: 可恢复任务的真实状态必须保存在模型上下文之外
  type: rule
  source_chapter: 第 2 章 · 2.2.3
  source_quote: |
    必须将可恢复任务的真实状态保存在模型上下文之外：如果压缩、截断或模型误读能够改变任务事实，任务就失去了可恢复性，长任务与多智能体协作也无从谈起。
  summary: |
    判别式：任何压缩/截断/误读若能改变任务事实，即违反本规则。这是长任务与多 Agent 协作的前提条件。
  tags: [rule, state, recoverability]

- id: p1-44
  title: 一次受控行动必须经过六个环节
  type: checklist
  source_chapter: 第 2 章 · 2.2.4
  source_quote: |
    能力被发现，不等于能力已被授权；工具参数符合 Schema，不等于工具意图符合用户目标；命令在 Sandbox 中成功运行，也不等于结果可以安全地影响生产系统。
  summary: |
    六环节：能力选择、身份与权限判定、参数校验、隔离环境执行、结果校验、状态与审计记录。
    分布于 Harness 编排层、Gateway、Sandbox 与平台控制面；任一环节缺失，模型建议的动作就直接变成系统执行的事实。
  tags: [checklist, controlled-action, six-steps]

- id: p1-45
  title: 策略集中定义、判定点分布式执行
  type: principle
  source_chapter: 第 2 章 · 2.3.2
  source_quote: |
    控制面不应成为每次模型或工具调用的强同步依赖。一种更稳健的模式是策略集中定义，判定点分布式执行。
  summary: |
    控制面管理策略/版本/发布，Harness Middleware、Gateway、Runtime、Sandbox 在请求路径按已下发策略判定，审计事件异步回送。
    代价是策略传播延迟：高风险策略（凭证吊销、租户封禁）需辅以强制刷新、短期凭证或集中校验兜底。
  tags: [principle, control-plane, policy]

- id: p1-46
  title: Sandbox 只约束技术可达范围，不回答授权与正确性
  type: rule
  source_chapter: 第 2 章 · 2.3.3
  source_quote: |
    Sandbox 是基础隔离，它约束的是行动的技术可达范围，不回答某个动作是否获得授权、是否符合业务意图，以及结果是否正确。
  summary: |
    三个遗留问题分别由工具权限判定、业务审批、结果校验承担；不能因有沙箱而省略三者。Sandbox 需同时控制文件系统、进程、CPU/内存/存储、包安装、网络、Secrets 注入与数据出站。
  tags: [rule, sandbox, boundary]

- id: p1-47
  title: 隔离粒度按风险选择
  type: rule
  source_chapter: 第 2 章 · 2.3.3
  source_quote: |
    隔离粒度应根据风险选择：进程级隔离成本低但边界弱，容器适合大多数通用任务，微虚拟机、独立虚拟机或独立账号适合执行不可信代码和处理高敏感数据的任务。
  summary: |
    三档隔离映射：进程级（低成本弱边界）/容器（通用任务默认）/微虚拟机及以上（不可信代码、高敏感数据）。
  tags: [rule, sandbox, risk-tiering]

- id: p1-48
  title: 状态对象的一致性要求不同，不可同库混存
  type: rule
  source_chapter: 第 2 章 · 2.3.4
  source_quote: |
    把它们全部放入同一个向量库或会话记录中，通常意味着放弃了其中大部分要求。
  summary: |
    Event Log 与 Checkpoint 需强一致与顺序保证；Workspace 需可快照可回滚；Artifact 需可寻址可保留；Memory 与 Knowledge 需可检索可治理。
  tags: [rule, state-store, consistency]

- id: p1-49
  title: AI 网关三层语义（LLM/MCP/Agent）治理粒度必须区分
  type: rule
  source_chapter: 第 2 章 · 2.3.4
  source_quote: |
    三者可以是同一 Gateway 的不同能力，但治理对象和粒度必须区分，否则模型成本、工具权限和任务预算会在同一处策略中互相覆盖。
  summary: |
    LLM Gateway 以模型调用为粒度（协议适配、路由、限流、降级、容灾、Token 成本）；MCP Gateway 以工具调用为粒度（Server Registry、凭证托管、工具级 ACL、审计）；Agent Gateway 以任务为粒度（Agent 路由、会话连续性、租户配额、预算）。
  tags: [rule, gateway, governance-granularity]

- id: p1-50
  title: 差异化资产自建，横切能力平台化
  type: rule
  source_chapter: 第 2 章 · 2.3.5
  source_quote: |
    业务特有的 Prompt、Skill、Tool、评估标准和人机协作流程通常属于差异化资产；模型接入、任务托管、沙箱、状态存储、身份、可观测、评估基础设施和安全策略更适合成为共享平台能力。
  summary: |
    选型顺序：先判断哪些能力构成企业差异化资产，再判断哪些横切能力适合平台化。
  tags: [rule, platform-selection, differentiation]

- id: p1-51
  title: 平台提供安全默认路径，应用保留组合选择权
  type: principle
  source_chapter: 第 2 章 · 2.3.5
  source_quote: |
    平台提供安全且可运营的默认路径，应用保留对模型、Harness 编排和业务能力的组合选择权。
  summary: |
    两个失败方向：平台封装所有差异→退化为最低共同需求、业务绕过自建；平台只提供计算无策略/状态/观测/评估→无法解决真实共性问题。
  tags: [principle, platform, balance]

- id: p1-52
  title: Agent Release 是生命周期最小可复现发布单元（六类要素）
  type: checklist
  source_chapter: 第 2 章 · 2.4.1
  source_quote: |
    生命周期的最小发布单元不应只是一份应用代码，而应是一个可复现的 Agent Release。
  summary: |
    至少绑定六类要素：(1) 模型及版本、参数、推理预算与路由策略；(2) Harness 编排代码、配置、循环控制、Middleware 与终止条件；
    (3) Prompt、Context Policy、Memory Policy、Skill 与 Knowledge 版本；(4) Tool/MCP/Agent 能力清单、权限策略和凭证范围；
    (5) Runtime、Sandbox、资源、网络与数据保留配置；(6) 评估数据集、Evaluator、基线、准入阈值和已知风险。
    只有要素可追踪，才能回答生产任务用了哪个 Release、复现失败、证明变更后的质量提升。
  tags: [checklist, agent-release, reproducibility]

- id: p1-53
  title: 无版本的 Prompt 修改使评估结论失效
  type: rule
  source_chapter: 第 2 章 · 2.4.1
  source_quote: |
    如果 Prompt 可以在生产中随时修改而不留版本，任何评估结论都只对当时那一刻成立。
  summary: |
    可复现评估的前提是发布单元版本化；不可追踪的在线修改使一切回归与对比失去意义。
  tags: [rule, versioning, evaluation]

- id: p1-54
  title: 架构决策是生命周期第一阶段，不可隐含在构建中
  type: principle
  source_chapter: 第 2 章 · 2.4.2
  source_quote: |
    把这些决策留给构建阶段隐式完成，等价于让实现细节反向定义架构约束，而这类问题往往只能通过重构而非补丁解决。
  summary: |
    形态选择、自主性授予、责任域划分、权限边界一旦确定，后续构建/运行/治理/调优的成本区间基本被锁定。
  tags: [principle, architecture-first, lifecycle]

- id: p1-55
  title: 风险来自形态或权限模型时，重新设计架构而非叠加补丁
  type: rule
  source_chapter: 第 2 章 · 2.4.2
  source_quote: |
    当风险来自形态选择或权限模型本身，而不是某个 Prompt 或某次工具调用时，正确的动作是重新设计架构，而不是继续叠加补丁。
  summary: |
    归因决定动作：Prompt/工具调用级问题→打补丁；形态/权限模型级问题→回到架构设计阶段。
  tags: [rule, re-architecture, risk-attribution]

- id: p1-56
  title: 治理落地自检标准
  type: rule
  source_chapter: 第 2 章 · 2.4.3
  source_quote: |
    如果某项治理要求只能在上线后通过流程和人工补齐，而不能在架构和构建阶段落到策略、接口或数据结构上，那么它在规模化之后很可能会失效。
  summary: |
    治理要求必须能落到策略、接口或数据结构上（架构/构建期物化），仅靠上线后流程与人工的治理在规模化后失效。
  tags: [rule, governance, self-check]

- id: p1-57
  title: 调优变更不得绕过构建与准入门禁
  type: rule
  source_chapter: 第 2 章 · 2.4.4
  source_quote: |
    任何由模型、反馈或轨迹生成的 Prompt、Memory、Skill 或 Harness 补丁，都应被视为候选变更，需要经过可复现评估、安全检查、版本化、灰度和可回滚发布之后才能进入生产。
  summary: |
    调优≠在生产中自由修改自己；可控的改进（审批+门禁）优先于无审批的自我修改，这是企业级自进化的定义。运行阶段必须产出结构化 Trace/状态/成本事实支撑该闭环。
  tags: [rule, tuning, release-gate, controlled-evolution]

- id: p1-58
  title: 编排层与模型共同演进，不能一次定型或越复杂越好
  type: rule
  source_chapter: 第 2 章 · 2.2.2
  source_quote: |
    编排层本身是决定 Agent 能力的可评估对象，既不能一次设计定型，也不能默认越复杂越好。
  summary: |
    模型能力增强后，为旧模型缺陷设计的复杂脚手架可能反而限制新模型，需随模型迭代重新评估甚至简化；模型固定时也可通过 Trace 分析、错误聚类、完成前验证和循环检测改进编排。
  tags: [rule, harness, co-evolution]

# ============ 第 3 章 范式：Harness 的主流构建方式和责任边界 ============

- id: p1-59
  title: 四类构建入口不构成成熟度阶梯
  type: principle
  source_chapter: 第 3 章 · 3.7（3.1、3.5.1 重申）
  source_quote: |
    四类构建入口不是成熟度高低关系，也不必互斥。企业应根据任务结构、定制深度、数据边界、环境影响、团队能力和交付方式选择最低充分方案。
  summary: |
    高代码框架、产品化 Harness、基于模型构建（Managed）、云产品四类入口不是由低到高的阶梯，也不互斥（3.5.1：不能按自建、半托管、全托管简单排列为成熟度阶梯）。
    同一企业可混用多类入口，再由 Agent Platform 统一纳管。
  tags: [principle, build-entry, no-maturity-ladder]

- id: p1-60
  title: 构建入口选择四判据
  type: checklist
  source_chapter: 第 3 章 · 3.1.3
  source_quote: |
    选择时应分别判断任务效果是否依赖修改 Loop 或 Context、数据和执行环境能否托管、团队是否愿意维护状态恢复与 Sandbox，以及最终交付对象是个人工作区、嵌入式应用、异步任务服务还是平台内业务 Agent。
  summary: |
    四个判断点：定制深度（是否需改 Loop/Context）、数据边界（环境可否托管）、运行责任（谁维护状态恢复与沙箱）、交付方式（个人工作区/嵌入式应用/异步任务服务/平台内 Agent）。
  tags: [checklist, build-entry, selection-criteria]

- id: p1-61
  title: 路径控制方式判据：Workflow / Agent 主导 / Hybrid
  type: rule
  source_chapter: 第 3 章 · 3.1.3
  source_quote: |
    步骤、分支和异常在设计时已经明确的任务，更适合使用 Workflow；执行路径必须根据中间结果和环境反馈动态决定时，可以由 Agent 持有部分决策权；高风险主流程稳定、局部判断复杂的任务，则可以使用 Hybrid。
  summary: |
    按路径在什么阶段确定来选择控制方式。注意：Workflow/Agent 主导/Hybrid 描述路径控制方式，不是与 Single/Long-Horizon/Multi-Agent 并列的应用形态。
  tags: [rule, path-control, workflow-agent-hybrid]

- id: p1-62
  title: 构建 Agent 先固定任务契约（Agent Contract）
  type: rule
  source_chapter: 第 3 章 · 3.1.2
  source_quote: |
    构建 Agent 不能从选择框架或打开工具开始，而应先固定任务契约（Agent Contract）。
  summary: |
    契约说明：Agent 为谁工作、接受什么输入、交付什么结果、允许影响哪些系统、哪些动作必须拒绝或审批、什么证据能证明任务完成。
    选择构建路径本质上就是决定契约中各对象由企业开发、复用产品还是交给托管平台。
  tags: [rule, agent-contract, contract-first]

- id: p1-63
  title: 契约未授予发布权限，不得把修复完成解释为已发布
  type: rule
  source_chapter: 第 3 章 · 3.1.2
  source_quote: |
    除非契约明确授予发布权限并规定审批条件，这些 Agent 都不应把修复完成解释为已经发布生产。
  summary: |
    完成语义由契约定义：生成报告、隔离环境修改代码、创建合并请求是三种不同的完成级别，不可自行升格。
  tags: [rule, completion-semantics, contract]

- id: p1-64
  title: Harness 八类构建对象清单（企业生产必须有明确责任人）
  type: checklist
  source_chapter: 第 3 章 · 3.1.2（表 3-1）
  source_quote: |
    一个最小 Agent 可以只实现其中一部分，但进入企业生产环境后，这些问题都必须有明确责任人。
  summary: |
    (1) Agent Contract（角色指令、输入输出、成功标准）；(2) Execution（Loop、状态机、预算、Verifier）；
    (3) Context & State（Context Policy、Session/Task Schema、Workspace、Memory）；(4) Capability（Tool Schema、MCP、Skill、Subagent）；
    (5) Environment（Environment Contract、Sandbox、Artifact 边界）；(6) Control（Permission Policy、HITL、短时凭证）；
    (7) Interaction（Event Schema、Streaming、Channel、恢复游标）；(8) Quality（Trace、Outcome、测试用例、评估基线）。
    可归纳为执行与编排、上下文与状态、行动与反馈三个能力域。
  tags: [checklist, build-objects, ownership]

- id: p1-65
  title: Harness / Runtime / Sandbox 责任三分
  type: rule
  source_chapter: 第 3 章 · 3.1.1
  source_quote: |
    Harness 决定下一步应为模型提供什么、允许模型提出什么行动、怎样推进任务；Runtime 负责持续承载这个过程；Sandbox 负责把实际行动限制在可控环境中。三者协同，但责任不同。
  summary: |
    五层责任：Model（能理解推理到什么程度）→Harness 编排（如何工作）→Runtime（如何持续运行）→Sandbox/Environment（在哪里行动、影响多大）→Agent Platform（如何规模化交付治理）。
  tags: [rule, responsibility, layering]

- id: p1-66
  title: 逻辑边界不对应独立产品，但架构设计必须保留边界
  type: rule
  source_chapter: 第 3 章 · 3.1.1
  source_quote: |
    但在架构设计中仍要保留边界，否则企业无法判断故障归属、数据位置、迁移成本和最终责任。
  summary: |
    一个 SDK 可同时含 Harness 与本地 Runtime，托管服务可同时提供三者；产品形态可合并，责任边界不可合并。
  tags: [rule, boundary, ownership]

- id: p1-67
  title: Framework 提供构建材料，不自动补齐生产责任
  type: rule
  source_chapter: 第 3 章 · 3.2
  source_quote: |
    Framework 提供的是构建材料，不会自动补齐多租户隔离、状态恢复、Sandbox、安全策略、评估基线和业务验收。
  summary: |
    高代码框架的核心价值是任务语义控制（Context 组成、重试策略、计划时机、审批触发、完成证据）；
    生产六项责任（多租户隔离、状态恢复、Sandbox、安全策略、评估基线、业务验收）仍归企业。
  tags: [rule, framework, production-responsibility]

- id: p1-68
  title: 不一次性打开所有能力，从任务成功标准反推 Harness
  type: rule
  source_chapter: 第 3 章 · 3.2
  source_quote: |
    接下来不应一次性打开所有能力，而应从任务成功标准反推需要的 Harness。
  summary: |
    示例推导链：需读代码/生成补丁/执行测试/安全扫描→基础工具；计划未经确认不能改代码→Plan Mode + Permission；
    分析与评审可并行→Subagent；任务跨多个调用→外置状态 + 可恢复 Workspace。
  tags: [rule, capability-derivation, minimal-sufficient]

- id: p1-69
  title: 稳定步骤沉淀、判断留给 Loop、副作用过权限与沙箱
  type: rule
  source_chapter: 第 3 章 · 3.2.1
  source_quote: |
    稳定、确定性的步骤应尽量沉淀为 Tool、脚本或策略；需要模型理解目标和权衡方案的部分留在 Agent Loop；可能影响外部世界的动作统一经过权限和 Sandbox。
  summary: |
    三分法使 Harness 具有可测试边界，而不是由 Prompt 驱动的一组隐式行为。
  tags: [rule, deterministic-split, testability]

- id: p1-70
  title: 在线实例不依赖进程内消息历史恢复任务
  type: rule
  source_chapter: 第 3 章 · 3.2.2
  source_quote: |
    在线实例不应依赖进程内消息历史恢复任务。应用团队需要把 Session、Task、Plan、子任务和 Artifact 映射到共享状态接口。
  summary: |
    配套要求：为同一任务设置并发写入或执行租约；Worker 失效后从安全点恢复；按租户创建/复用 Sandbox；使用短时身份访问企业工具。
    物理存储与容灾属 Runtime，但 Harness 必须先定义逻辑契约。
  tags: [rule, state-externalization, distributed]

- id: p1-71
  title: Session Store 只负责 Session Transcript
  type: rule
  source_chapter: 第 3 章 · 3.3.2
  source_quote: |
    但它只负责 Session Transcript，不等于完整的企业任务存储，也不保存认证状态、应用配置、文件 Checkpoint 或保留策略。
  summary: |
    外部 Session Store 支持跨主机 session_id 续接；Task State、Artifact、Sandbox Snapshot、凭证、业务 Outcome 仍需分别管理。
    共享 Session Store 必须保证租户与项目键隔离、同一键追加顺序、幂等写入与并发控制。
  tags: [rule, session-store, boundary]

- id: p1-72
  title: Worker 失效后先确认原行动，再决定恢复方式
  type: rule
  source_chapter: 第 3 章 · 3.3.2
  source_quote: |
    Worker 失效后，任务服务先确认原行动和工作区状态，再决定 Resume、Retry 或转人工，不能因为 Session 能被读取就直接重放最后一次工具调用。
  summary: |
    恢复前核对：幂等键、外部系统状态、已生成 Artifact；可读≠可重放。
  tags: [rule, recovery, idempotency]

- id: p1-73
  title: 功能可用不等于生产责任已满足
  type: rule
  source_chapter: 第 3 章 · 3.3.3
  source_quote: |
    官方文档明确提示后台 Subagent 不可恢复，Sandbox 在未启用约束时不会自动提供隔离，因此不能把功能可用直接等同于生产责任已经满足。
  summary: |
    接入产品化 Harness/工作区助手时须逐项确认并发控制、恢复、保留、审计语义；共享部署不能解释为强隔离平台（如 QwenPaw Hub 明确不构成对陌生用户的强多租户边界）。
  tags: [rule, product-liability, production-readiness]

- id: p1-74
  title: 平台不能替企业定义业务正确性
  type: rule
  source_chapter: 第 3 章 · 3.4.3
  source_quote: |
    平台可以推进基础 Harness、执行工具并产生 Event，但不能替企业定义业务正确性。
  summary: |
    平台能运行测试、创建变更文件；是否允许发布生产仍取决于企业审批；Agent 声称修复完成，仍需企业依据安全扫描、测试和真实系统状态验收。
  tags: [rule, managed-agent, business-correctness]

- id: p1-75
  title: 托管越多，越要把工具、数据、权限和成功标准定义清楚
  type: rule
  source_chapter: 第 3 章 · 3.4.4
  source_quote: |
    托管越多，企业越应把工具、数据、权限和成功标准定义清楚，否则只是把不明确的 Agent 行为转移到了云端。
  summary: |
    托管转移的是执行基础，不是定义责任；定义缺位时托管只是把模糊行为搬家。
  tags: [rule, managed-agent, responsibility]

- id: p1-76
  title: Managed Agents 的开发重点是机器可执行契约
  type: principle
  source_chapter: 第 3 章 · 3.4.3
  source_quote: |
    Managed Agents 的开发重点是把责任边界写成机器可执行契约。
  summary: |
    契约内容：Agent 能看见什么工具、Environment 能访问什么资源、凭证以谁的身份发放、哪些事件需人工参与、什么 Outcome 才能使企业 Task 完成。
  tags: [principle, managed-agent, contract]

- id: p1-77
  title: 云产品快速构建不等于用可视化页面替代工程设计
  type: rule
  source_chapter: 第 3 章 · 3.5.1
  source_quote: |
    云产品快速构建不等于用可视化页面替代工程设计。产品可以预置模型访问、能力目录、运行环境、身份策略、观测和评估入口，但业务目标、任务状态、数据边界、工具授权和 Outcome 标准仍由企业定义。
  summary: |
    即使 Agent 在平台内配置运行，也不能据此假定已满足多租户隔离、长任务恢复、业务审批或生产准入，需按产品版本与部署方式逐项确认。
  tags: [rule, cloud-product, engineering]

- id: p1-78
  title: 从任务契约反推资源组合，不先堆砌全部能力
  type: rule
  source_chapter: 第 3 章 · 3.5.2
  source_quote: |
    企业在云产品中构建 Agent 时，应先从任务契约反推资源组合，而不是先把所有可用模型、知识和工具都加入 Agent。
  summary: |
    反向推导避免能力堆砌；与 3.2"从成功标准反推 Harness"同一原则在云产品路径的表述。
  tags: [rule, resource-composition, contract-first]

- id: p1-79
  title: 平台发布状态不等于业务任务完成
  type: rule
  source_chapter: 第 3 章 · 3.5.3
  source_quote: |
    平台侧的发布或部署状态只说明某个 Agent 定义已经可以被调用，不等于业务任务已经完成。
  summary: |
    企业需建立业务 Task 与平台执行对象的映射，把 Event、Trace、Artifact 关联到具体版本，由业务应用或授权 Verifier 依真实系统状态形成 Outcome。
  tags: [rule, outcome, completion-semantics]

- id: p1-80
  title: 多源 Agent 统一管理的三个必要条件
  type: checklist
  source_chapter: 第 3 章 · 3.6.2
  source_quote: |
    首先，版本与归属必须可追溯，不能只记录一个 Agent 名称……其次，Event、Artifact 和 Trace 必须关联 Task、Identity 与 Tenant……最后，远程 Agent 不能只返回自然语言结论，还应提供可验收的 Artifact、事件或环境事实。
  summary: |
    (1) 版本与归属可追溯（实际模型、Harness 配置、能力、环境版本）；(2) 执行证据关联 Task/Identity/Tenant（支持审计与成本归因）；
    (3) 远程 Agent 返回可验收的 Artifact/事件/环境事实（否则无法进入统一质量闭环）。
  tags: [checklist, platform, multi-source-agents]

- id: p1-81
  title: 平台统一公共对象与接入契约，不抹平 Harness 实现差异
  type: principle
  source_chapter: 第 3 章 · 3.6.2（3.6.1、3.7 重申）
  source_quote: |
    Agent Platform 应统一公共对象和接入契约，而不是抹平 Harness 实现差异。
  summary: |
    统一的是目录、身份、资源、任务、运行和质量事实（Agent Definition、Task、Session、Event、Identity、State、Checkpoint、Artifact、Trace、Outcome）；
    不要求外部 Agent 改用同一种 Harness，也不替业务应用定义任务目的和正确性。
  tags: [principle, platform, unification-boundary]

- id: p1-82
  title: 无论入口如何，四组概念必须区分且全部版本化
  type: rule
  source_chapter: 第 3 章 · 3.7
  source_quote: |
    无论选择哪种入口，都要区分 Task 与 Session、Event 与 Trace、Artifact 与 Outcome，把权限与验证落实为确定性机制，并让模型、Harness 配置、能力、环境、策略和评估基线共同受版本控制。
  summary: |
    四组易混概念的强制区分 + 权限与验证的确定性机制 + 六类要素共同版本化，是所有构建入口的共性交付规则。
  tags: [rule, versioning, concept-distinctions]

# ============ 第 4 章 任务：编排、长程推进与协作流转 ============

- id: p1-83
  title: 消息历史不是任务状态的唯一来源，必须维护机读权威状态
  type: rule
  source_chapter: 第 4 章 · 4.1.2
  source_quote: |
    消息历史记录了模型和用户曾经交换的内容，却不应成为任务状态的唯一来源。企业 Harness 至少要维护一份可机读的权威状态。
  summary: |
    权威状态至少包含：目标、当前阶段、Plan 与 Todo、已确认事实、阻塞项、子任务、Artifact、剩余预算、等待原因、完成依据。
    模型看到的是由权威状态生成的当前任务视图，而不是从历史消息中猜测进度。
  tags: [rule, authoritative-state, task-state]

- id: p1-84
  title: 任务状态显式化（十状态机清单）
  type: checklist
  source_chapter: 第 4 章 · 4.1.2
  source_quote: |
    WAITING 不是失败，PAUSED 也不是结束。只有把这些状态显式化，上层 Runtime 才能在等待期间释放计算资源并准确恢复。
  summary: |
    十状态：CREATED / RUNNING / WAITING_INPUT / WAITING_APPROVAL / WAITING_EVENT / PAUSED / VERIFYING / COMPLETED / FAILED / CANCELLED，各有允许的下一步。
    显式化的三方收益：Runtime 可释放资源并准确恢复；界面能说明 Agent 在等什么；观测量能区分执行慢、审批慢、工具慢。
  tags: [checklist, state-machine, task-states]

- id: p1-85
  title: Loop 必须有外部终止边界，多维预算进入约束
  type: rule
  source_chapter: 第 4 章 · 4.1.2
  source_quote: |
    Loop 还必须有外部终止边界。步骤数、总时长、Token 与费用、工具调用次数、子任务并发数、高风险动作次数都应进入预算。
  summary: |
    预算维度清单：步骤数、总时长、Token、费用、工具调用次数、子任务并发数、高风险动作次数。
    接近阈值：收敛范围、停止新委派、优先完成可交付部分或请求用户选择；预算耗尽：产生明确终态与未完成清单，而不是悄然截断。
  tags: [rule, budget, termination]

- id: p1-86
  title: 模型只能申请完成，Harness 依据事实提交完成
  type: principle
  source_chapter: 第 4 章 · 4.6.2（4.1.1、4.1.3 重申）
  source_quote: |
    模型只能提出完成申请，Harness 才能提交完成状态。
  summary: |
    Verify 不接受"我已经完成"作为唯一依据；模型输出"修复已完成"时状态只能进入 VERIFYING，验收器补齐证据才能 COMPLETED。
    这是第 4 章完成验证的核心原则，跨场景稳定（变化的是 Verifier，稳定的是该原则）。
  tags: [principle, completion, verification]

- id: p1-87
  title: Planning 的价值是把任务结构外部化为控制对象
  type: principle
  source_chapter: 第 4 章 · 4.2.1
  source_quote: |
    Planning 的价值不是展示模型隐藏的思考过程，而是把任务结构外部化为 Harness 和用户都能读取、修改和验证的控制对象。
  summary: |
    外部化内容：阶段目标、依赖、正在执行的步骤、完成判定证据。计划可供 Harness 与用户双向读取和验证。
  tags: [principle, planning, externalization]

- id: p1-88
  title: 三种计划控制方式及适用条件
  type: checklist
  source_chapter: 第 4 章 · 4.2.1（表）
  source_quote: |
    根据任务复杂度，Harness 可以使用三种控制方式。
  summary: |
    轻量 Todo（有序待办列表）：目标明确、步骤少、反馈快；
    Plan Mode（只读探索→形成计划→确认后执行）：影响面较大、需要审阅、环境尚不清楚；
    Planner–Executor（Planner 维护阶段和依赖，Executor 逐项执行）：长任务、多依赖、可并行或需要专业角色。
  tags: [checklist, planning-modes, selection]

- id: p1-89
  title: 计划必须允许修订，且不得改写目标掩盖失败
  type: rule
  source_chapter: 第 4 章 · 4.2.1
  source_quote: |
    每次重规划都应说明触发事实并保留已完成项，不能通过改写目标掩盖失败。
  summary: |
    重规划触发源：工具结果推翻假设、用户改变目标、环境暴露新约束。每次重规划需携带触发事实，历史完成项保留可审计。
  tags: [rule, replanning, auditability]

- id: p1-90
  title: Todo 只记录改变可交付状态的事项
  type: rule
  source_chapter: 第 4 章 · 4.2.1
  source_quote: |
    Todo 则不需要记录每次微小工具调用，只记录会改变任务可交付状态的事项，并保持唯一的当前进行项或明确的并行分组。
  summary: |
    Todo 粒度规则：可交付状态变化级；同时约束当前进行项唯一（或有明确并行分组），防止执行焦点漂移。
  tags: [rule, todo, granularity]

- id: p1-91
  title: 阶段门禁三问：输出是什么、证据在哪里、谁来确认
  type: rule
  source_chapter: 第 4 章 · 4.2.2
  source_quote: |
    有效的阶段描述必须回答"输出是什么、证据在哪里、谁来确认"，而不是只写"分析问题""处理代码""确保质量"。
  summary: |
    长任务不应直到最后才验证——阶段门禁既降低错误方向上的继续投入，也为 Context 压缩、人工接管和跨窗口续行提供稳定边界。
  tags: [rule, stage-gate, verification]

- id: p1-92
  title: Prompt 约束不等于 Harness 约束
  type: rule
  source_chapter: 第 4 章 · 4.2.3
  source_quote: |
    Prompt 可以要求模型"先规划再修改"，但只有权限模式、工具白名单、持久状态与 HITL 共同生效，系统才真正具备"计划获批前不可写"的约束。
  summary: |
    行为约束必须由权限模式、工具白名单、持久状态、HITL 等确定性机制保证；仅写入 Prompt 的约束不是约束。
    Plan Mode 的四段式：只读探索→写入计划→人工确认（plan_exit 触发）→进入执行。
  tags: [rule, prompt-vs-harness, determinism]

- id: p1-93
  title: Subagent 委派的三种价值与启用门槛
  type: rule
  source_chapter: 第 4 章 · 4.3.1
  source_quote: |
    只有隔离、专业化或并行收益超过这些成本时，Subagent 才有价值。
  summary: |
    三价值：上下文隔离（子任务只加载相关内容）、能力隔离（不同模型/指令/工具/权限）、并行执行（缩短墙钟时间）。
    反条件：任务很短、步骤高度依赖或共享对象频繁变化时，委派只增加通信和合并成本。
  tags: [rule, subagent, delegation-criteria]

- id: p1-94
  title: 子任务委派契约七项清单
  type: checklist
  source_chapter: 第 4 章 · 4.3.2（表）
  source_quote: |
    每个子任务都应携带可机读契约。
  summary: |
    (1) 目标与边界：交付什么，哪些目录/系统/动作在范围内；(2) 已知上下文：已确认事实、不可自行改变的决定；
    (3) 能力与权限：可用模型、Skill、Tool、环境、权限；(4) 预算：最大时间、步骤、Token、费用、并发；
    (5) 输出与证据：结果结构、证据来源；(6) 失败语义：何时重试、部分返回、升级或终止；(7) 验收条件：父 Agent 判断结果可采用的依据。
  tags: [checklist, delegation-contract, subagent]

- id: p1-95
  title: 主 Agent 不把"任务完成"的责任交出去
  type: rule
  source_chapter: 第 4 章 · 4.3.2
  source_quote: |
    主 Agent 负责全局目标、计划、预算、依赖和最终结果，不应把"任务完成"的责任一并交出去。
  summary: |
    研究型 Subagent 收集事实与候选、执行型在限定范围内产生变更、评审型用独立上下文找缺口——均为运行时职责而非永久角色，最终整合与验收归父任务。
  tags: [rule, delegation, accountability]

- id: p1-96
  title: Delegation 与 Handoff 是两种不同的责任转移
  type: rule
  source_chapter: 第 4 章 · 4.3.2
  source_quote: |
    Delegation 是父任务保留责任，将有边界的子任务委派出去；结果返回后仍由父 Agent 整合和验收。Handoff 则是任务控制权发生转移，接收者成为当前责任人。
  summary: |
    共同要求：二者都不能只通过一条自然语言消息实现，至少要有任务关系、状态与责任变更记录。Handoff 还需传递目标、状态和恢复位置。
  tags: [rule, delegation-vs-handoff, accountability]

- id: p1-97
  title: 子代理默认不继承父任务的全部上下文和权限
  type: rule
  source_chapter: 第 4 章 · 4.3.3
  source_quote: |
    子代理默认不应继承父任务的全部上下文和权限；父任务有权委派，并不意味着子 Agent 自动获得同等授权。
  summary: |
    委派权不等于授权传递；子代理的能力边界由其自身规格（工作区模式、步数、工具白名单）决定。
  tags: [rule, subagent, least-privilege]

- id: p1-98
  title: 多执行者共写工作区必须用机制防冲突
  type: rule
  source_chapter: 第 4 章 · 4.3.3
  source_quote: |
    多个执行者若同时修改同一工作区，应使用隔离分支、对象级锁或补丁合并，不能依赖"大家小心不要冲突"。
  summary: |
    并发写控制必须落实为机制（隔离分支/对象级锁/补丁合并）；评审必须读取固定差异和测试结果，不与执行者共享未提交的中间判断。
  tags: [rule, concurrency, workspace]

- id: p1-99
  title: 子任务失败策略四型
  type: checklist
  source_chapter: 第 4 章 · 4.3.4
  source_quote: |
    父任务需要显式定义子任务失败策略：关键分析失败时 FAIL_FAST；非关键探索可以 BEST_EFFORT；瞬时故障可以 RETRY_OR_REASSIGN；需要业务决定时进入 ESCALATE。
  summary: |
    失败策略须在委派前显式定义，四种基本型按子任务关键性与故障性质选择；合并结果时还要校验输入版本和证据时间，避免采用基于旧代码/旧业务状态的结论。
  tags: [checklist, failure-policy, subagent]

- id: p1-100
  title: Subagent 的产物是新 Observation，验收后才入权威状态
  type: rule
  source_chapter: 第 4 章 · 4.3.4
  source_quote: |
    Subagent 的产物不是主 Agent 可以直接复述的"答案"，而是新的 Observation。只有经过 Schema 校验、版本检查和父任务验收后，才能进入权威任务状态。
  summary: |
    三道关口：Schema 校验、版本检查、父任务验收；防止子任务输出未经检验直接污染任务事实。
  tags: [rule, observation, validation]

- id: p1-101
  title: 任务身份必须与当前连接分离
  type: rule
  source_chapter: 第 4 章 · 4.4.1
  source_quote: |
    Harness 必须把任务身份与当前连接分离：调用方提交任务后获得稳定 task_id，可以持续消费事件，也可以断开。
  summary: |
    任务进入等待或后台运行时 Runtime 可释放计算资源；条件满足后从权威状态恢复，不依赖原进程存活。这是异步与长程续行的前提。
  tags: [rule, task-identity, async]

- id: p1-102
  title: 后台任务、事件与恢复共用一套契约字段
  type: checklist
  source_chapter: 第 4 章 · 4.4.1
  source_quote: |
    后台任务、事件和恢复应共用一套契约：稳定任务 ID、父任务 ID、当前状态、创建者与执行者、输入和 Artifact 引用、事件序号、超时、取消与幂等语义、结果位置、错误分类，以及恢复所需的 Continuation。
  summary: |
    十二字段模板：任务 ID、父任务 ID、状态、创建者/执行者、输入与 Artifact 引用、事件序号、超时、取消与幂等语义、结果位置、错误分类、Continuation。
  tags: [checklist, async-contract, template]

- id: p1-103
  title: 恢复前的重检查清单
  type: checklist
  source_chapter: 第 4 章 · 4.4.1
  source_quote: |
    恢复时重新检查目标、外部条件、工具是否实际执行、权限是否仍有效、工作区是否变化以及剩余预算。
  summary: |
    六项重检查：目标、外部条件、工具实际执行情况、权限有效性、工作区变化、剩余预算。
    暂停前则应：停止创建新行动、处理可中断操作、保存最新状态。
  tags: [checklist, recovery, resume]

- id: p1-104
  title: 取消沿父子任务传播，副作用不能假装消失
  type: rule
  source_chapter: 第 4 章 · 4.4.1
  source_quote: |
    取消需要沿父子任务传播，但已发生的外部副作用不能假装消失，必须保留事实并在必要时执行补偿。
  summary: |
    取消语义=传播 + 事实保留 + 必要时补偿；不可把取消实现为简单丢弃状态。
  tags: [rule, cancellation, side-effects]

- id: p1-105
  title: 稳定内核 + 可插拔能力
  type: principle
  source_chapter: 第 4 章 · 4.5.1
  source_quote: |
    更稳健的结构是"稳定内核 + 可插拔能力"：核心 Loop 只定义阶段与状态迁移；Middleware、Hook 或 Ability 在明确的生命周期点读取 Runtime Context，返回放行、修改、短路或追加行为。
  summary: |
    反模式：每加一个能力就在 Loop 里加一组条件分支，长期使状态迁移不可预测。核心 Loop 保持稳定，能力经扩展点插入。
  tags: [principle, stable-core, extensibility]

- id: p1-106
  title: Middleware 扩展时机与禁区对照表
  type: checklist
  source_chapter: 第 4 章 · 4.5.1（表）
  source_quote: |
    一个常见演进问题是：每加入压缩、计划、权限、模型路由或观测能力，就在 Loop 中增加一组条件分支。短期直接，长期会使状态迁移不可预测。
  summary: |
    六个扩展时机及禁区：Task 创建前后（可加任务分类/初始预算/版本绑定；禁隐式改变用户目标）；Prepare 前后（计划提醒/Context 请求/模型路由；禁把持久状态只写进 Prompt）；
    Model 调用前后（参数策略/输出解析/无进展检测；禁记录不应保留的敏感推理内容）；Action 前后（预算检查/结果标准化；禁绕过统一 Action Plane）；
    State 变化前后（状态校验/Checkpoint/通知；禁多处各自维护权威状态）；Verify 前后（选择验收器/生成缺口/质量门禁；禁仅凭模型自述标记完成）。
    扩展本身也需要顺序、读写范围、冲突规则、失败语义和可观测性。
  tags: [checklist, middleware, extension-points]

- id: p1-107
  title: 扩展应返回结构化 Decision/Patch，由核心 Loop 统一提交
  type: rule
  source_chapter: 第 4 章 · 4.5.1
  source_quote: |
    最好让扩展返回结构化 Decision 或 Patch，由核心 Loop 统一提交，而不是任意修改共享对象。
  summary: |
    中间件不直接改共享状态，只返回结构化决策/补丁；提交权收敛于核心 Loop，保证状态迁移可预测。
  tags: [rule, middleware, structured-decision]

- id: p1-108
  title: 执行内核的四类确定性测试
  type: checklist
  source_chapter: 第 4 章 · 4.5.2
  source_quote: |
    Harness 测试不能只看最终回答。执行内核至少需要四类确定性测试。
  summary: |
    (1) 状态迁移测试：每个状态只接受合法事件，暂停/取消/失败正确传播；(2) 扩展顺序测试：Middleware 在确定时机运行，冲突与短路行为稳定；
    (3) 恢复测试：任意安全点中断后任务可重建且不重复副作用；(4) 预算与边界测试：达到阈值后 Loop 按设计收敛。
    模型输出可用固定样本、录制回放或模拟器替代；真实模型端到端效果归 Evaluation。
  tags: [checklist, testing, determinism]

- id: p1-109
  title: 恢复后先查询原任务，不重复执行
  type: rule
  source_chapter: 第 4 章 · 4.5.2
  source_quote: |
    恢复后应先查询原测试任务，而不是再次修改文件或重复启动发布。
  summary: |
    恢复测试场景（补丁已写入但测试结果未返回时强制中断）验证的规则：恢复动作从查证开始，不从重放开始。
  tags: [rule, recovery, idempotency]

- id: p1-110
  title: 失败类型决定恢复策略，盲目重试禁止
  type: checklist
  source_chapter: 第 4 章 · 4.6.1
  source_quote: |
    "Agent 失败"不是一个可执行的诊断。瞬时模型或网络错误适合有界退避；参数错误需要修正；环境缺失需要重建；权限拒绝应等待或终止；重复探索需要重新规划；业务条件不满足则要明确缺口。
  summary: |
    六类失败→六种处置：瞬时错误→有界退避；参数错误→修正；环境缺失→重建；权限拒绝→等待或终止；重复探索→重新规划；业务条件不满足→明确缺口。
    总则：盲目重试只会增加成本和风险。
  tags: [checklist, failure-handling, recovery-strategy]

- id: p1-111
  title: 有副作用的行动重试前必须先证明未发生
  type: rule
  source_chapter: 第 4 章 · 4.6.1
  source_quote: |
    有副作用的行动在重试前必须先回答"上一次究竟有没有发生"……Harness 应生成幂等键，记录请求与结果，并优先查询状态；无法证明未执行时，不应直接重复。
  summary: |
    适用对象：发布、通知、写数据库等操作——超时可能只是响应丢失。降级路径（切换模型/工具/环境）也要被记录，因为能力、权限和结果质量可能已变。
  tags: [rule, idempotency, side-effects]

- id: p1-112
  title: 无进展检测信号清单
  type: checklist
  source_chapter: 第 4 章 · 4.6.1
  source_quote: |
    无进展检测比单纯的最大步数更早发现问题。常见信号包括：连续调用相同工具且参数高度相似、反复得到同一错误、Plan 长时间不变、工作区没有新增事实、模型在少数行动间循环。
  summary: |
    五个信号：同工具同参数重复、同一错误反复、Plan 长期不变、工作区无新增事实、少数行动间循环。
    处置阶梯：先要求模型依结构化证据重新规划→缩小任务→切换能力→创建独立评审→转人工。
  tags: [checklist, no-progress-detection, loop-detection]

- id: p1-113
  title: 验证强度与风险匹配（五级验证层级）
  type: checklist
  source_chapter: 第 4 章 · 4.6.2（表）
  source_quote: |
    模型只能提出完成申请，Harness 才能提交完成状态。验证强度应与风险匹配。
  summary: |
    五级：结构验证（Schema/必填/格式/文件存在，低风险结构化产物）→环境验证（查真实系统、文件差异，工具与工作区任务）→确定性验证（测试/规则/静态检查/业务校验，可编码成功条件）→独立模型验证（独立 Context 查质量遗漏，开放式分析）→人工验收（责任人审阅签署，高影响/主观/合规任务）。
    Verifier 应返回结构化缺口、失败证据和可修复性。
  tags: [checklist, verification-levels, risk-tiering]

- id: p1-114
  title: 验证失败不是结束，而是新的 Observation
  type: rule
  source_chapter: 第 4 章 · 4.6.2
  source_quote: |
    验证失败不是简单结束，而是新的 Observation：Harness 决定继续修复、重新规划、转交还是失败。
  summary: |
    验证结果回流任务循环继续驱动决策；Verifier 是单次任务的完成门禁，跨版本比较属于 Evaluation Harness（第 6 章），二者职责不同。
  tags: [rule, verification, feedback-loop]

- id: p1-115
  title: 完成语义必须与契约目标范围一致
  type: rule
  source_chapter: 第 4 章 · 4.6.3
  source_quote: |
    如果目标只到"形成可审批变更"，系统不得把"尚未发布"误判为未完成；如果目标包含发布，则必须进一步核验审批和真实部署状态。
  summary: |
    完成判定依据契约定义的目标边界：目标范围不同，同一状态既可能是完成也可能是未完成；发布型目标需核验审批与真实部署状态。
  tags: [rule, completion-semantics, contract]

- id: p1-116
  title: 完成结果是一组目标相关事实而非一句"已完成"（案例证据清单）
  type: checklist
  source_chapter: 第 4 章 · 4.6.3
  source_quote: |
    对贯穿案例而言，以下事实必须同时成立：依赖清单证明受影响版本已经被替换，且没有通过传递依赖重新引入。
  summary: |
    漏洞修复完成的五组并行事实：依赖替换且无传递重引入；代码差异仅落授权目录且与批准计划一致；单测/集成/安全扫描有可寻址报告且强制项全过；独立评审无未关闭阻断问题；变更单含影响范围、回滚方案、证据引用。
    可迁移结构：合同审查（条款覆盖/来源/签署）、数据修复（影响行数/抽样校验/回滚点）、客户工单（系统状态/沟通记录/用户确认）。
  tags: [checklist, completion-evidence, facts]

- id: p1-117
  title: 平台统一纳管不等于共享完成语义
  type: rule
  source_chapter: 第 4 章 · 4.6.4
  source_quote: |
    平台统一纳管多个 Agent，也不意味着这些 Agent 天然共享同一父子任务语义或完成标准。
  summary: |
    企业仍需维护 Task 标识、状态版本、幂等键、审批条件和 Artifact 引用，把平台可观测数据映射到任务契约；业务完成责任由企业应用定义和承担。
  tags: [rule, platform, completion-semantics]

# ============ 第 5 章 信息：上下文、状态与可复用能力资产 ============

- id: p1-118
  title: System Context 必须动态编译，不是仓库里的长字符串
  type: principle
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    生产级 Agent 的 System Prompt 不应只是仓库中的一个长字符串。模型每轮实际接收到的 System Context，需要根据 Agent 版本、当前任务阶段、用户身份、工作区规则、剩余预算、可用工具和选中的 Skill 动态生成。
  summary: |
    角色类似一次"编译"：多来源按优先级合并、冲突处理、超预算内容压缩或移除，得到本轮可执行模型视图。
    分层模板：System Context = Platform Policy / Agent Contract / Tenant-Project Rule / Runtime Reminder / Selected Skill / Tool Descriptors；
    Task Context = User Goal 与 Steering / Plan 与 Todo / Recent Interaction / Compacted History / Retrieved Memory / Retrieved Knowledge / Workspace References。
  tags: [principle, context-compilation, system-context]

- id: p1-119
  title: 上下文分层优先级不可越级覆盖
  type: rule
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    平台策略不能被项目文档覆盖；用户最新要求可以改变任务方向，却不能突破企业安全边界；Memory 中的历史偏好不能替代当前业务事实；工具返回的外部内容也不能自动升级为系统指令。
  summary: |
    四条覆盖禁令：项目文档<平台策略；用户指令<安全边界；历史偏好<当前业务事实；外部内容<系统指令。
    若所有内容拼成同一层文本，Harness 无法判断冲突来源，也无法独立版本和回归。
  tags: [rule, context-layering, precedence]

- id: p1-120
  title: Context Builder 先做权限过滤，再做相关性排序
  type: rule
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    管线应先做身份和权限过滤，再做相关性排序，不能为了排序方便先把跨租户内容交给检索器或模型。
  summary: |
    确定性管线顺序：读取 Task State→解析身份与作用域→收集候选→权限与可信度过滤→相关性排序去重→分配 Token 预算→压缩截断引用化→按层级编译→生成 Manifest。顺序不可调换。
  tags: [rule, context-pipeline, permission-first]

- id: p1-121
  title: 外部内容必须保留来源与信任等级
  type: rule
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    对于外部内容，还要保留来源和信任等级，避免检索到的文档或网页把自身文本伪装成高优先级指令。
  summary: |
    检索到的文档/网页/工具返回内容不得自动获得高优先级；信任等级（平台规则/企业事实/用户输入/外部内容）随内容进入管线。
  tags: [rule, trust-level, prompt-injection]

- id: p1-122
  title: Context 优先级函数七要素
  type: checklist
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    Context 构建不能只按相似度排序。一个实用的优先级函数通常同时考虑：约束强度……任务相关性……时间有效性……来源可信度……执行依赖……信息增量……Token 成本。
  summary: |
    七要素：(1) 约束强度（平台政策与明确业务规则高于经验性建议）；(2) 任务相关性（是否直接影响当前阶段判断行动）；
    (3) 时间有效性（当前环境事实高于过期历史结论）；(4) 来源可信度（权威系统事实高于未确认模型摘要）；
    (5) 执行依赖（即将调用的工具说明与验收条件优先保留）；(6) 信息增量（重复信息合并或移除）；(7) Token 成本（同等价值优先紧凑可引用表达）。
    缺失条件：原文未给出各要素具体权重，需按场景自定。
  tags: [checklist, context-priority, ranking]

- id: p1-123
  title: 窗口划分为 Token 预算区，预算非静态百分比
  type: rule
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    Harness 可以把可用窗口划分为若干预算区，例如为不可覆盖规则、当前目标和状态保留固定下限，为最近交互、检索知识、Skill 和工具 Schema 分配动态额度，并预留模型输出与后续 Observation 空间。
  summary: |
    预算区结构：不可覆盖规则+当前目标状态（固定下限）｜最近交互/检索知识/Skill/工具 Schema（动态额度）｜模型输出与后续 Observation（预留）。
    动态调整：工具密集阶段工具定义与环境状态权重上升；最终综合阶段证据与验收条件更重要。原文未给具体比例数值。
  tags: [rule, token-budget, context-planning]

- id: p1-124
  title: 每次模型调用生成 Context Manifest
  type: template
  source_chapter: 第 5 章 · 5.1（表）
  source_quote: |
    每次模型调用都应生成一份 Context Manifest，记录模型究竟看到了什么，而不只是保存最终拼接文本。
  summary: |
    字段模板：source_type / source_id、scope（Global/Tenant/Project/User/Session/Task）、version、trust_level、permission_basis（本轮为何有权读取）、selected_reason（规则命中/阶段/检索相关/显式引用）、token_count、transform（原文/摘要/截断/去重/引用化）、content_hash（回放与变更检测）。
    用途：开发解释遗漏、评估比较版本差异、安全审计敏感内容来源。只记 Prompt 文本无法完成这些任务。
  tags: [template, context-manifest, observability]

- id: p1-125
  title: 上下文策略统一定义为 Context Policy 并绑定 Agent 版本
  type: rule
  source_chapter: 第 5 章 · 5.1
  source_quote: |
    企业应把上下文层级、检索范围、预算分配、压缩阈值、工具披露和敏感内容处理统一定义为 Context Policy，并与可运行的 Agent 版本绑定。
  summary: |
    Context Policy 决定模型、Prompt、Skill、Knowledge 索引变化后如何组合；把"偶然在某次调用中看见了什么"转化为可测试、可回放的工程行为。
  tags: [rule, context-policy, versioning]

- id: p1-126
  title: 活动上下文的「最近」不只按时间定义
  type: rule
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    "最近"不只按时间定义。用户对目标的最新修改、尚未解决的工具错误、待审批动作和验收失败证据，即使产生得更早，也应被视为活动状态。
  summary: |
    活动状态的判定是任务相关性而非时间近因；已完成且可由 Artifact 证明的探索过程可以退出活动窗口。
    Active Context 结构：稳定目标与约束/当前任务状态/最近逐字交互/结构化历史摘要/检索事实/Artifact 引用。
  tags: [rule, active-context, recency]

- id: p1-127
  title: 对话压缩必须保留的八类信息
  type: checklist
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    对话压缩不是普通摘要。它要支持下一轮继续执行，因此至少保留：原始目标、成功标准和不可变约束。
  summary: |
    八类必保信息：(1) 原始目标、成功标准、不可变约束；(2) 已确认事实及来源（区分事实/假设/模型建议）；(3) 已做决定、原因、被否决方案；
    (4) 已执行行动、工具结果与副作用；(5) 当前 Plan、Todo、阻塞、下一步；(6) Artifact、工作区路径、外部对象 ID；
    (7) 用户偏好、审批结果、权限模式；(8) 失败尝试及避免重复的原因。
    摘要须采用结构化 Schema，携带覆盖事件范围、生成版本和来源引用。
  tags: [checklist, compaction, retention]

- id: p1-128
  title: 高风险事实确定性提取，模型只压缩叙述性内容
  type: rule
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    高风险事实不能只由模型自由归纳，最好从 Task State、工具结果和审批记录中确定性提取，再让模型压缩叙述性内容。
  summary: |
    压缩的分工规则：事实层由确定性提取保证准确，叙述层才交给模型压缩。
  tags: [rule, compaction, determinism]

- id: p1-129
  title: 大工具结果卸载到外部，窗口只留引用
  type: rule
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    大结果不应反复进入每轮 Context。Harness 可以将原始结果写入 Workspace 或 Artifact Store，只保留结果摘要、首尾或关键片段、内容类型、大小、生成工具、权限范围和可继续读取的引用。
  summary: |
    配套要求：引用必须稳定且受权限保护（临时 URL 在任务恢复时可能失效）；随时间变化的结果须记录读取时版本、时间或快照标识；
    模型需要细节时经搜索、范围读取或分页工具按需取回。节省的是后续每一轮不再重复携带同一大结果。
  tags: [rule, result-offloading, references]

- id: p1-130
  title: 影响后续行动的事实先落权威状态，才可移出上下文
  type: rule
  source_chapter: 第 5 章 · 5.2（5.7 小结重申）
  source_quote: |
    任何会影响后续行动的事实，都应先进入权威状态，再允许从活动上下文中移除。否则一次不准确的摘要就可能改变任务真实状态。
  summary: |
    典型对象：审批、工具提交结果、Plan 状态、Artifact、外部对象 ID、预算消耗、用户变更。
    顺序强制：先持久化，后移除；压缩/卸载/Reset 均受此约束。
  tags: [rule, authoritative-state, compaction-boundary]

- id: p1-131
  title: 上下文生命周期四步：Commit / Compact / Rebuild / Validate
  type: checklist
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    Commit：将结构化事实提交到 Task State、Workspace 或相应资产库。Compact：把可叙述历史转换为摘要，附带来源和覆盖范围。
  summary: |
    四步定义：Commit（结构化事实入权威状态/资产库）→Compact（可叙述历史转摘要，附来源与覆盖范围）→Rebuild（用新摘要+当前状态+最近消息重建 Context，检查关键约束仍在）→Validate（对目标、未解决项、权限模式、关键证据做完整性检查，用续行用例验证无行为漂移）。
  tags: [checklist, context-lifecycle, compaction]

- id: p1-132
  title: Context Reset 的前提是 Continuation 已形成一致继续点
  type: rule
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    当多次压缩仍不足以维持有效窗口，或任务进入新的大阶段时，可以进行 Context Reset。Reset 的前提是第 4 章所述 Continuation 已经形成一致继续点。
  summary: |
    Reset 触发条件（两条之一）：多次压缩仍不足 / 进入新大阶段。前提：Continuation Package 已形成。
    Continuation Package 内容：目标与验收标准、当前 Plan/Todo/阻塞、结构化事实与决定、Workspace/Artifact Manifest、活动异步任务与审批、相关 Memory/Knowledge 引用、权限与预算快照、下一步简报。
    新窗口从包+权威状态+当前环境重建，不重放全部对话；文件式交接适合工作区 Agent。
  tags: [rule, context-reset, continuation]

- id: p1-133
  title: 压缩质量五指标
  type: calculation
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    压缩质量不能只看节省多少 Token。至少要同时衡量：事实保留率……继续成功率……矛盾率……引用可用率……成本收益。
  summary: |
    指标口径：(1) 事实保留率——关键事实、约束、决定是否完整保留；(2) 继续成功率——压缩或 Reset 后能否不重复大量探索地继续；
    (3) 矛盾率——摘要是否与工具事实、Task State 或最新指令冲突；(4) 引用可用率——外部 Artifact 与分页引用是否仍可访问；
    (5) 成本收益——减少的 Token 与额外压缩调用、读取轮次的平衡。
    inputs: 压缩前后 Context、Task State、Artifact 引用；output: 五个比率/平衡判断；原文未给阈值，需按任务定。
  tags: [calculation, compaction-metrics, quality]

- id: p1-134
  title: 压缩器、摘要 Schema 与阈值必须版本化并进回归
  type: rule
  source_chapter: 第 5 章 · 5.2
  source_quote: |
    压缩器、摘要 Schema 和阈值都应版本化，并进入 Agent 版本的回归范围。
  summary: |
    压缩行为本身是 Agent 行为的一部分；压缩配置变更须随 Agent Release 回归验证。
  tags: [rule, compaction, versioning]

- id: p1-135
  title: Call / Session / Task 三分，Task ID 不得绑定为消息线程 ID
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    一个 Session 可以发起多个 Task；一个长 Task 也可以在多个 Session 中被查看、干预和恢复。将 Task ID 绑定为消息线程 ID，会限制后台执行、多人协作和跨渠道续接。
  summary: |
    三对象：Call（一次请求/恢复动作，秒到分钟）、Session（用户与 Agent 连续交互边界，分钟到数天）、Task（围绕可验收目标的执行对象，可跨 Call/Session/进程/节点）。
    Harness 应分别保留 Session 与 Task，并显式记录关联关系。
  tags: [rule, session-task-call, identity]

- id: p1-136
  title: Event Log / Snapshot / Checkpoint 三种状态表示不可互替
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    三者不能互相替代。只有 Event Log，恢复成本会随任务长度增长；只有 Snapshot，无法解释状态如何形成；把每次状态保存都称作 Checkpoint，则会掩盖某些工具事务仍在进行、不能安全重放的事实。
  summary: |
    Event Log 记录发生过什么（因果、审计、重建）；Snapshot 记录某时刻聚合状态（快速读取当前视图）；Checkpoint 是可安全恢复执行的位置（还需包含 Continuation、幂等、环境依赖）。
    Harness 须定义状态 Schema、事件归并规则、乐观并发版本与安全点语义。
  tags: [rule, state-representation, checkpoint]

- id: p1-137
  title: 面向逻辑状态接口编程，不绑定本地内存或特定数据库
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    Harness 还应面向逻辑状态接口编程，而不是把恢复能力绑定到本地内存或某个数据库。
  summary: |
    接口集：append_event（带版本与因果关系）、load_task_state / commit_task_patch、save_snapshot / load_snapshot、put_artifact / get_artifact（带元数据）、create_checkpoint / resume_checkpoint、search_workspace / read_range。
    本地 Agent 映射到文件与进程内状态，分布式映射到外置状态服务；逻辑语义一致即可接入同一企业状态平台。
  tags: [rule, state-interface, portability]

- id: p1-138
  title: Workspace 与 Memory 的区分
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    Workspace 服务当前任务的显式工作过程，内容通常可被用户直接查看和编辑；Memory 则是跨任务选择性保留的经验和事实。
  summary: |
    Workspace 是可寻址、可检查、可逐步修改的外部工作空间（代码目录、文档空间、数据目录、受控对象存储视图）；Memory 是跨任务经验。
    混淆后果：临时工作过程被误当长期经验，或长期经验被任务清理误删。
  tags: [rule, workspace, memory]

- id: p1-139
  title: Workspace 内对象按生命周期与责任区分，不同策略管理
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    目录形式只是示意，核心是区分生命周期和责任……不能因为它们都存在文件系统中，就使用同样的保留和权限策略。
  summary: |
    逻辑分区模板：inputs/（保持来源）、scratch/（任务后可清理）、state/（Harness 管理）、artifacts/（可交付）、evidence/（证明完成）、manifest（来源/版本/权限/状态/保留策略）。
    每对象至少记录：稳定 ID、路径/对象引用、内容类型、创建者、来源、版本、权限范围、所属任务、状态、校验摘要、保留期限。
  tags: [rule, workspace, lifecycle]

- id: p1-140
  title: 模型写出文件不等于产物完成
  type: rule
  source_chapter: 第 5 章 · 5.3
  source_quote: |
    模型写出文件不等于产物已经完成。进入 Ready 前应通过格式、测试或业务验收；进入 Published 往往还需要权限审批和提交动作。
  summary: |
    Artifact 状态机：Draft → Validating → Ready → Published / Rejected → Archived / Deleted。状态变化的实际外部行动由 Action Plane 承担，本章保存对象与版本事实。
  tags: [rule, artifact, completion-semantics]

- id: p1-141
  title: Memory 四分类清单
  type: checklist
  source_chapter: 第 5 章 · 5.4（表）
  source_quote: |
    Memory 不是一个无限增长的历史数据库。它是 Harness 有选择地写入、检索、更新和遗忘的信息，用于改善后续决策。
  summary: |
    Working Memory（当前任务临时事实，Task/Session 级，来自 Task State 或 Workspace）；Episodic Memory（过去任务经验片段，按相似性与结果质量检索）；
    Semantic Memory（稳定事实、偏好、实体关系，按实体/主题/权限检索）；Procedural Memory（已验证方法步骤，通常沉淀为 Skill/规则/策略）。
    约束：Episodic 要保留当时条件和 Outcome（防偶然成功当普遍规律）；Semantic 需来源与更新时间。
  tags: [checklist, memory-types, classification]

- id: p1-142
  title: 稳定的 Procedural Memory 应升级为受版本治理的 Skill
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    Procedural Memory 如果已经稳定且可复用，最好升级为受版本治理的 Skill，而不是长期停留在自由文本记忆中。
  summary: |
    可复用方法从记忆迁入资产管理：可测试、可版本化、可发布回滚，避免自由文本记忆中的方法漂移。
  tags: [rule, memory, skill-promotion]

- id: p1-143
  title: Memory 写入前六问
  type: checklist
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    "每次任务结束自动总结并写入 Memory"很容易造成污染。Harness 在写入前应判断：这条信息是否会在未来任务中产生可预期价值？
  summary: |
    六问：(1) 是否会在未来任务产生可预期价值？(2) 是环境验证的事实，还是模型推测/用户临时表达？(3) 属于哪个用户/项目/租户/保留周期？
    (4) 是否含敏感、受限或依法不应长期保存的数据？(5) 是否已存在——新增、合并、更新还是标记冲突？(6) 未来出错时谁可纠正删除，派生索引如何清理？
  tags: [checklist, memory-write, pollution-prevention]

- id: p1-144
  title: 高价值 Memory 必须携带元数据
  type: template
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    高价值 Memory 应包含内容之外的元数据：来源事件、证据、可信度、适用条件、作用域、创建者、最近验证时间、使用次数、成功或失败反馈、过期策略和版本。
  summary: |
    十一项元数据模板：来源事件、证据、可信度、适用条件、作用域、创建者、最近验证时间、使用次数、成功/失败反馈、过期策略、版本。
    Memory 检索同时考虑语义相关性、实体匹配、时间、作用域、可信度和历史效果。
  tags: [template, memory-metadata]

- id: p1-145
  title: 被检索到的 Memory 不能直接写入 Context
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    被检索到并不代表可以直接写入 Context；Context Builder 还要依据当前身份和任务目的过滤，并把它标记为历史经验而不是当前事实。
  summary: |
    检索命中≠可注入：还需当前身份与任务目的过滤，且以"历史经验"标记进入，防止与当前事实混淆。
  tags: [rule, memory-retrieval, context-injection]

- id: p1-146
  title: Memory 冲突不得静默覆盖
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    当新信息与旧 Memory 冲突时，Harness 不应静默覆盖。可以保留多个版本和来源，按时间或权威性选择当前值，并在高影响场景请求确认。
  summary: |
    冲突处理：保留多版本与来源→按时间或权威性选当前值→高影响场景请求人工确认。
  tags: [rule, memory-conflict, versioning]

- id: p1-147
  title: 长期未用/未验证/致错的 Memory 应衰减或遗忘
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    长期未使用、长期未验证或持续导致错误结果的 Memory 应衰减权重、进入复核或被遗忘。
  summary: |
    Memory 生命周期管理三触发：长期未使用、长期未验证、持续导致错误结果；处置：衰减权重、复核、遗忘。
  tags: [rule, memory-decay, lifecycle]

- id: p1-148
  title: Knowledge 提供事实、Memory 提供经验（治理责任分离）
  type: rule
  source_chapter: 第 5 章 · 5.4（表）
  source_quote: |
    企业 Knowledge 是由组织维护、具有来源和时效的业务事实……Memory 是 Agent 从任务和用户交互中选择性积累的经验或个体信息。
  summary: |
    分离维度：来源（企业权威文档/系统 vs Agent 任务与用户反馈）；权威责任（内容所有者与业务系统 vs Harness/用户/项目责任人）；
    更新方式（同步、发布、索引刷新 vs 写入、合并、纠正、衰减、遗忘）；使用风险（过期/权限泄漏/来源冲突 vs 污染/错误固化/跨用户混淆）。
  tags: [rule, knowledge-memory, governance]

- id: p1-149
  title: 实时与强一致事实优先查询权威工具，不依赖离线索引
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    对实时经营数据或强一致事实，优先查询权威工具，而不是依赖离线索引；对稳定文档，可使用检索索引定位，再读取原始来源。
  summary: |
    检索通道按事实性质分流：实时/强一致→权威工具直查；稳定文档→索引定位+读原始来源。
    Harness 关心的判断：当前任务是否需要该事实、调用方是否有权、来源是否有效、多源冲突如何呈现、是否需引用证据、检索结果是否含非可信指令。
  tags: [rule, retrieval, authoritative-source]

- id: p1-150
  title: 权限不能靠向量库中的自然语言标签推断
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    任何检索结果在进入模型前都必须完成租户和用户权限过滤，权限不能仅靠向量库中的自然语言标签推断。
  summary: |
    权限过滤必须在进入模型前完成，且依据结构化权限模型而非文本标签；自然语言标签可被伪造或遗漏。
  tags: [rule, permission-filter, retrieval]

- id: p1-151
  title: 知识接口必须返回结构化证据
  type: template
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    面向 Harness 的知识接口应返回结构化证据，而不仅是一段拼接文本。
  summary: |
    Knowledge Evidence 字段：content / structured value、source 与稳定标识、version 或生效时间、owner 与权威级别、tenant/project/ACL scope、retrieval reason 与 score、freshness/expiration、citation 或 query trace。
  tags: [template, knowledge-evidence, structured-output]

- id: p1-152
  title: 双层记忆：原始记忆流水与已整理长期记忆分开
  type: rule
  source_chapter: 第 5 章 · 5.4
  source_quote: |
    一种实用实现是把"原始记忆流水"和"已整理长期记忆"分开……对话压缩前还可以先 Flush 关键事实，避免摘要把可复用经验一起抹掉。
  summary: |
    实现模式：当日事实追加到 memory/日期.md（保留来源过程），周期性合并去重到 MEMORY.md（每轮按策略进入 System Context）；
    压缩前先 Flush 关键事实。
  tags: [rule, memory-implementation, two-tier]

- id: p1-153
  title: 信息写入去向判定（五类信息分派表）
  type: checklist
  source_chapter: 第 5 章 · 5.4（表）
  source_quote: |
    这一分可以阻止最常见的记忆误用：把一次任务的临时状态当成长期经验，把模型总结当成企业事实，或把尚未验证的方法直接推广到所有项目。
  summary: |
    去向规则：当前补丁/测试状态/待审批项→Task State/Workspace（精确恢复）；依赖兼容约束→Project Memory 候选（需来源与验证时间）；
    企业制度→Knowledge（制度所有者维护，Agent 不得自行改写）；已验证分析测试步骤→Skill 候选（测试、版本化、发布）；未验证的模型猜测→不写入长期 Memory。
  tags: [checklist, information-routing, memory-hygiene]

- id: p1-154
  title: Skill 不等于 Prompt 或 Tool 别名，且不自带权限
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    Skill 不等于一段 Prompt，也不等于 Tool 的别名……它不直接拥有额外权限，只有在当前用户、任务和环境允许时，相关能力才能执行。
  summary: |
    定位：Tool 告诉 Agent"能做什么动作"，Skill 告诉 Agent"在某类任务中如何正确使用若干动作"。
    组成：manifest（名称/版本/所有者、描述/适用性、所需工具权限环境、输入输出验收契约）+ instructions + scripts + templates + examples + references + tests。
  tags: [rule, skill, definition-boundary]

- id: p1-155
  title: Skill 渐进式披露三层
  type: checklist
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    当企业积累数百个 Skill 时，全部注入每轮 Context 会迅速耗尽窗口，也会让模型选择错误能力。渐进式披露可分为三层。
  summary: |
    发现层：模型只看见名称、简短描述、适用条件、主要风险；选择层：Harness 据任务/权限/环境解析候选并加载完整 Manifest；
    执行层：真正需要某一步时才读取详细指令、脚本、模板与参考资源。
  tags: [checklist, progressive-disclosure, skill]

- id: p1-156
  title: Skill 选择不只靠语义匹配，须过六项检查
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    Skill 选择不应只依赖模型语义匹配。Harness 还应检查模型兼容性、工具依赖、环境条件、租户许可、数据范围和版本状态。若 Skill 要求写生产系统，而当前任务处于只读探索阶段，它可以被发现，但不能进入可执行状态。
  summary: |
    六项检查：模型兼容性、工具依赖、环境条件、租户许可、数据范围、版本状态。"可发现"与"可执行"分离。
  tags: [rule, skill-selection, gating]

- id: p1-157
  title: Skill 内确定性步骤与模型判断的边界
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    Skill 中稳定、重复、可编码且失败代价高的步骤，适合沉淀为脚本、工具或规则……需要理解模糊目标、比较方案、解释异常或根据新证据调整方向的部分，保留为模型指令。
  summary: |
    判据四条件：稳定、重复、可编码、失败代价高→脚本化；模糊理解与权衡→模型指令。
    约束：脚本不能藏在说明文本中被不受控运行，仍要经过 Action Plane、环境和权限契约。
  tags: [rule, skill-design, deterministic-split]

- id: p1-158
  title: 任务成功不等于可复用能力，Skill 沉淀前先清洗验证
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    一次任务成功，不意味着它已经成为可复用能力。团队应先从 Trace 中提取稳定步骤，移除特定任务 ID、临时路径和一次性判断，为脚本和模板补充测试，再形成 Skill。
  summary: |
    沉淀流程：Trace 提取稳定步骤→移除任务特定信息（ID/临时路径/一次性判断）→补测试→成 Skill。描述决定何时进入候选集合，正文说明模型判断步骤，脚本承担确定性动作。
  tags: [rule, skill-creation, distillation]

- id: p1-159
  title: Skill 按软件资产管理（生命周期状态机与注册表）
  type: template
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    企业需要把 Skill 当作软件资产管理。Skill Registry 至少记录所有者、作用域、版本、依赖、权限需求、支持的 Agent / Model、测试结果、发布日期、弃用状态和使用效果。
  summary: |
    生命周期：Draft（编写本地试验）→Test（脚本、契约、任务用例）→Review（安全、权限、领域评审）→Publish（进入允许作用域）→Observe（使用率、成功率、失败模式）→Update / Deprecate / Rollback（回到 Test）。
    Registry 十项字段：所有者、作用域、版本、依赖、权限需求、支持的 Agent/Model、测试结果、发布日期、弃用状态、使用效果。
  tags: [template, skill-lifecycle, registry]

- id: p1-160
  title: Skill 评估不看是否被选中，看启用前后效果差
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    Skill 的评估不应只看是否被模型选中。还要比较启用前后的任务成功率、步骤数、工具错误、人工修改量、成本和安全事件；对于很少被采用或持续降低效果的 Skill，应调整描述、缩小适用范围或下线。
  summary: |
    对比维度：任务成功率、步骤数、工具错误、人工修改量、成本、安全事件；低价值处置：调描述、缩范围、下线。
  tags: [rule, skill-evaluation, before-after]

- id: p1-161
  title: 线上 Trace 必须能定位到具体 Skill 版本
  type: rule
  source_chapter: 第 5 章 · 5.5
  source_quote: |
    线上 Trace 必须能够定位到具体 Skill 版本。latest 指针适合开发，不适合不可追溯的生产执行。
  summary: |
    Agent 版本可锁定允许的 Skill 集与版本范围；紧急修复经新 Agent 版本、受控热补丁或明确策略覆盖生效，并触发相关回归集。
  tags: [rule, skill-versioning, traceability]

- id: p1-162
  title: 资产作用域六层模型
  type: checklist
  source_chapter: 第 5 章 · 5.6（表）
  source_quote: |
    Context、Memory、Knowledge 和 Skill 都可能跨任务复用，但不能默认全局可见。企业应建立一致的作用域模型。
  summary: |
    六层：Global（平台安全策略、通用基础 Skill；所有获授权 Agent，通常只读）、Tenant（企业制度、租户 Knowledge/Skill）、
    Project（项目规则、代码规范、项目 Memory；项目成员与关联 Agent）、User（个人偏好与历史；本人及明确委派任务）、
    Session（当前交互偏好与临时输入）、Task（Plan/Todo/Scratch/Artifact/证据；当前 Task 及获授权父子任务）。
  tags: [checklist, scope-model, multi-tenancy]

- id: p1-163
  title: 先确定身份与作用域再检索，隔离先于生成
  type: rule
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    检索和 Context 构建必须先确定调用身份、租户、项目和任务，再查询允许作用域。不要先跨作用域召回再在生成端"提醒模型不要泄漏"，因为内容进入模型输入时，隔离已经失败。
  summary: |
    隔离时点规则：权限过滤发生在检索前；内容一旦进入模型输入，隔离已失败，生成端"提醒"无效。
  tags: [rule, isolation-first, tenancy]

- id: p1-164
  title: 每项资产携带最小治理元数据
  type: template
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    每项资产都应携带最小治理元数据：来源、所有者、作用域、版本、创建与更新时间、权限、敏感级别、保留周期、内容摘要、派生关系和状态。
  summary: |
    十一项基础元数据 + 类型特有项：Memory 加可信度与适用条件；Knowledge 加生效时间与权威来源；Skill 加依赖和评估基线；Context Summary 加覆盖事件范围。
    统一元数据使同一套 Policy 决定"能否读取、能否写入、如何引用、何时过期、如何删除"，并使 Agent 版本准确绑定依赖资产版本。
  tags: [template, asset-metadata, governance]

- id: p1-165
  title: 污染防护必须覆盖写入与读取两端
  type: checklist
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    防护需要覆盖写入和读取两端。写入时进行来源识别、验证、敏感数据检测、作用域绑定和冲突检查；读取时进行身份过滤、信任标记、指令与数据分离、最小披露和输出审查。
  summary: |
    三类污染对象：记忆污染（错误推测/失败轨迹/恶意输入被长期写入当经验）、指令污染（外部文本被提升为高优先级行为规则）、跨租户污染（索引/缓存/摘要/Artifact/评估数据跨租户泄漏）。
    写入端五动作：来源识别、验证、敏感数据检测、作用域绑定、冲突检查；读取端五动作：身份过滤、信任标记、指令与数据分离、最小披露、输出审查。高风险 Memory 可先进候选区经人工或规则验证再发布。
  tags: [checklist, pollution-defense, read-write]

- id: p1-166
  title: 真正的删除必须清理派生链（六步）
  type: checklist
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    禁止新的读取与 Context 注入；删除或隔离原始内容；清理索引、缓存、摘要和其他派生数据；更新引用该资产的 Manifest 与 Skill；保留法律允许且最小化的审计证明；触发受影响 Agent 版本的验证或重新发布。
  summary: |
    删除六步：禁新读取→删/隔离原文→清派生（索引、缓存、摘要）→更新引用（Manifest、Skill）→留最小审计→触发受影响版本验证或重发布。
    前提：企业要能从稳定资产 ID 追踪派生链（文档→切片→向量→摘要→实体；Memory 合并关系；Skill 引用关系）。
  tags: [checklist, deletion, lineage]

- id: p1-167
  title: 权重衰减与彻底删除必须在策略中区分
  type: rule
  source_chapter: 第 5 章 · 5.6（5.4 重申）
  source_quote: |
    "遗忘"有时是权重衰减，有时是彻底删除，二者必须在策略中区分。需要撤回的数据不能只通过降低检索分数来处理。
  summary: |
    两种遗忘语义：衰减（降权重）与删除（清派生链）；需撤回的数据必须走删除路径，降检索分数不构成撤回。
  tags: [rule, deletion-semantics, right-to-be-forgotten]

- id: p1-168
  title: 资产变更纳入与代码相同的发布链（反退化闭环）
  type: checklist
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    Context Policy、Memory、Knowledge 和 Skill 的任何更新都可能改变 Agent 行为。企业应把资产变更纳入与代码相同的发布链。
  summary: |
    发布链：资产变更→结构与权限校验→受影响 Agent/用例分析→离线回放与安全评估→灰度进入新 Agent 版本→Trace 与 Outcome 监测→保留、修订或回滚。
    反退化监测不止成功率：成本、延迟、引用准确性、跨租户泄漏、错误 Memory 使用、Skill 误选、上下文膨胀。
  tags: [checklist, asset-release, anti-regression]

- id: p1-169
  title: 资产效果评估必须分层
  type: rule
  source_chapter: 第 5 章 · 5.6
  source_quote: |
    一个资产可能提升某类任务，却损害另一类任务，因此评估必须按场景、租户、风险和任务复杂度分层。
  summary: |
    评估分层四维度：场景、租户、风险、任务复杂度；聚合评估会掩盖局部退化。
  tags: [rule, layered-evaluation, assets]

- id: p1-170
  title: 三种构建路径的上下文与状态责任边界
  type: checklist
  source_chapter: 第 5 章 · 5.6（表）
  source_quote: |
    Managed Agents 托管更多 Session 与执行基础，却不会替企业决定哪些知识可见、哪些记忆可写、哪些 Skill 可以在某个租户中使用。
  summary: |
    Framework（AgentScope）：企业为 Context 选择与状态正确性负责，调优空间最大，适合业务差异大、资产治理要求高的场景；
    SDK/产品化 Harness：复用成熟 Harness，多租户隔离、共享存储、业务验收仍由企业补齐；
    Managed Agents：托管 Session 与执行基础，知识可见性、记忆可写性、Skill 租户许可仍由企业治理。
    五个能力维度的三路径对照：Context 装配、Session/Task、Workspace/Artifact、Memory/Knowledge、Skill。
  tags: [checklist, build-entry, responsibility-matrix]
```
