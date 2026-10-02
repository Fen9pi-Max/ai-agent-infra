# counter-example extractor：全书失败模式

> 来源：AI Agent 手册（阿里云）。提取范围：2.2/2.4、5.2/5.6、6.1/6.6、11.7、第 14 章安全全章、第 17 章模型调优、第 22 章 Badcase 分析。未做筛选，供阶段 2 的 B（Boundary）段取用。

## 第 2 章 参考架构

- id: ce-01
  title: 把协议兼容当成能力等价（降级/路由模型直接上线）
  type: counter-example
  source_chapter: 2.2 构建与编排
  source_quote: |
    "一种需要防范的失败模式是：降级模型在协议层可以正常返回工具调用，但在多步任务中丢失中间约束，最终产生的是一次结构合法却结果错误的执行。"
  failure_mode: |
    模型路由与降级策略只验证协议层兼容（能返回合法工具调用），不与完整 Harness 一起回归评估就上线，多步任务中丢失中间约束，产生结构合法却结果错误的执行。
  mechanism: |
    协议兼容只保证输出格式可解析，不保证长上下文中的指令遵循与约束保持能力。
    降级模型的单步行为与多步任务行为可以严重不一致，格式校验无法发现。
  warning_signs:
    - 降级后单工具调用测试全部通过，但端到端多步任务成功率下降
    - 任务结果"看起来正常"但遗漏早期约束条件
    - 路由策略变更没有配套回归评估
  bound_to:
    - "模型路由与降级设计"
    - "调优归因判据"
  tags: [counter-example, model-routing, degradation]

- id: ce-02
  title: 策略只靠提示词实现，模型可以绕过约束
  type: counter-example
  source_chapter: 2.2 构建与编排（表 2-2）
  source_quote: |
    "策略只能依赖提示词，模型可以绕过约束"
  failure_mode: |
    权限、审批、审计、内容过滤等策略全部写进 Prompt 而非 Middleware/Policy Hook，模型在概率性行为中可以绕过约束。
  mechanism: |
    模型对指令的遵循是概率性的；策略若不在模型调用、工具调用、状态变更等确定性环节强制执行，就没有任何系统层保证。
  warning_signs:
    - 安全/权限要求只存在于 Prompt 文本中
    - 没有 Middleware 或 Policy Hook 层
    - 约束违反只能靠事后人工发现
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, prompt-only-policy, security]

- id: ce-03
  title: 任务"看起来完成"但结果不可信（验证与 HITL 缺失）
  type: counter-example
  source_chapter: 2.2 构建与编排（表 2-2）
  source_quote: |
    "高风险动作缺少人类监督，任务"看起来完成"但结果不可信"
  failure_mode: |
    缺少结束前的测试、评审、结果完整性检查与环境状态验证，以模型自行宣布完成或进程结束作为任务完成，高风险动作也无人监督。
  mechanism: |
    完成语义必须由环境证据判定；没有 Verifier 与 HITL 环节时，模型的"我认为完成了"直接成为交付依据，错误不可见直到业务受损。
  warning_signs:
    - 完成判定依据是模型输出文本而非测试/文件差异/业务校验
    - 高风险动作前没有审批点
    - 交付后频繁返工或用户投诉"说做完了其实没做"
  bound_to:
    - "任务契约设计"
    - "上线前仿真"
  tags: [counter-example, completion-semantics, verification]

- id: ce-04
  title: 工具错误被当成模型错误
  type: counter-example
  source_chapter: 2.2 构建与编排（表 2-2）
  source_quote: |
    "工具描述挤占上下文，工具错误被当成模型错误"
  failure_mode: |
    工具与 Skill 管理能力缺失（能力注册、参数校验、结果截断、工具失败分类）时，工具侧故障被误判为模型能力不足，导致在错误的层上投入调优。
  mechanism: |
    工具失败与模型决策失败在 Trace 上呈现相似现象（任务未完成），没有工具失败分类与确定性归因手段时，责任无法区分。
  warning_signs:
    - 失败样本中工具超时/报错/结果截断占比高却未被单独统计
    - 调优讨论直接从"换模型/训模型"开始
  bound_to:
    - "调优归因判据"
    - "行动契约设计"
  tags: [counter-example, attribution, tooling]

- id: ce-05
  title: 压缩/截断/模型误读能改变任务事实（Context 被当 State 用）
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.3）
  source_quote: |
    "必须将可恢复任务的真实状态保存在模型上下文之外：如果压缩、截断或模型误读能够改变任务事实，任务就失去了可恢复性，长任务与多智能体协作也无从谈起。"
  failure_mode: |
    把不完整对话历史（压缩摘要）当作可恢复的任务事实，关键状态只存在于上下文窗口内，压缩或截断后任务事实被改变或丢失。
  mechanism: |
    Context 是模型此刻看见什么（持续被重建的视图），State 是权威任务事实（需强一致持久化）。
    摘要是有损的：一旦权威事实只能从摘要恢复，一次不准确的压缩就永久改变任务状态，长任务与多 Agent 协作失去恢复基础。
  warning_signs:
    - 任务恢复/续行的唯一依据是历史摘要
    - 压缩后 Agent 遗忘审批结果、用户变更、已否决方案
    - 没有独立于对话窗口的 Task State 存储
  bound_to:
    - "信息契约设计"
    - "状态存储分层"
  tags: [counter-example, context-state, compaction]

- id: ce-06
  title: Memory 与 Knowledge 放进同一个向量库
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.3）
  source_quote: |
    "把两者放进同一个向量库，往往会同时失去这两类治理能力。"
  failure_mode: |
    将运行中生成的 Memory 与组织既有 Knowledge 混入同一存储，同时失去写入门槛/遗忘机制与权限过滤/时效管理两类治理能力。
  mechanism: |
    Memory 的风险是错误经验被反复沿用（需写入门槛与遗忘），Knowledge 的风险是权限越界与信息过期（需与源系统权限和更新周期对齐）；二者的写入方式与治理责任不同，混存后无法分别施加策略。
  warning_signs:
    - 记忆与文档切片在同一索引中无来源区分
    - 删除请求无法区分"衰减记忆"与"下线文档"
    - 知识越权事件与错误经验复用同时出现
  bound_to:
    - "信息契约设计"
    - "状态存储分层"
  tags: [counter-example, memory, knowledge, governance]

- id: ce-07
  title: 能力被发现等于已授权（受控行动六环节缺失）
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.4）
  source_quote: |
    "能力被发现，不等于能力已被授权；工具参数符合 Schema，不等于工具意图符合用户目标；命令在 Sandbox 中成功运行，也不等于结果可以安全地影响生产系统。"
  failure_mode: |
    跳过身份与权限判定、参数校验、隔离执行、结果校验、状态与审计记录中的若干环节，把模型建议的动作直接变成系统执行的事实。
  mechanism: |
    一次受控行动需要六个环节分布在编排层、Gateway、Sandbox 和控制面；任何一个环节缺失，"模型看见工具"就会被等同于"有权执行"，最小权限模型整体失效。
  warning_signs:
    - 工具注册即对模型可见且可调用，无 Policy 决策点
    - 沙箱测试通过后直接连生产资源
    - 审计记录无法回答"这次行动以谁的身份发生"
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, authorization, action-plane]

- id: ce-08
  title: 编排层直接依赖 Runtime 的内存结构
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.2）
  source_quote: |
    "二者的接口应当是任务与状态，而不是函数调用细节；一旦编排层直接依赖某个 Runtime 的内存结构，原则二就已经被破坏。"
  failure_mode: |
    Harness 编排层与 Runtime 的接口退化为函数调用细节/内存结构依赖，导致 Runtime 无法替换、任务无法跨实例恢复。
  mechanism: |
    编排层负责语义决策、Runtime 负责物理保障；耦合后运行位置、恢复点、配额回收等物理变化都会破坏任务语义，责任边界随之消失。
  warning_signs:
    - 换 Runtime 或升级 SDK 需要改编排逻辑
    - 进程重启后任务状态丢失
    - 接口讨论集中在数据结构而非任务与状态
  bound_to:
    - "运行环境设计"
    - "任务契约设计"
  tags: [counter-example, coupling, runtime]

- id: ce-09
  title: 为旧模型设计的复杂脚手架限制新模型
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.2）
  source_quote: |
    "模型能力增强后，为弥补旧模型缺陷而设计的复杂脚手架可能反而限制新模型，因此脚手架需要随模型迭代重新评估甚至简化。"
  failure_mode: |
    模型升级后保留全部旧补偿逻辑，复杂编排反而拖累新模型表现，且团队把退化归因于新模型本身。
  mechanism: |
    脚手架中的补丁规则针对旧模型特定缺陷；新模型能力增强后这些规则变成错误先验/冗余约束，与模型自身策略冲突。
  warning_signs:
    - 模型升级后指标不升反降，且无其他变更
    - 编排层积累了大量"防呆"补丁规则从未清理
  bound_to:
    - "调优归因判据"
    - "Harness 构建入口选择"
  tags: [counter-example, scaffolding, model-upgrade]

- id: ce-10
  title: 编排层一次设计定型、默认越复杂越好
  type: counter-example
  source_chapter: 2.2 构建与编排（2.2.2）
  source_quote: |
    "编排层本身是决定 Agent 能力的可评估对象，既不能一次设计定型，也不能默认越复杂越好。"
  failure_mode: |
    把 Harness 编排当作一次性交付的静态代码，不随 Trace 分析与模型演进迭代评估，或无差别堆叠编排复杂度。
  mechanism: |
    编排层与模型共同演进：在模型固定的前提下优化编排逻辑同样能改变完成质量（LangChain 实践），复杂度本身不是能力，未评估的复杂度只会增加失效面。
  warning_signs:
    - 编排逻辑没有任何基于 Trace 的迭代记录
    - 复杂度增加没有对应的效果评估
  bound_to:
    - "Harness 构建入口选择"
    - "调优归因判据"
  tags: [counter-example, overengineering, evolution]

- id: ce-11
  title: Prompt 在生产中随时修改而不留版本
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.1）
  source_quote: |
    "反过来，如果 Prompt 可以在生产中随时修改而不留版本，任何评估结论都只对当时那一刻成立。"
  failure_mode: |
    Prompt/Context Policy 等资产在生产中可随时热改且无版本追踪，失败无法复现，变更后也无法证明质量确实提升。
  mechanism: |
    Agent 行为由模型、Prompt、Context、Memory、Tool、Harness、预算与环境的组合决定；任一要素无版本化，Agent Release 就不是可复现单元，评估结论随下一次热改立即作废。
  warning_signs:
    - 生产 Prompt 有多个入口可以改且不留痕
    - 复现线上问题时无法确定当时用的 Prompt 版本
  bound_to:
    - "资产注册发现"
    - "上线前仿真"
    - "黄金集构建"
  tags: [counter-example, versioning, release]

- id: ce-12
  title: 架构决策留给构建阶段隐式完成
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.2）
  source_quote: |
    "把这些决策留给构建阶段隐式完成，等价于让实现细节反向定义架构约束，而这类问题往往只能通过重构而非补丁解决。"
  failure_mode: |
    形态选择、自主性授予、责任域划分、权限边界不在架构设计阶段显式决策，由实现细节倒推确定，后续成本区间被错误锁定。
  mechanism: |
    这些决策一旦确定，构建、运行、治理、调优的成本区间基本被锁定；实现细节反向定义约束后，问题只能靠重构解决。
  warning_signs:
    - 立项时没有架构决策记录与影响半径评估
    - 权限模型是开发过程中"顺手"形成的
  bound_to:
    - "形态与架构选型"
  tags: [counter-example, architecture, lifecycle]

- id: ce-13
  title: 风险来自形态/权限模型本身时继续叠加补丁
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.2）
  source_quote: |
    "当风险来自形态选择或权限模型本身，而不是某个 Prompt 或某次工具调用时，正确的动作是重新设计架构，而不是继续叠加补丁。"
  failure_mode: |
    治理与调优发现的风险根因在架构层（形态选择、权限模型），团队却持续在 Prompt 和工具调用层打补丁，风险不收敛。
  mechanism: |
    补丁作用于症状层；架构层缺陷会在所有补丁之下持续产生新的风险实例，投入与风险收敛不成比例。
  warning_signs:
    - 同类安全/失败事件反复出现，补丁越打越多
    - 风险复盘结论指向自主性过大或权限模型，但仍无架构变更计划
  bound_to:
    - "形态与架构选型"
    - "Agent 安全防护"
  tags: [counter-example, patch-vs-redesign]

- id: ce-14
  title: 治理要求只能上线后靠流程和人工补齐
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.3）
  source_quote: |
    "如果某项治理要求只能在上线后通过流程和人工补齐，而不能在架构和构建阶段落到策略、接口或数据结构上，那么它在规模化之后很可能会失效。"
  failure_mode: |
    权限、审计、数据边界等治理要求未落成策略/接口/数据结构，只靠上线后的人工流程执行，规模化后失效。
  mechanism: |
    人工流程的执行容量不随任务量扩展，且无法被系统强制；治理若不是架构约束而是运营活动，流量增长必然击穿人工兜底。
  warning_signs:
    - 治理清单里大量条目依赖"人工检查"
    - 流量上涨后治理事件漏报率上升
  bound_to:
    - "可观测接入"
    - "Agent 安全防护"
  tags: [counter-example, governance, scalability]

- id: ce-15
  title: 让 Agent 在生产中自由修改自己
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.4）
  source_quote: |
    "调优不等于让 Agent 在生产中自由修改自己。任何由模型、反馈或轨迹生成的 Prompt、Memory、Skill 或 Harness 补丁，都应被视为候选变更，需要经过可复现评估、安全检查、版本化、灰度和可回滚发布之后才能进入生产。"
  failure_mode: |
    由模型/反馈/轨迹生成的 Prompt、Memory、Skill、Harness 修改绕过构建与准入门禁直接进入生产，自进化变成不可控的线上试错。
  mechanism: |
    无审批的自我修改不符合企业级自进化定义：模型生成的变更可能包含注入的恶意内容或局部有效全局有害的规则，未经评估即放大到全部流量。
  warning_signs:
    - Memory/Skill 写入没有验证与灰度环节
    - 线上行为变化找不到对应发布记录
  bound_to:
    - "受控自进化"
    - "资产注册发现"
  tags: [counter-example, self-modification, uncontrolled-evolution]

- id: ce-16
  title: 无可复现证据、无版本单元的主观调参
  type: counter-example
  source_chapter: 2.4 生命周期架构（2.4.4）
  source_quote: |
    "没有可复现的证据，调优只能依赖主观印象；没有版本化的发布单元，即使找到了改进方向，也无法证明改进确实来自这次变更。"
  failure_mode: |
    运行阶段不产出结构化 Trace/状态/成本事实，调优凭印象改参数；改进无法归因到具体变更，闭环退化为一次性人工调参。
  mechanism: |
    观测、评估与发布构成同一条链路：缺少任何一环，变更与效果之间没有可验证的因果关联，改进与退化都无法解释。
  warning_signs:
    - 改进讨论以"感觉最近好了一些"为依据
    - 变更记录与效果数据无法关联
  bound_to:
    - "调优归因判据"
    - "可观测接入"
  tags: [counter-example, evidence, tuning]

## 第 5 章 信息：上下文、状态与可复用能力资产

- id: ce-17
  title: 简单截断最早历史，丢失初始目标与关键决定
  type: counter-example
  source_chapter: 5.2 Context 生命周期与压缩
  source_quote: |
    "将完整历史永久放进窗口，会同时带来成本、延迟和注意力退化；简单截断最早内容，又容易丢失初始目标和关键决定。"
  failure_mode: |
    用固定窗口截断（丢弃最早消息）管理上下文增长，任务的原始目标、成功标准和早期关键决定被切掉，长任务中途偏航。
  mechanism: |
    任务的关键约束往往在早期建立而全程有效；按时间截断假设"越旧越不重要"，与任务结构相反。正确做法是外部保存原始历史并按阶段重构 Active Context。
  warning_signs:
    - 长任务后段行为与最初指令冲突
    - Agent 反复询问已经确认过的目标
  bound_to:
    - "信息契约设计"
  tags: [counter-example, truncation, long-task]

- id: ce-18
  title: 影响后续行动的事实未先落权威状态就移出上下文
  type: counter-example
  source_chapter: 5.2 Context 生命周期与压缩
  source_quote: |
    "任何会影响后续行动的事实，都应先进入权威状态，再允许从活动上下文中移除。……否则一次不准确的摘要就可能改变任务真实状态。"
  failure_mode: |
    审批结果、工具提交结果、Plan 状态、外部对象 ID、用户变更等事实仅存在于对话历史，压缩时由模型自由归纳，不准确的摘要改变任务真实状态。
  mechanism: |
    高风险事实若不从 Task State、工具结果、审批记录中确定性提取，而交由模型压缩叙述，摘要误差会固化为任务事实并被后续行动放大。
  warning_signs:
    - 压缩摘要没有 Schema、事件范围与来源引用
    - 压缩后出现与工具事实矛盾的"记忆"
    - 审批/用户变更只存在于聊天记录中
  bound_to:
    - "信息契约设计"
    - "状态存储分层"
  tags: [counter-example, compaction, state-authority]

- id: ce-19
  title: 卸载引用不稳定或无版本标识
  type: counter-example
  source_chapter: 5.2 Context 生命周期与压缩
  source_quote: |
    "引用必须稳定且受权限保护；如果只给出一个临时 URL，任务恢复时可能已经失效。如果结果会随时间变化，还要记录读取时版本、时间或快照标识，避免后续将新内容与旧推理混为一谈。"
  failure_mode: |
    大结果卸载到外部后只留临时 URL 或无版本引用，任务恢复时引用失效；时变内容不记读取版本，新内容与旧推理混读。
  mechanism: |
    卸载把"内容在窗口内"变成"内容可按需取回"；引用的稳定性、权限与版本是这条链路的隐性契约，缺失后压缩节省的 Token 以任务事实损坏为代价。
  warning_signs:
    - 恢复任务时报引用不可访问
    - 同一 Artifact 在续行后读到不同内容却无感知
  bound_to:
    - "信息契约设计"
  tags: [counter-example, eviction, reference-stability]

- id: ce-20
  title: 压缩质量只看节省了多少 Token
  type: counter-example
  source_chapter: 5.2 Context 生命周期与压缩
  source_quote: |
    "压缩质量不能只看节省多少 Token。至少要同时衡量：事实保留率……继续成功率……矛盾率……引用可用率……成本收益。"
  failure_mode: |
    以 Token 节省率作为压缩器唯一指标，事实丢失、摘要与权威状态矛盾、引用失效等质量退化不可见。
  mechanism: |
    压缩是有损变换，节省与保真天然冲突；只测节省等于只测收益不测成本，错误的压缩在续行失败时才暴露且难以归因。
  warning_signs:
    - 压缩器指标只有 Token 削减率
    - 压缩器/摘要 Schema/阈值未版本化、不在回归范围
  bound_to:
    - "信息契约设计"
    - "黄金集构建"
  tags: [counter-example, compaction, metrics]

- id: ce-21
  title: 数万行大结果反复进入每轮 Context
  type: counter-example
  source_chapter: 5.2 Context 生命周期与压缩
  source_quote: |
    "一次日志查询、网页抓取、代码搜索或数据分析可能返回数万行内容。大结果不应反复进入每轮 Context。"
  failure_mode: |
    超大工具结果内嵌在历史中每轮重放，成本与延迟随轮次线性放大，且注意力退化导致模型偏离目标。
  mechanism: |
    原始结果应写入 Workspace/Artifact Store 只留摘要与引用；不卸载时每一轮都重复支付同一结果的输入成本并挤占注意力。
  warning_signs:
    - 每轮 Token 消耗中单个工具结果占比极高
    - 长任务后期成本异常增长
  bound_to:
    - "信息契约设计"
  tags: [counter-example, tool-result, eviction]

- id: ce-22
  title: 先跨作用域召回，再在生成端"提醒模型不要泄漏"
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    "不要先跨作用域召回再在生成端"提醒模型不要泄漏"，因为内容进入模型输入时，隔离已经失败。"
  failure_mode: |
    检索与 Context 构建不按调用身份/租户/项目/任务先确定允许作用域，把跨租户内容召回进输入后指望 Prompt 约束模型不泄漏。
  mechanism: |
    隔离必须在查询边界（读取端）完成：内容一旦进入模型输入，就可能被复述、被摘要转述、进入后续推理，提示词约束是概率性的且可被注入攻击穿透。
  warning_signs:
    - 检索请求不带身份与作用域参数
    - 安全设计依赖输出端脱敏/审查兜底
  bound_to:
    - "信息契约设计"
    - "Agent 安全防护"
  tags: [counter-example, multi-tenant, isolation, retrieval]

- id: ce-23
  title: 记忆/指令/跨租户三类污染
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    "记忆污染：错误推测、失败轨迹或恶意输入被长期写入，并在未来任务中被当作经验。"
  failure_mode: |
    错误推测、失败轨迹、恶意输入被写入 Memory 长期沿用；外部文档/工具结果中的文字被提升为高优先级行为规则；索引/缓存/摘要把一个租户的信息带入另一个租户。
  mechanism: |
    写入端没有来源识别、验证、敏感检测、作用域绑定与冲突检查，读取端没有身份过滤与指令/数据分离，任何一次污染都会被复用机制放大到后续任务。
  warning_signs:
    - Memory 写入无门槛、无候选区
    - 工具返回的文本改变了 Agent 行为规则
    - 评估数据或摘要中出现他租户实体
  bound_to:
    - "信息契约设计"
    - "状态存储分层"
    - "Agent 安全防护"
  tags: [counter-example, poisoning, memory, cross-tenant]

- id: ce-24
  title: 需要撤回的数据只通过降低检索分数处理
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    ""遗忘"有时是权重衰减，有时是彻底删除，二者必须在策略中区分。需要撤回的数据不能只通过降低检索分数来处理。"
  failure_mode: |
    把删除请求实现为检索降权或向量移除，派生切片、摘要、缓存、索引、评估数据中的副本未清理，数据事实上仍存在并可被再次召回。
  mechanism: |
    资产存在派生链（文档→切片/向量/摘要/实体）；只处理一层会让其余层继续提供内容。合规删除需要禁止新读取、删原始内容、清派生数据、更新引用并保留最小审计证明。
  warning_signs:
    - 删除后检索仍能命中原内容的其他形态
    - 没有从稳定资产 ID 追踪派生链的机制
  bound_to:
    - "状态存储分层"
    - "Agent 安全防护"
  tags: [counter-example, deletion, derived-data, compliance]

- id: ce-25
  title: 资产变更绕过与代码相同的发布链
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    "Context Policy、Memory、Knowledge 和 Skill 的任何更新都可能改变 Agent 行为。企业应把资产变更纳入与代码相同的发布链"
  failure_mode: |
    资产（Prompt/知识/记忆/Skill/Context Policy）更新不经过受影响分析、离线回放、灰度与回滚，直接生效导致 Agent 行为退化且无法定位原因。
  mechanism: |
    资产是行为输入的一部分，变更等价于代码变更；没有反退化闭环（结构校验→影响分析→回放→灰度→监测→回滚）时，退化以成本上升、泄漏、Skill 误选等间接形式出现。
  warning_signs:
    - 知识库/Skill 更新无评审与灰度
    - 版本指标正常但成本、引用准确性、误选率悄然劣化
  bound_to:
    - "资产注册发现"
    - "受控自进化"
  tags: [counter-example, asset-change, anti-regression]

- id: ce-26
  title: 总体成功率掩盖分场景退化
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    "一个资产可能提升某类任务，却损害另一类任务，因此评估必须按场景、租户、风险和任务复杂度分层。"
  failure_mode: |
    资产/版本评估只看总体成功率，某类任务或某租户的显著退化被平均掉，问题在业务受损后才被发现。
  mechanism: |
    变更的影响按场景分布不均匀；聚合指标天然掩盖分布尾部，必须按场景、租户、风险、复杂度分层评估才能看到受损子集。
  warning_signs:
    - 评估报告只有单一总分手
    - 个别租户投诉与整体指标向好并存
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, evaluation, segmentation]

- id: ce-27
  title: 以为托管平台会替企业决定知识可见性与 Skill 授权
  type: counter-example
  source_chapter: 5.6 多租户资产治理与反退化
  source_quote: |
    "Managed Agents 托管更多 Session 与执行基础，却不会替企业决定哪些知识可见、哪些记忆可写、哪些 Skill 可以在某个租户中使用。"
  failure_mode: |
    采用 Managed Agent/云产品路径后，企业以为多租户隔离、知识可见范围、记忆写入边界由平台默认兜底，实际责任仍在企业。
  mechanism: |
    托管转移的是 Session 与执行基础的运行责任，不是业务数据边界与资产授权决策；SDK/托管路径下多租户隔离、共享存储、业务验收均需企业补齐。
  warning_signs:
    - 选型时无人回答"哪些知识对哪些租户可见"
    - 多租户需求出现在托管路径上却没有配套策略
  bound_to:
    - "Harness 构建入口选择"
    - "Agent 安全防护"
  tags: [counter-example, managed-agent, responsibility]

## 第 6 章 行动：受控执行、验证反馈与交付准备

- id: ce-28
  title: '出现在 Tool Schema 中"被等同于"可以调用'
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.1）
  source_quote: |
    "若"出现在 Tool Schema 中"就等价于"可以调用"，最小权限、用户委派和阶段性只读模式都无法成立。"
  failure_mode: |
    把"模型看见工具""Harness 注册工具""用户与任务获得执行授权"三件事混为一谈，模型可见即视为可执行，最小权限与阶段性只读失效。
  mechanism: |
    三者是不同事实：可见性由 Context 决定，注册由系统决定，授权由 Policy 与审批决定；合并后高权限能力一旦被披露给模型就自动获得执行通道。
  warning_signs:
    - 注册表中的能力全部对模型披露且无授权差异
    - 无法配置"只读阶段"或按用户委派收窄
    - 越权事件表现为"模型调了它不该调的工具"
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, authorization, least-privilege]

- id: ce-29
  title: 模型自由文本说明被当作身份与参数依据
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.2）
  source_quote: |
    "模型产生的自由文本说明只能作为 purpose 的候选输入，能力名称、参数类型、影响范围和身份必须由确定性代码解析与校验。"
  failure_mode: |
    行动请求的能力名、参数、影响范围、actor 身份取自模型的自然语言输出，未经确定性解析校验，注入与幻觉直接进入执行链。
  mechanism: |
    统一 Action Request 契约的意义就在于把语义意图转换为可校验结构；自由文本承载身份与权限信息时，攻击者可通过措辞伪造授权上下文。
  warning_signs:
    - 权限判定读取模型生成的说明字段
    - Action 请求没有 actor/purpose/risk 的确定性来源
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, action-contract, injection]

- id: ce-30
  title: 每个 Tool 各自任意修改任务权威状态
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.2）
  source_quote: |
    "Harness 统一提交 State Patch，避免每个 Tool 任意修改任务权威状态。"
  failure_mode: |
    工具实现直接写任务状态，状态更新口径不一致、无审计，任务事实被多个工具交叉污染。
  mechanism: |
    权威状态的单写点（Harness 统一提交 State Patch）是可审计与可恢复的前提；多点写入使状态变更无法归因、无法回放。
  warning_signs:
    - 多个工具 SDK 内置改状态逻辑
    - 状态异常时无法定位是哪个工具写的
  bound_to:
    - "行动契约设计"
    - "状态存储分层"
  tags: [counter-example, state-patch, single-writer]

- id: ce-31
  title: 高低风险动作被包装成一个模糊工具
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.3）
  source_quote: |
    ""创建变更单"与"发布生产环境"不能被包装成一个模糊工具，因为它们的身份、风险、可逆性和审批要求完全不同。"
  failure_mode: |
    业务语义不同的动作（创建变更单 vs 生产发布）合并为一个粗粒度工具，无法分别授权、审批、审计、重试与验证，低风险授权隐式携带高风险能力。
  mechanism: |
    策略以能力为粒度匹配；粗粒度工具使 Policy 无法区分风险等级，对"创建"的 ALLOW 被连带授予"发布"。
  warning_signs:
    - 一个工具同时触发多种副作用且共享一个权限点
    - 审批单上看不出实际执行了哪类操作
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, tool-granularity, approval]

- id: ce-32
  title: 让模型通过通用 HTTP 或 Shell 自行拼接生产操作
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.3）
  source_quote: |
    "企业 Tool 设计应优先暴露这种业务语义，而不是让模型通过通用 HTTP 或 Shell 自行拼接生产操作。"
  failure_mode: |
    不提供业务语义化工具，给模型通用 HTTP/Shell 通道自行拼接请求，参数、目标与身份全部失去校验点，SSRF 与误操作直达生产。
  mechanism: |
    通用通道没有 Schema、目标白名单与语义校验；确定性代码能做的参数校验、影响范围判定全部退化为模型自由生成。
  warning_signs:
    - 生产操作经通用 shell/http 工具发起
    - 工具清单里只有 compute 类通用能力
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, generic-tool, production]

- id: ce-33
  title: Action Result 只有模型可读文本
  type: counter-example
  source_chapter: 6.1 Harness Action Plane（6.1.2）
  source_quote: |
    "Action Result 不应只有模型可读文本"
  failure_mode: |
    行动结果不含结构化状态、错误类别、可重试性、副作用标记、执行身份与成本，任务状态机与审计、Verifier 都失去依据。
  mechanism: |
    Action 生命周期（REQUESTED→…→SUCCEEDED/FAILED）与补偿、幂等、审计依赖结构化结果；只有文本时进程只能靠解析模型输出猜测行动结果。
  warning_signs:
    - 任务状态推进靠解析自然语言
    - 失败后不知是否已产生副作用，无法决定重试或补偿
  bound_to:
    - "行动契约设计"
    - "可观测接入"
  tags: [counter-example, action-result, observability]

- id: ce-34
  title: Trace 只记录模型输入输出
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.1）
  source_quote: |
    "Agent 的最终质量来自模型与 Harness 的组合，问题可能发生在 Context、计划、工具、权限、环境、状态或验证任一环节。Trace 因而不能只记录模型输入输出。"
  failure_mode: |
    可观测只覆盖模型调用，Context 决策、Plan 变更、Action 授权、工具结果、验证证据之间没有因果关联，问题无法定位到环节。
  mechanism: |
    一次模型决策用了哪份 Context、产生了哪个 Action、更新了哪些状态、触发了哪次验证，需要因果链关联；纯 IO 日志只有时间序没有因果。
  warning_signs:
    - 排障时需要在多个系统间人肉拼接时间线
    - 无法回答"这次任务用了哪个 Agent 版本/哪份 Context"
  bound_to:
    - "可观测接入"
    - "调优归因判据"
  tags: [counter-example, trace, causality]

- id: ce-35
  title: Build 期不埋事件与关联 ID，指望治理平台后补
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.1）
  source_quote: |
    "如果 Build 时没有稳定事件、版本和关联 ID，后续平台无法补出可信的 Agent Trace。"
  failure_mode: |
    观测埋点（Span、属性、关联 ID、版本绑定）被视为上线后平台的事，构建期缺失后任何平台都无法重建可信 Trace。
  mechanism: |
    因果关联与版本绑定必须在事件发生时写入；事后聚合无法凭空恢复缺失的关联，只能得到不可信的时间线日志。
  warning_signs:
    - Loop 阶段/Middleware/Tool/Verifier 没有定义观察单元
    - 上线后才招标观测平台
  bound_to:
    - "可观测接入"
  tags: [counter-example, instrumentation, build-time]

- id: ce-36
  title: Evaluation 不固定运行条件，分数差异来自环境
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "Evaluation Harness 必须固定或披露模型、推理设置、Harness、工具版本、预算、重试、环境和评分规则。否则两个版本的分数差异可能来自运行条件，而不是所评估的 Harness Patch。"
  failure_mode: |
    版本比较时不固定模型、推理设置、工具版本、预算、重试、环境与评分规则，把运行条件差异误读为补丁效果。
  mechanism: |
    Agent 行为是组合函数；比较时任何未固定的输入都会污染因果归因，得出"改进"或"退化"的虚假结论。
  warning_signs:
    - 评估报告不披露运行条件版本
    - 两次评估之间悄悄换过模型或工具版本
  bound_to:
    - "黄金集构建"
    - "调优归因判据"
  tags: [counter-example, evaluation, controlled-comparison]

- id: ce-37
  title: 把昂贵的发布评估器直接嵌入每次线上任务
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "Verifier 可以成为 Evaluation 的数据来源……但不应把昂贵的发布评估器直接嵌入每次线上任务。"
  failure_mode: |
    混淆单次完成门禁（Verifier）与跨版本评估（Evaluation）两套系统，把发布级评估器塞进每次线上执行，成本与延迟爆炸。
  mechanism: |
    两套系统职责不同：Verifier 关注单任务可交付底线应轻量内嵌；Evaluation 面向多样本版本比较应离线一致运行。错位部署让任一端都做不好。
  warning_signs:
    - 每次线上任务触发多模型评审
    - 线上延迟和成本被评估逻辑主导
  bound_to:
    - "任务契约设计"
    - "黄金集构建"
  tags: [counter-example, verifier-vs-evaluation]

- id: ce-38
  title: 只评最终结果或只评单步
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "只评最终结果可能掩盖高成本或高风险轨迹；只评单步又可能惩罚有效探索。"
  failure_mode: |
    评测对象只选一层（最终结果或单步），高成本/高风险轨迹被结果掩盖，或有效探索被单步规则惩罚。
  mechanism: |
    三层对象（单步/轨迹/最终结果）各自可见不同的病：轨迹层才能看到绕路、重复、越权、过度消耗；单层评估结构性地盲于其他两层问题。
  warning_signs:
    - 成功率正常但成本异常
    - 评估器惩罚了"先探索后收敛"的合理行为
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, evaluation-layers]

- id: ce-39
  title: 从单条失败直接改 Prompt
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "效果闭环不应从单条失败直接改 Prompt。"
  failure_mode: |
    看到单条失败案例立即修改 Prompt/配置，没有失败聚类与根因诊断，补丁相互冲突且引入新的退化。
  mechanism: |
    稳健闭环是 Trace+Outcome→失败聚类→诊断（Model/Context/State/Tool/Policy/Environment/Loop）→最小针对性变更→回归→发布门禁；跳过诊断使每次补丁成为无对照实验。
  warning_signs:
    - Prompt 频繁小改且无回归验证
    - 修好一类案例又坏另一类
  bound_to:
    - "Badcase 闭环"
    - "调优归因判据"
  tags: [counter-example, prompt-patching, feedback-loop]

- id: ce-40
  title: 越权事件后往 Prompt 里加一句"谨慎发布"
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.3）
  source_quote: |
    "正确的 Patch 不是在 Prompt 中再增加一句谨慎发布，而是拆分 change.create 与 production.deploy，让生产发布策略校验审批类型、对象版本、目标环境和短时授权"
  failure_mode: |
    高风险越权问题的修复方式是追加 Prompt 警告，而不去拆分能力、强化策略校验与回归用例，同类越权必然复发。
  mechanism: |
    越权根因在权限模型/策略实现层；Prompt 警告是概率性软约束，攻击或语境漂移即可绕过，且无回归用例锁定行为。
  warning_signs:
    - 安全修复以"加了句提示"收尾
    - 同类越权事件重复出现
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
    - "Badcase 闭环"
  tags: [counter-example, security-patch, privilege-escalation]

- id: ce-41
  title: Policy 仅按 Tool 名称匹配就返回 ALLOW
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.3 案例）
  source_quote: |
    "Policy Decision └── 仅按 Tool 名称匹配，错误返回 ALLOW"
  failure_mode: |
    权限策略只匹配工具名，不校验审批类型、对象版本、目标环境与授权时效，把用户对草稿的同意错误解释为允许生产发布。
  mechanism: |
    授权语义存在动作与对象两个维度；只看动作名的策略无法区分"同意生成变更单"与"同意发布生产"，语义滑移直接变成越权放行。
  warning_signs:
    - 策略规则里只有工具名条件
    - 审批记录与执行动作之间无类型/对象绑定
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, policy-matching, authorization]

- id: ce-42
  title: 把人工反馈当天然真值
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "人工反馈也不是天然真值，需要区分用户偏好、业务结果和操作便利性，并与环境证据结合解释。"
  failure_mode: |
    直接用点赞/投诉等人工反馈作为评估真值，混淆用户偏好、业务结果与操作便利性，优化方向被偏好噪声带偏。
  mechanism: |
    反馈反映多类混合信号且存在采样偏差；不与环境证据（测试、业务查询）交叉解释时，无法区分"真的没完成"与"用户不喜欢答案形式"。
  warning_signs:
    - 评估指标完全由用户评分构成
    - 高分任务在业务侧无对应结果
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, human-feedback, ground-truth]

- id: ce-43
  title: 模型升级后不清理旧 Harness 补偿逻辑
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "模型升级后，还应重新检查旧 Harness 中的补偿逻辑，删除已经失效或阻碍新模型的规则。"
  failure_mode: |
    模型升级时保留全部旧补偿规则，已失效或阻碍新模型的规则继续生效，新模型能力被旧脚手架压制。
  mechanism: |
    补偿逻辑针对旧模型缺陷设计；新模型行为分布变化后，同样的规则从"补丁"变成"错误先验"。
  warning_signs:
    - 升级模型后效果不及预期且无故障
    - 编排层规则多年无人审视
  bound_to:
    - "调优归因判据"
    - "模型调优路线"
  tags: [counter-example, compensation-logic, model-upgrade]

- id: ce-44
  title: 回归集缺少相邻正常用例与安全对抗用例
  type: counter-example
  source_chapter: 6.6 Observability、Evaluation 与效果闭环（6.6.2）
  source_quote: |
    "回归集必须同时包含原失败用例、相邻正常用例和安全对抗用例，防止局部补丁损害其他任务。"
  failure_mode: |
    修复验证只重跑原失败用例，不跑相邻正常用例与对抗样本，局部补丁损害其他任务或安全修复让所有任务陷入无效审批而不被发现。
  mechanism: |
    补丁的作用范围大于故障点；无对照组与对抗组的回归在统计上只能确认"原问题消失"，无法确认"没有引入新问题"。
  warning_signs:
    - 回归集只有失败样本的重放
    - 安全修复上线后审批量异常飙升却无人过问
  bound_to:
    - "Badcase 闭环"
    - "上线前仿真"
  tags: [counter-example, regression-suite, safety]

## 第 11 章 多 Agent 团队

- id: ce-45
  title: Agent 持有或复用用户的长期凭证
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.1/11.7.2）
  source_quote: |
    "Agent 不得持有或复用用户的长期凭证，也不得超出本次任务范围扩大访问权限。"
  failure_mode: |
    把用户长期凭证（密码、AK/SK、Token）直接交给 Agent 使用，一次泄露即长期有效，且审计无法还原从发起人到执行工作负载的责任链。
  mechanism: |
    凭证只是证明载体而非身份；长期凭证缺少范围、时段与到期约束，Agent 被攻破或行为越界时没有收权手段。正确做法是主体/委托/凭证三者分离 + 临时凭证。
  warning_signs:
    - Agent 配置中出现用户级长期密钥
    - 审计里执行身份显示为某个人的账号
    - 用户离职/改密后 Agent 大面积失败
  bound_to:
    - "Agent 安全防护"
    - "异步与多Agent协作"
  tags: [counter-example, credentials, identity]

- id: ce-46
  title: 权限只在入口检查一次
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.2）
  source_quote: |
    "在每次实际访问前重新决策，禁止只在入口检查。"
  failure_mode: |
    权限只在任务入口校验一次，长任务运行中环境、数据等级、委托时效变化后不再复查，越权访问发生在入口检查之后。
  mechanism: |
    有效权限是人员权限、团队边界、Agent 策略、资源策略与委托范围的交集，且随上下文动态变化；一次入口检查把动态交集冻结成静态快照。
  warning_signs:
    - 长任务执行中委托已过期仍继续访问
    - 资源访问记录中没有逐次策略决策
  bound_to:
    - "行动契约设计"
    - "Agent 安全防护"
  tags: [counter-example, permission, per-call-check]

- id: ce-47
  title: 调用者能访问 Agent 就默认 Agent 能访问调用者的全部资源
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.2）
  source_quote: |
    "不得因调用者能够访问 Agent，就默认 Agent 可以访问调用者拥有的全部资源。"
  failure_mode: |
    入站权限（谁能调用 Agent）与出站权限（Agent 能代表谁访问什么）不分离，调用者身份隐式转换为 Agent 的全部出站能力，形成权限放大通道。
  mechanism: |
    入站鉴权只证明调用者有权发起请求，不构成出站授权；两类权限独立配置、以任务和委托关联后，出站边界不得超过入站请求形成的授权边界。
  warning_signs:
    - Agent 出站能力等于其所有调用者权限的并集
    - 出站访问没有对应委托关系记录
  bound_to:
    - "Agent 安全防护"
    - "异步与多Agent协作"
  tags: [counter-example, inbound-outbound, privilege-escalation]

- id: ce-48
  title: 校验失败降级为共享账号或平台默认权限
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.2）
  source_quote: |
    "任一环节的身份、授权或策略校验失败，都应拒绝执行，不得降级为共享账号或平台默认权限。"
  failure_mode: |
    身份/授权/策略校验失败时为"保可用性"降级到共享账号或默认权限继续执行，责任链断裂且权限模型被绕过。
  mechanism: |
    Fail-open 设计把校验失败当作可忽略错误；一旦降级路径存在，攻击者只需制造校验故障即可获得默认权限。
  warning_signs:
    - 存在"鉴权异常时使用默认身份"的兜底逻辑
    - 审计记录中出现无主体归属的操作
  bound_to:
    - "Agent 安全防护"
  tags: [counter-example, fail-open, shared-account]

- id: ce-49
  title: Agent 间交接扩大权限
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.2）
  source_quote: |
    "Agent 间交接不会扩大权限：接收方只能获得当前任务所需且双方都被允许的最小范围。"
  failure_mode: |
    多 Agent 协作中任务交接时传递全部上下文与凭证，接收方获得超出任务所需或超出双方授权范围的能力。
  mechanism: |
    交接的授权边界应取交集（任务所需 ∧ 双方均被允许）；全量传递使单个低权限 Agent 可借委派获得高权限 Agent 的能力。
  warning_signs:
    - 交接时转发完整凭证与全部工具访问
    - 接收方调用了发起方都无权使用的资源
  bound_to:
    - "异步与多Agent协作"
    - "Agent 安全防护"
  tags: [counter-example, delegation, least-privilege]

- id: ce-50
  title: 把动态执行图强行简化成一棵调用树
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.3）
  source_quote: |
    "这样既保留真实因果，也避免将动态执行图强行简化成一棵调用树。"
  failure_mode: |
    观测系统强制把跨 Trace、并行汇合的多 Agent 执行压成单一父子树，因果失真，无法定位真实的责任交接与阻塞点。
  mechanism: |
    动态执行图需要 Span Links 等多关联机制；强行树化会虚构不存在的父子关系、丢失并行与恢复的真实结构。
  warning_signs:
    - Trace 拓扑与实际任务流程对不上
    - 长程任务被强制塞进单条 Trace 导致关联丢失
  bound_to:
    - "可观测接入"
    - "异步与多Agent协作"
  tags: [counter-example, tracing, execution-graph]

- id: ce-51
  title: 把模型评审结论当作不可质疑的运行事实
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.3）
  source_quote: |
    "评估结论应与事实记录分开保存，明确评估方法、版本和时间，避免将模型判断视为不可质疑的运行事实。"
  failure_mode: |
    模型评审/评估结果与事实记录混存且无版本标注，后续排障与审计把模型判断当作权威事实使用。
  mechanism: |
    模型评审是带方法与版本偏差的二手判断；与事实同层存储后，评审误差会被下游当作证据引用并扩散。
  warning_signs:
    - 质量分数字段直接写进业务事实表
    - 无人能说清某条结论来自规则还是模型评审
  bound_to:
    - "可观测接入"
    - "黄金集构建"
  tags: [counter-example, evaluation-vs-fact]

- id: ce-52
  title: 日志直接记录凭证、完整提示词与敏感数据
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.3）
  source_quote: |
    "日志应保留必要的诊断上下文，但不直接记录凭证、完整提示词、敏感数据或未经处理的模型输出。"
  failure_mode: |
    为诊断方便在日志/Trace 中明文记录凭证、完整 Prompt、敏感数据与原始输出，可观测性建设本身成为新的敏感数据暴露渠道。
  mechanism: |
    观测数据的访问控制弱于业务数据（受众更广、保留更久）；未按数据等级做摘要化、脱敏、采样与分级授权时，泄露面随观测建设同步扩大。
  warning_signs:
    - 排障手册里教人从日志里复制密钥
    - 观测平台未设敏感字段分级
  bound_to:
    - "可观测接入"
    - "Agent 安全防护"
  tags: [counter-example, sensitive-data, observability]

- id: ce-53
  title: 拒绝结果只返回通用的"无权限"
  type: counter-example
  source_chapter: 11.7 团队治理与可观测（11.7.2）
  source_quote: |
    "拒绝结果需要返回可理解的原因，例如缺少角色、委托已过期、资源超出团队边界或动作需要审批，而不是只返回通用的"无权限"。"
  failure_mode: |
    权限拒绝只返回笼统错误，调用方与模型都无法区分缺角色、委托过期、越边界还是待审批，任务恢复与排障低效，并诱发放宽权限的压力。
  mechanism: |
    拒绝原因是恢复路径的输入（补审批/换身份/缩范围）；信息缺失使每次拒绝都变成人工工单，团队倾向用提权而不是修复来消除摩擦。
  warning_signs:
    - 大量"无权限"工单无法自助解决
    - 为绕过摩擦出现共享高权限账号
  bound_to:
    - "行动契约设计"
    - "可观测接入"
  tags: [counter-example, denial-reason, usability]

## 第 14 章 安全：防护与管控

- id: ce-54
  title: 只防"不被打穿"，不管"不越边界"
  type: counter-example
  source_chapter: 14.1 Agent 安全风险与挑战
  source_quote: |
    "Agent 安全既要解决"不被打穿"，更要解决"不越边界"。"
  failure_mode: |
    安全建设只做防护（防外部攻击），不做管控（身份鉴权、意图识别、逐次调用校验、高危二次授权、数据出域阻断），未被攻击的 Agent 也因越界行为造成损害。
  mechanism: |
    Agent 既是被攻击对象也是行为主体：持有身份权限、自主调用工具、接触敏感数据；只设盾不配缰绳时，自主性越大损失越大。IBM 调研中发生 AI 安全事件的机构 92% 缺乏适当的 AI 访问控制。
  warning_signs:
    - 安全方案里没有身份/授权/逐次校验内容
    - 把 WAF/漏洞扫描当作 Agent 安全全部
  bound_to:
    - "Agent 安全防护"
  tags: [counter-example, security-scope, control]

- id: ce-55
  title: 外部内容间接污染（攻击者不必直接提交请求）
  type: counter-example
  source_chapter: 14.2.2 新的攻击面
  source_quote: |
    "即使攻击者无法直接向 Agent 提交请求，只要能够影响其将要读取的内容，就可能间接影响业务执行。"
  failure_mode: |
    把网页、邮件、文档、图片识别结果、检索记录仅当作"待处理数据"，忽略其能改变 Agent 下一步行动，攻击者通过污染只读内容间接操纵业务。
  mechanism: |
    Agent 把读取内容接入规划-工具选择-执行链路后，数据即指令的注入面从用户输入扩展到一切被读取内容；数据与指令未分离时污染自动转化为行动。
  warning_signs:
    - 外部内容没有来源标记，与任务指令同层处理
    - Agent 行为异常出现在抓取/检索特定来源之后
  bound_to:
    - "Agent 安全防护"
    - "信息契约设计"
  tags: [counter-example, indirect-prompt-injection, untrusted-input]

- id: ce-56
  title: 工具描述与实际行为偏离（两层工具欺骗）
  type: counter-example
  source_chapter: 14.2.2 新的攻击面
  source_quote: |
    "这里存在两层风险：Agent 对工具用途的理解被误导，以及工具实际执行的行为偏离其声明。"
  failure_mode: |
    MCP 服务、连接器、技能配置与工具注册信息被视为可信描述，攻击者操纵工具描述/返回内容或工具实现夹带额外操作，误导 Agent 的工具选择与参数构造。
  mechanism: |
    Agent 依据名称、描述、参数定义和返回结果选择动作，这些元数据本身成为干预入口；供应链与注册信息未纳入治理时"声明"与"行为"可以任意偏离。
  warning_signs:
    - 工具/连接器接入与更新无安全评估
    - 工具返回内容中夹带行为指令
  bound_to:
    - "Agent 安全防护"
    - "资产注册发现"
  tags: [counter-example, tool-poisoning, supply-chain]

- id: ce-57
  title: Agent 生成的 SQL/HTML/参数被下游直接解释执行
  type: counter-example
  source_chapter: 14.2.1 背景和挑战
  source_quote: |
    "Agent 生成的 SQL、HTML 或工具参数，如果被下游应用直接解释执行，也可能触发 SQL 注入、XSS 等传统漏洞。"
  failure_mode: |
    下游服务信任 Agent 生成的动态参数（URL、查询条件、请求正文）直接执行，Agent 成为 SQL 注入/XSS/SSRF/业务逻辑滥用的新入口。
  mechanism: |
    Agent 的输出是概率生成的自由内容；传统应用假定输入来自受控客户端，缺少参数化查询、对象校验与目标白名单时，模型幻觉与注入同样能穿透。
  warning_signs:
    - 数据库访问拼接 Agent 生成的 SQL
    - 网络请求不校验实际连接地址与重定向
  bound_to:
    - "Agent 安全防护"
    - "行动契约设计"
  tags: [counter-example, sqli, xss, ssrf]

- id: ce-58
  title: 拒绝钱包攻击（DoW）——每次调用合规、累计成本失控
  type: counter-example
  source_chapter: 14.2.2 新的攻击面
  source_quote: |
    "这类拒绝钱包攻击（Denial of Wallet，DoW）及可用性风险发生在完整执行过程中，即使每次调用都符合接口限制、最终回答也正常，累计消耗仍可能远超任务的合理成本。"
  failure_mode: |
    只按单次请求限制防护，攻击者通过制造补充工作、失败反馈和新的依赖让任务持续消耗 Token、付费 API、并发槽位与下游容量，账单被拖垮而监控全程正常。
  mechanism: |
    自主执行中的重试、递归委派与并行调用是资源放大器；单请求维度的限流对任务级累计消耗不可见，需要任务级预算与异常行为阻断。
  warning_signs:
    - 单任务成本分布出现极端长尾
    - 失败重试与委派深度无上限
  bound_to:
    - "Agent 安全防护"
    - "网关三语义"
  tags: [counter-example, denial-of-wallet, cost-attack]

- id: ce-59
  title: 外部数据经摘要转述后被当作新的执行要求
  type: counter-example
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "应保留来源信息，将其与任务指令分开处理，避免外部数据经过摘要、转述或多次调用后被当作新的执行要求。"
  failure_mode: |
    外部内容不带来源标记地进入摘要/记忆/上下文复用链，多轮之后被模型提升为任务指令执行，注入指令"洗白"为内部要求。
  mechanism: |
    摘要与转述会剥离来源上下文；指令与数据不分离时，时间与轮次让外部内容的"外来性"消失，防护端无法再区分其出身。
  warning_signs:
    - Agent 突然执行从未下达过的操作且引用"之前的要求"
    - 摘要/记忆对象不携带来源元数据
  bound_to:
    - "信息契约设计"
    - "Agent 安全防护"
  tags: [counter-example, injection-laundering, provenance]

- id: ce-60
  title: 只做静态检测，不审查工具实际行为是否符合声明
  type: counter-example
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "通过代码安全检测和组件分析可以辅助发现缺陷，但对工具是否夹带额外操作、实际行为是否符合声明，仍需要审查和验证。"
  failure_mode: |
    工具/MCP 接入只跑代码安全检测与组件分析，不验证运行时实际行为与声明一致，夹带的额外操作（外发数据、隐蔽调用）不被发现。
  mechanism: |
    静态检测发现的是已知缺陷模式；声明与行为的偏离是语义问题，需要在接入与更新时以实际调用审查和验证兜底。
  warning_signs:
    - 工具上线流程只有 SAST/SCA
    - 无工具行为的运行时比对或抽检
  bound_to:
    - "Agent 安全防护"
    - "资产注册发现"
  tags: [counter-example, tool-verification, runtime]

- id: ce-61
  title: 加密通信与 SaaS 操作只依赖网络流量判断
  type: counter-example
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "对加密通信和 SaaS 内部操作，还需补充应用侧日志，避免只依赖网络流量判断。"
  failure_mode: |
    行为监测全部建立在网络流量观测上，加密流量与 SaaS 平台内部操作不可见，数据外发与越权操作在盲区发生。
  mechanism: |
    网络层只能看到连接元数据；TLS 加密与第三方托管平台把 payload 与操作细节移出网络观测范围，必须由应用侧日志补全。
  warning_signs:
    - 安全告警里没有任何 SaaS 操作事件
    - 出站检测只有域名级记录
  bound_to:
    - "Agent 安全防护"
    - "可观测接入"
  tags: [counter-example, blind-spot, encrypted-traffic]

- id: ce-62
  title: 依赖"提示词加密 + 模型拒答"防蒸馏/数据泄露
  type: counter-example
  source_chapter: 14.4.3 数据安全保障（数据传输）
  source_quote: |
    "仅依赖提示词加密叠加模型本身的拒答能力来防止模型蒸馏等数据泄漏方式的方案是存在先天缺陷的。生成式模型面对相同提示词攻击的拒答能力并不稳定，一定概率会指令遵循失败。"
  failure_mode: |
    把防数据泄露寄托在传输加密加模型自身拒答上，攻击者用大模型返回的加密令牌去询问同系列小尺寸弱安全模型绕过拒答，语料仍被系统性抽出。
  mechanism: |
    拒答是概率行为且同系列小模型安全对齐更弱；加密保护信道不保护模型输出端的服从性，需要第三方专业防护能力补充。
  warning_signs:
    - 安全方案中"模型会拒绝"是关键防线
    - 未部署独立的提示词反爬/护栏层
  bound_to:
    - "Agent 安全防护"
  tags: [counter-example, distillation-attack, refusal]

- id: ce-63
  title: AI 权限过大导致误删重要数据
  type: counter-example
  source_chapter: 14.4.3 数据安全保障（数据删除）
  source_quote: |
    "错误的提示词引导会导致AI错误删除重要数据，即使一些AICoding产品如Qoder本身当感知到删除操作时会提示风险，人类仍然可能惯性误点。世界范围内已陆续有不少此类AI误删重要数据的报道。"
  failure_mode: |
    AI 拥有过大或自主提升的数据操作权限，错误提示词引导其删除关键数据；删除确认弹窗被人类惯性点掉，且攻击者会刻意利用 AI 执行脱库。
  mechanism: |
    AI 替代人操作的占比快速攀升，而删除类操作的确认机制依赖人的注意力；权限过大 + 确认疲劳 + 恶意引导三者叠加时，最后防线应在关键数据节点引入第三方告警。
  warning_signs:
    - Agent 拥有删除/批量导出权限且无二次校验
    - 高频确认弹窗导致人工麻木
  bound_to:
    - "Agent 安全防护"
    - "行动契约设计"
  tags: [counter-example, destructive-action, over-privilege]

- id: ce-64
  title: 影子 Agent 资产黑箱
  type: counter-example
  source_chapter: 14.5.1 身份安全背景与挑战
  source_quote: |
    "资产黑箱：很多企业不知道内部有多少 Agent、谁创建的、调用了哪些资源、拥有什么权限。"
  failure_mode: |
    企业不知道内部有多少 Agent、谁创建、调用哪些资源、持有什么权限，安全事件发生时无法定位与收权，影子 Agent 游离于管理之外。
  mechanism: |
    Agent 创建门槛低于传统应用，平台自动创建与手动创建并存；没有统一身份注册与资产清单时，权限授予不可枚举也就不可治理。
  warning_signs:
    - 无法回答"我们现在有多少 Agent 在跑"
    - 员工自建 Agent 接入生产资源无登记
  bound_to:
    - "资产注册发现"
    - "Agent 安全防护"
  tags: [counter-example, shadow-agents, inventory]

- id: ce-65
  title: 权限逃逸与离职权限残留
  type: counter-example
  source_chapter: 14.5.1 身份安全背景与挑战
  source_quote: |
    "员工可能通过 Agent 间接获得超出自身岗位的权限，例如普通销售借助"报销助手"查看 CEO 差旅明细；离职员工的 Agent 可能继续运行，造成权限残留。"
  failure_mode: |
    员工通过 Agent 间接获得超出岗位的权限；员工离职转岗后其创建或授权的 Agent 继续运行，权限残留持续存在。
  mechanism: |
    Agent 成为权限的中间层后，岗位权限约束不再随路径传递；Agent 生命周期与人员生命周期未挂钩时，授权在人员失效后仍然存活。
  warning_signs:
    - Agent 能访问的数据超出任何使用者岗位范围
    - 离职流程不触发 Agent 权限回收
  bound_to:
    - "Agent 安全防护"
    - "资产注册发现"
  tags: [counter-example, privilege-escalation, lifecycle]

- id: ce-66
  title: 万能权限与硬编码长期密钥
  type: counter-example
  source_chapter: 14.5.1 身份安全背景与挑战
  source_quote: |
    "只为 Agent 配置一个应用账号或长期密钥，无法回答"谁在行动、代表谁行动、凭什么行动、为什么此刻允许、发生异常后如何收权"。"
  failure_mode: |
    给 Agent 配一个应用账号或长期密钥当作身份，权限"万能"、Key 硬编码、长期有效且无法审计，异常后没有收权手段。
  mechanism: |
    传统 IAM 没有为非人身份设计动态凭据与短周期令牌；长期静态凭证把"认证"与"授权决策"合并成一个不可撤销的整体。
  warning_signs:
    - 代码/配置里出现硬编码 AK/SK
    - 一个 Key 被多个 Agent 与环境共用
  bound_to:
    - "Agent 安全防护"
  tags: [counter-example, hardcoded-keys, over-privilege]

- id: ce-67
  title: Agent 成为聚合全部下游权限的"超级应用"
  type: counter-example
  source_chapter: 14.5.3 身份授权治理
  source_quote: |
    "这避免了 Agent 成为"超级应用"，即使 Agent 被攻破，泄露的也只是对单一服务的短期、受限令牌。"
  failure_mode: |
    Agent 持有对全部下游服务的聚合长期权限，被攻破一次即横向拿下所有系统；权限不按调用逐服务收敛。
  mechanism: |
    Token Exchange 让每次出站调用换发仅含最小 scope 的新令牌并保留调用链；不做权限收敛时，单点沦陷等于全域沦陷。
  warning_signs:
    - Agent 的出站凭证能访问所有对接系统
    - 出站授权规则不分服务不分 scope
  bound_to:
    - "Agent 安全防护"
    - "网关三语义"
  tags: [counter-example, blast-radius, token-exchange]

- id: ce-68
  title: 态势管理只解决"看得见"，不与运行时管控联动
  type: counter-example
  source_chapter: 14.6.2 基础设施的统一安全态势管理
  source_quote: |
    "态势管理解决的是"看得见"的问题，发现的风险还必须与运行时的管控手段联动闭环"
  failure_mode: |
    资产盘点与风险扫描产出了告警清单，但不联动网关、防火墙与任务编排层执行限流、隔离或暂停，风险持续暴露无处置。
  mechanism: |
    发现与处置分离时，处置依赖人工排期；高危漏洞、异常暴露或行为失真的工作负载在窗口期内持续可被利用。
  warning_signs:
    - 风险工单积压且无自动阻断
    - 高危 Agent 工作负载照常运行
  bound_to:
    - "Agent 安全防护"
    - "可观测接入"
  tags: [counter-example, posture-management, closed-loop]

- id: ce-69
  title: Agent 运行环境不按不可信负载做会话级隔离
  type: counter-example
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "Agent 在执行任务时会动态生成并运行代码、访问外部服务，其运行环境本身需要被当作不可信负载对待。"
  failure_mode: |
    把 Agent 当普通服务部署，隔离单位停留在服务/容器级，残留环境与凭据被后续任务或攻击者复用，单会话被攻陷横向波及。
  mechanism: |
    Agent 动态生成并运行代码，行为不可预知；会话级 microVM（内核级隔离、用完即弃、超时回收、按会话注入最小临时凭据）才能切断复用链。
  warning_signs:
    - 多个任务共享同一沙箱与凭据
    - 空闲环境长期存活不回收
  bound_to:
    - "运行环境设计"
    - "Agent 安全防护"
  tags: [counter-example, sandbox, session-isolation]

- id: ce-70
  title: Agent 出站流量默认允许
  type: counter-example
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "出站方向应默认拒绝、按需放行……2026 年 7 月 Hugging Face 入侵事件中，攻击 Agent 正是借助可访问的中间服务代联网突破了网络边界。"
  failure_mode: |
    只防入站不防出站，Agent 可任意访问互联网与 MCP 服务；被诱导的 Agent 借可访问的中间服务（如 Artifactory）代联网突破网络边界。
  mechanism: |
    Agent 是主动外联的主体；出站默认开放等于给任意注入攻击提供了数据外传与横向跳板通道，应按域名显式放行并经统一出站关口收敛。
  warning_signs:
    - Agent 子网无出站策略
    - 出现经内部服务转发的异常外联
  bound_to:
    - "Agent 安全防护"
    - "运行环境设计"
  tags: [counter-example, egress, network-boundary]

- id: ce-71
  title: 未封禁云元数据服务端点与内网保留地址
  type: counter-example
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "在网络层封禁云元数据服务端点与内网保留地址段，防止 SSRF 与提示注入演变为内网探测和凭据窃取。"
  failure_mode: |
    Agent 可达云元数据服务（169.254.169.254 等）与内网保留段，SSRF 或提示注入直接升级为内网探测与临时凭据窃取。
  mechanism: |
    元数据服务返回实例级临时凭证；Agent 生成 URL 的能力使其成为 SSRF 的理想载体，网络层不封禁时应用层校验形同虚设。
  warning_signs:
    - 出站策略未显式封禁元数据地址
    - 检测到对保留地址段的扫描
  bound_to:
    - "Agent 安全防护"
    - "运行环境设计"
  tags: [counter-example, ssrf, metadata-endpoint]

- id: ce-72
  title: 多 Agent 协作无相互隔离，单点沦陷横向波及
  type: counter-example
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "防止单个 Agent 被攻陷后横向波及其他 Agent 及其可访问的数据与工具。"
  failure_mode: |
    多 Agent 共享运行环境、网络与工具凭证且无相互隔离，一个低防护 Agent 被攻陷后横向渗透整个团队的数据与工具面。
  mechanism: |
    协作需要连通性，攻击者同样利用连通性；按任务边界划分运行环境与网络策略才能把爆炸半径限制在单 Agent 内。
  warning_signs:
    - 团队内 Agent 共享同一凭证与网络域
    - 无按任务边界的网络策略
  bound_to:
    - "异步与多Agent协作"
    - "Agent 安全防护"
  tags: [counter-example, lateral-movement, multi-agent]

## 第 17 章 模型调优

- id: ce-73
  title: 失败现象本身直接决定归因
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述
  source_quote: |
    "失败现象本身不能直接决定归因。例如，工具选错既可能源于模型能力不足，也可能源于工具描述含混。"
  failure_mode: |
    看到工具选错、重复执行等表象就直接归因于模型并启动模型优化，实际根因在工具描述、上下文供给或执行环境。
  mechanism: |
    模型、Harness、执行环境三层的失败现象高度相似；现象与根因是多对多关系，必须用"固定两层、验证一层"的对照实验判定，而非直觉映射。
  warning_signs:
    - 归因结论没有验证实验支撑
    - 同一现象在不同任务上反复出现且修复无效
  bound_to:
    - "调优归因判据"
  tags: [counter-example, attribution]

- id: ce-74
  title: 用模型训练弥补系统工程问题
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述
  source_quote: |
    "实际排查时，应先排除能够独立复现的执行环境故障，再检查 Harness 是否提供了充分的信息和可靠的运行控制，最后判断是否属于模型能力缺口。这个顺序……是为了避免用模型训练弥补本应由系统工程解决的问题。"
  failure_mode: |
    跳过"先环境→再 Harness→后模型"的归因次序，直接训练/更换模型去修环境故障或 Harness 信息缺陷，代价最高且无法消除根因。
  mechanism: |
    模型训练改变的是条件行为分布，修不了"信息没进上下文、工具协议含混、服务不可用"这类供给问题；训练只能在缺陷条件下过拟合表象，环境稍变即失效。
  warning_signs:
    - 必要信息缺失/工具协议含混/状态不可见尚未排查
    - 数据分析 Agent 反复生成错误 SQL 但字段含义从未供给
  bound_to:
    - "调优归因判据"
    - "模型调优路线"
  tags: [counter-example, attribution-order, costliest-misjudgment]

- id: ce-75
  title: 把 Prompt 适配、权限放宽带来的收益归因于模型
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述
  source_quote: |
    "若候选模型需要专门的提示或协议适配，应将适配纳入对应配置记录，避免将组合变化全部归因于模型。"
  failure_mode: |
    候选模型配套大幅 Prompt 扩写、额外重试或放宽工具权限后取得高分，把组合变更的收益全部记在模型能力上。
  mechanism: |
    收益来自模型×适配的组合；不区分记录时，后续系统条件回收（权限收紧、Prompt 精简）会让"模型优势"凭空消失，决策依据失真。
  warning_signs:
    - 模型对比实验中 Prompt/权限/重试也被修改
    - 报告只有端到端总分无配置差异说明
  bound_to:
    - "调优归因判据"
    - "模型调优路线"
  tags: [counter-example, confounding, attribution]

- id: ce-76
  title: 放弃困难任务形成的表面成本下降
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述（17.1.3）
  source_quote: |
    "比较时应固定任务构成并同时报告成功率，避免因放弃困难任务而形成表面上的成本下降。"
  failure_mode: |
    成本比较时不固定任务构成、不同时报告成功率，通过少做/拒做困难任务制造的"单位成本下降"被当作优化收益。
  mechanism: |
    单位成功任务成本的分子是全部任务成本、分母是成功数；不固定任务分布时，难度结构变化即可操纵指标而无任何真实改进。
  warning_signs:
    - 成本下降的同时成功率或任务覆盖也在变化
    - 新版本"恰好"不再接受最难的任务
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, cost-metrics, gaming]

- id: ce-77
  title: 假设模型训练必然降低成本
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述（17.1.3）
  source_quote: |
    "SFT 只有在减少提示长度、降低重试与复核次数，或使更小模型能够胜任目标任务时，才可能降低端到端成本；否则，数据准备、训练、评测、发布和维护的一次性投入可能抵消运行节省。"
  failure_mode: |
    以"效率驱动"为由启动 SFT/蒸馏，未核算数据、训练、评测、发布、维护的一次性投入与任务频率，最终总成本不降反升。
  mechanism: |
    训练节省的是边际执行成本，收益需乘以任务量；低频或变化快的任务下 Harness 轻量调整更经济，一次性投入无法摊薄。
  warning_signs:
    - 没有按任务量计算盈亏平衡点
    - 任务分布仍在快速变化就启动训练
  bound_to:
    - "模型调优路线"
  tags: [counter-example, roi, training-cost]

- id: ce-78
  title: 仅依赖模型自行遵守安全约束
  type: counter-example
  source_chapter: 17.1 Agent 优化方法总述（17.1.3）
  source_quote: |
    "模型负责识别约束并提出动作，但不能仅依赖模型自行遵守：Harness 负责调用侧的权限校验、动作放行、重试上限、停止条件和独立验证，执行环境则负责资源与服务侧的强制校验和动作执行。"
  failure_mode: |
    把权限边界、重试上限、停止条件寄托在模型自觉上，不做系统侧强制校验，越权与失控以一定概率发生。
  mechanism: |
    模型对约束的遵守是概率倾向而非保证；系统控制（校验、放行、上限、独立验证）是确定性层，二者不可互相替代。
  warning_signs:
    - 安全属性全部通过训练/提示获得
    - 无权限校验与重试上限的硬实现
  bound_to:
    - "Agent 安全防护"
    - "模型调优路线"
  tags: [counter-example, model-self-restraint, guardrails]

- id: ce-79
  title: 把不确定结果直接当监督标签
  type: counter-example
  source_chapter: 17.2 SFT（17.2.1）
  source_quote: |
    "如果路径优劣只能通过环境交互才能判断，或者同一状态下缺少可信的目标动作，就不应直接将不确定结果作为监督标签，而应先完善验证条件，或考虑 Agentic RL 等依赖环境反馈的方法。"
  failure_mode: |
    无法验证对错的结果（偶然成功的轨迹、含糊的示范）被直接当作 SFT 监督标签，模型把错误与噪声一起固化。
  mechanism: |
    SFT 放大标签行为的生成概率，不区分正确与错误；标签不可信时训练等于系统性放大噪声，应先完善验证条件或改用环境反馈。
  warning_signs:
    - 训练数据只按最终成败筛选、无逐步验证
    - 同一状态下的示范彼此矛盾
  bound_to:
    - "模型调优路线"
  tags: [counter-example, sft, label-quality]

- id: ce-80
  title: 监督目标笼统定义为"提升 Agent 能力"
  type: counter-example
  source_chapter: 17.2 SFT（17.2.1）
  source_quote: |
    "监督目标应从已经识别的能力缺口出发，而不是笼统地定义为"提升 Agent 能力"。"
  failure_mode: |
    SFT 目标写成笼统的"提升能力"，没有定位到具体决策点（调用时机/工具选择/参数语义/反馈理解/终止判断），无法确定补什么样本、验证什么改进。
  mechanism: |
    可构造的训练数据与可验收的评测都必须以具体决策缺口为单位；笼统目标使数据组织与验收口径都无法闭合。
  warning_signs:
    - 训练需求文档只有一句话目标
    - 评测无法定位改善来自哪类决策
  bound_to:
    - "模型调优路线"
  tags: [counter-example, sft, objective-definition]

- id: ce-81
  title: 训练样本泄露未来信息（离线条件优于线上）
  type: counter-example
  source_chapter: 17.2 SFT（17.2.2）
  source_quote: |
    "某次工具调用尚未返回时，目标动作不能使用其结果；用户在后续轮次补充的条件，也不能提前进入早期决策的输入。否则，离线训练中的信息条件优于真实运行条件，模型即使拟合了监督数据，上线后也难以复现相同表现。"
  failure_mode: |
    构造样本时目标动作使用了其决策时刻尚不可见的信息（未返回的工具结果、后续轮次补充的条件），离线分数高、上线表现差。
  mechanism: |
    每个目标行为只能依赖该决策时刻已可见的信息；信息穿越使训练分布与运行分布系统性偏移，模型学到的是"偷看答案"的捷径。
  warning_signs:
    - 离线评测远好于线上
    - 样本前缀中包含决策时尚未发生的事件
  bound_to:
    - "模型调优路线"
    - "轨迹组织"
  tags: [counter-example, data-leakage, sft]

- id: ce-82
  title: 用 SFT 补偿 Harness 的状态组织缺陷
  type: counter-example
  source_chapter: 17.2 SFT（17.2.2）
  source_quote: |
    "如果必要状态在进入模型前已经被截断、遗漏或错误组织，应先修复 Harness，而不是依靠 SFT 补偿。"
  failure_mode: |
    上下文管线截断/遗漏/错误组织关键状态后，试图用 SFT 教模型"记住"缺失信息，训练成本高且效果不稳定。
  mechanism: |
    模型学习的是利用可见信息决策，不能凭空恢复从未进入输入的状态；供给层缺陷在训练分布外必然复发。
  warning_signs:
    - 训练样本里需要的信息线上并未注入
    - 训练改善无法在完整 Agent 中复现
  bound_to:
    - "调优归因判据"
    - "模型调优路线"
  tags: [counter-example, sft-vs-harness]

- id: ce-83
  title: 成功轨迹被全盘模仿
  type: counter-example
  source_chapter: 17.2 SFT（17.2.3）
  source_quote: |
    "任务最终成功，并不意味着轨迹中的每一步都值得模仿。成功轨迹可能包含多余查询、无依据的判断，或错误后偶然得到正确结果的步骤。"
  failure_mode: |
    以任务最终成败决定整条轨迹的监督价值，多余查询、无依据判断、错误后侥幸成功的步骤被一并模仿。
  mechanism: |
    结果标签的粒度粗于行为质量；需要逐步审查关键行为，错误动作保留为上下文但屏蔽损失、质量存疑步骤移出训练集。
  warning_signs:
    - 数据筛选只有"成功/失败"二值
    - 训练后模型出现源轨迹中的坏习惯
  bound_to:
    - "模型调优路线"
    - "轨迹组织"
  tags: [counter-example, trajectory-quality, masking]

- id: ce-84
  title: 重复复制少量同质示范充当覆盖
  type: counter-example
  source_chapter: 17.2 SFT（17.2.4）
  source_quote: |
    "重复复制少量同质示范只能提高这些示范的权重，不能替代对任务变化、状态分支和异常条件的真实覆盖。"
  failure_mode: |
    数据不足时复制少量同质样本充量，实际只是提高了该分布的权重，任务变化、状态分支与异常条件仍未覆盖。
  mechanism: |
    复制不增加信息量只改变采样权重；泛化来自分布多样性，同质堆量换来的是过拟合与形式记忆。
  warning_signs:
    - 训练集去重后规模骤减
    - 改写题/边界场景表现差
  bound_to:
    - "模型调优路线"
  tags: [counter-example, data-diversity, sft]

- id: ce-85
  title: 训练与推理模板不兼容、截断丢依赖
  type: counter-example
  source_chapter: 17.2 SFT（17.2.4）
  source_quote: |
    "否则，训练损失可能正常下降，但模型生成的动作无法被运行时解析，或缺少作出正确决策所需的信息。"
  failure_mode: |
    训练与推理使用不兼容的对话模板、工具调用格式或结束标记；长样本截断位置丢掉目标动作依赖的约束与工具结果，损失正常下降但产物不可用。
  mechanism: |
    训练目标优化的是文本似然，运行时需要的是可解析结构与完整依赖；模板与截断错位使两个口径脱钩，且不体现在训练指标上。
  warning_signs:
    - 训练顺利但线上工具调用解析失败率高
    - 样本截断位置无人检查
  bound_to:
    - "模型调优路线"
  tags: [counter-example, template-mismatch, truncation]

- id: ce-86
  title: 训练损失下降被当作能力证明
  type: counter-example
  source_chapter: 17.2 SFT（17.2.5）
  source_quote: |
    "训练损失下降只说明模型更容易生成监督样本中的目标内容，不能证明它能够在真实交互中完成任务。"
  failure_mode: |
    以训练损失/拟合度作为 SFT 验收依据，不做完整 Agent 自主运行评测，上线后错误累积与恢复失败暴露。
  mechanism: |
    损失度量的是对静态示范的似然，任务能力是交互序列上的表现；二者之间隔着状态分布漂移与误差累积。
  warning_signs:
    - 验收报告只有训练曲线
    - 没有固定 Harness 下的自主运行评测
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, sft, acceptance]

- id: ce-87
  title: 每步提供标准历史的"续写式"验收
  type: counter-example
  source_chapter: 17.2 SFT（17.2.5）
  source_quote: |
    "若每一步都提供标准历史，只检查模型能否续写下一条标准响应，就无法暴露错误累积和恢复失败。"
  failure_mode: |
    评测时逐步喂标准历史只检查单步续写，模型自身错误被评测协议抹掉，错误累积与恢复能力完全未被测到。
  mechanism: |
    真实运行中历史由模型自身行为生成；标准历史前缀把闭环问题降维成单步问题，掩盖了误差传播。
  warning_signs:
    - 评测脚本预先给出全部正确前缀
    - 线上长任务失败率远超单步评测预期
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, evaluation-protocol, error-accumulation]

- id: ce-88
  title: 同一任务的近似改写同时进训练集与测试集
  type: counter-example
  source_chapter: 17.2 SFT（17.2.5）
  source_quote: |
    "测试集应尽量按任务族、模板或数据来源进行分组，避免同一任务的近似改写同时进入训练集和测试集。"
  failure_mode: |
    测试集与训练集含同源近似改写，评测分数虚高，泛化能力被高估。
  mechanism: |
    近似改写共享解法与表面特征，构成信息泄漏；按任务族分组隔离才能测出跨分布迁移。
  warning_signs:
    - 测试分数高但新任务表现差
    - 数据集切分按随机行而非任务族
  bound_to:
    - "黄金集构建"
    - "模型调优路线"
  tags: [counter-example, contamination, train-test-split]

- id: ce-89
  title: 训练后普遍增加工具调用或交互轮次
  type: counter-example
  source_chapter: 17.2 SFT（17.2.4）
  source_quote: |
    "对于原本可以直接完成的简单任务，也应保留相应样本，防止训练后普遍增加工具调用或交互轮次。"
  failure_mode: |
    训练集被工具调用类样本主导且缺少"直接回答"样本，模型形成"遇到任务就调用工具"的单一习惯，简单任务成本普遍上升。
  mechanism: |
    SFT 改变条件行为分布；类别配比失衡直接改写默认行为倾向，简单场景被过度服务。
  warning_signs:
    - 训练后平均工具调用次数/轮次整体上涨
    - 简单问答也开始调工具
  bound_to:
    - "模型调优路线"
  tags: [counter-example, behavior-shift, sample-ratio]

- id: ce-90
  title: 用 Agentic RL 补偿 Harness 或环境缺陷
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3 开篇）
  source_quote: |
    "Agentic RL 不是对 Harness 或执行环境缺陷的补偿。如果必要上下文没有进入模型、工具定义存在歧义、权限控制可以被绕过，或环境无法稳定复现，训练只会把错误的观察和反馈固化进策略。"
  failure_mode: |
    上下文供给、工具协议、权限控制或环境复现有缺陷时启动 RL 训练，错误的观察与反馈被固化进策略参数。
  mechanism: |
    RL 以环境反馈为真值更新策略；反馈链路本身有缺陷时，优化过程就是在系统化地拟合缺陷，且以参数形式长期携带。
  warning_signs:
    - 环境不可重置/不可复现就开跑训练
    - 训练收益无法在独立任务集复现
  bound_to:
    - "模型调优路线"
    - "调优归因判据"
  tags: [counter-example, rl, environment-defect]

- id: ce-91
  title: 验证器只检查表面结果（学会更快地创建不合规退款）
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.1）
  source_quote: |
    "如果退款验证器只检查"是否生成退款记录"，却不检查政策、金额和权限，模型就可能学会更快地创建不合规退款。"
  failure_mode: |
    验证器/奖励只检查任务表面完成标志，不校验政策、金额、权限等真实约束，模型学会高效制造不合规的"成功"。
  mechanism: |
    RL 最大化的是验证器度量的目标而非真实任务目标；验证口径与业务口径的缝隙就是 reward hacking 的通道，训练会主动找到并扩大它。
  warning_signs:
    - 训练奖励上升但独立评测不升
    - 高分轨迹人工抽检发现合规问题
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, reward-hacking, verifier-gap]

- id: ce-92
  title: 高风险约束只靠负奖励
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.3）
  source_quote: |
    "高风险约束不能只依赖负奖励，还必须由 harness 设置不可绕过的系统控制。"
  failure_mode: |
    越权、隐私、操作边界等硬约束只用负奖励惩罚，模型在探索中仍会以一定概率越过边界，且训练分布外的情形更不可控。
  mechanism: |
    负奖励改变的是倾向不是可行性；概率性抑制存在残留违规率，硬约束必须是系统层不可绕过的拦截。
  warning_signs:
    - 约束违反只出现在损失函数里
    - 无 Sandbox/权限层的强制实施
  bound_to:
    - "模型调优路线"
    - "Agent 安全防护"
  tags: [counter-example, hard-constraints, negative-reward]

- id: ce-93
  title: 成本信号诱导模型提前终止规避困难任务
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.3）
  source_quote: |
    "成本应在任务成功的前提下优化，不能诱导模型通过提前终止规避困难任务。"
  failure_mode: |
    成本惩罚设计不当（不与成功前提绑定），模型学会提前终止或草草收尾来规避高成本困难任务，成功率下降而指标看似改善。
  mechanism: |
    优化器寻找最低代价路径；若终止本身能止损，放弃任务就是理性策略，需要在奖励结构中把成本优化限制在成功任务内。
  warning_signs:
    - 平均成本下降伴随长任务成功率下降
    - 高失败率集中在长链路任务
  bound_to:
    - "模型调优路线"
  tags: [counter-example, cost-reward, early-termination]

- id: ce-94
  title: 把截断当失败、未完成轨迹一律当负样本
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.2）
  source_quote: |
    "不能把所有未完成轨迹一律当作失败，也不能在缺少后继状态时强行续估。"
  failure_mode: |
    把因采样时限、基础设施中断等与任务目标无关的截断当作任务失败训练，策略被无关噪声惩罚；或在无后继观察时强行 bootstrap。
  mechanism: |
    终止（成功/失败/预算耗尽）与截断（外因停止）语义不同；错标截断会把环境故障注入为策略信号，扭曲长任务学习。
  warning_signs:
    - 轨迹记录不区分终止原因
    - 训练不稳定且无算法原因可解释
  bound_to:
    - "模型调优路线"
  tags: [counter-example, truncation-vs-termination, rl]

- id: ce-95
  title: 历史失败轨迹无限回放
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.5）
  source_quote: |
    "将历史失败轨迹无限回放并不会自然产生有效的策略梯度。"
  failure_mode: |
    把历史失败轨迹当作可无限复用的训练数据反复回放，与当前策略的偏差不断累积，训练不稳定或无效。
  mechanism: |
    PPO 等近似 on-policy 方法依赖采样策略与当前策略接近；离策略数据需限制复用范围或做重要性/拒绝采样校正。
  warning_signs:
    - 同一批旧轨迹被多轮迭代重复使用
    - 策略更新收益逐轮衰减甚至反转
  bound_to:
    - "模型调优路线"
  tags: [counter-example, off-policy-bias, replay]

- id: ce-96
  title: 无可靠反馈的轨迹切分
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.5）
  source_quote: |
    "如果只是把长轨迹切成若干文本片段，却没有可靠反馈，模型仍然无法判断哪个早期动作导致了最终结果。"
  failure_mode: |
    以为把长轨迹切成小段就能解决信用分配，切分后没有子目标验证、阶段奖励或价值估计，早期错误的归因仍然缺失。
  mechanism: |
    分段只有同时对应可验证子目标或价值估计才缩短信号传播距离；纯文本切分不产生任何新的反馈信息。
  warning_signs:
    - 长任务训练收敛极慢
    - 分段没有对应任何可验证状态
  bound_to:
    - "模型调优路线"
  tags: [counter-example, credit-assignment, chunking]

- id: ce-97
  title: 训练奖励上升被当作真实能力提升
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.6）
  source_quote: |
    "训练奖励上升只能说明策略更擅长获得当前奖励，不能证明真实任务能力已经提升。"
  failure_mode: |
    以训练回路内的奖励曲线作为 RL 收益证据，不做冻结候选后的独立评测，奖励投机与代理目标偏移不可见。
  mechanism: |
    训练奖励与真实能力之间隔着验证器偏差、环境捷径与分布差异；独立评测（锁定留出集+分离评测器+人工抽检）是唯一裁判。
  warning_signs:
    - 汇报只有 reward 曲线
    - 高分轨迹从未人工抽检
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, reward-vs-capability, independent-eval]

- id: ce-98
  title: 训练反馈直接充当发布结论
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.6）
  source_quote: |
    "训练阶段使用的反馈不应直接充当发布结论"
  failure_mode: |
    开发集/训练验证器的结果被直接用于发布判断，训练与评测机制不分离，评测器偏见与标尺漂移污染发布决策。
  mechanism: |
    训练验证器服务于梯度信号，允许被迭代利用；发布结论要求隔离的验证集、锁定留出集与独立评测器，二者混用即过拟合评测。
  warning_signs:
    - 发布报告引用开发集指标
    - 训练与评测使用同一验证器同一数据
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, eval-separation, publishing]

- id: ce-99
  title: 长期用同一留出集选版本
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.6）
  source_quote: |
    "即使没有直接使用留出集调参，长期依据同一留出集的通过结果选择版本，也会产生间接过拟合。"
  failure_mode: |
    反复用同一锁定留出集选择候选版本，留出集信息通过选择过程泄漏，形成间接过拟合。
  mechanism: |
    版本选择本身是优化过程；选择次数累积后留出集退化为开发集，需定期轮换私有任务与环境种子、保留影子评测集。
  warning_signs:
    - 留出集分数高但线上表现平平
    - 留出集从未轮换
  bound_to:
    - "黄金集构建"
    - "模型调优路线"
  tags: [counter-example, holdout-leakage, selection-overfitting]

- id: ce-100
  title: 用算法选择与拍脑袋权重弥补奖励设计缺陷
  type: counter-example
  source_chapter: 17.3 Agentic RL（17.3.3/17.3.4）
  source_quote: |
    "算法不能弥补任务和奖励设计缺陷。"
  failure_mode: |
    奖励无区分度、Rollout 未覆盖有效路径、状态不可重置时，反复更换优势估计与策略更新算法、凭经验拍权重，问题依旧。
  mechanism: |
    算法改变的是梯度估计方式，不改变优化目标的信息含量；权重应经离线回放、人工抽检与消融实验校准，否则只是重排噪声。
  warning_signs:
    - 频繁切换 PPO/组相对等方法求"炼丹"突破
    - 奖励权重从未被消融验证
  bound_to:
    - "模型调优路线"
  tags: [counter-example, algorithm-vs-design, reward-weights]

- id: ce-101
  title: 把蒸馏理解为让小模型"说得像"
  type: counter-example
  source_chapter: 17.4 模型蒸馏（开篇）
  source_quote: |
    "模型蒸馏的目标并不是简单地让小模型"说得像"强模型，而是把强模型在任务执行中表现出的有效决策迁移给目标模型"
  failure_mode: |
    蒸馏只模仿教师的输出文本风格，不迁移交互决策（何时调工具、如何构造参数、异常后如何调整），学生上线后不能独立执行任务。
  mechanism: |
    Agent 能力在决策序列而非表面输出；监督信号需覆盖结构化动作与多轮轨迹，且要核算含失败重试与教师兜底的总成本。
  warning_signs:
    - 蒸馏数据只有最终答案
    - 学生在教师同分布题上尚可、稍变即崩
  bound_to:
    - "模型调优路线"
  tags: [counter-example, distillation, surface-mimicry]

- id: ce-102
  title: 教师自身不稳定仍进行蒸馏
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.1）
  source_quote: |
    "若教师在某类任务上本身不稳定，蒸馏只会更高效地复制其错误。"
  failure_mode: |
    未验证教师在目标任务上的成功率与示范可验证性就启动蒸馏，教师的错误被学生高效率、低成本地规模化复制。
  mechanism: |
    蒸馏的信号上限是教师质量；监督信号无校验通道时，教师偏差以更密集的方式注入学生参数。
  warning_signs:
    - 教师在目标任务上的成功率未测量
    - 教师示范未经验证直接入训练集
  bound_to:
    - "模型调优路线"
  tags: [counter-example, teacher-quality, error-amplification]

- id: ce-103
  title: 学生容量不足靠堆示范数量弥补
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.1）
  source_quote: |
    "若学生容量过小，增加示范数量也未必能弥补表示能力与长程规划能力的不足。"
  failure_mode: |
    学生模型容量/长程规划能力不足时继续扩大蒸馏数据规模，收益递减而成本上升，任务边界问题被数据问题掩盖。
  mechanism: |
    容量是表示能力的硬约束；数据量无法突破模型可表达策略族的上限，应先做小规模可教性测试再定投入。
  warning_signs:
    - 数据翻倍指标几乎不动
    - 未做可教性测试就锁定学生规格
  bound_to:
    - "模型调优路线"
  tags: [counter-example, student-capacity, scaling]

- id: ce-104
  title: 高风险任务仅凭平均分取消教师校验
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.1）
  source_quote: |
    "对于高风险或不可逆操作，即使学生在离线评测中达到较高成功率，也不宜仅凭平均分取消教师校验。"
  failure_mode: |
    学生平均成功率达到阈值后全量接管包括高风险/不可逆操作在内的全部任务，长尾失败直接转化为不可逆业务损失。
  mechanism: |
    平均分对尾部风险不敏感；高风险任务需要独立的安全违规率、不可逆错误率与教师兜底召回率门槛，或采用学生先执行+教师校验的级联。
  warning_signs:
    - 上线决策只看一个平均成功率
    - 高风险动作与学生普通动作走同一放行逻辑
  bound_to:
    - "模型调优路线"
    - "Agent 安全防护"
  tags: [counter-example, high-risk, average-score]

- id: ce-105
  title: 只用最终答案做蒸馏监督
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.2）
  source_quote: |
    "仅用最终答案训练，学生可能学会结果形式，却没有学会何时调用工具、如何构造参数以及观察异常后如何调整。"
  failure_mode: |
    蒸馏监督只含最终输出，学生学到答案形式而学不到工具时机、参数构造与异常恢复，Agent 场景下不可用。
  mechanism: |
    Agent 决策分布由（状态，动作）对构成；结果级模仿丢失了状态条件映射，监督需上升到结构化动作与多轮轨迹层。
  warning_signs:
    - 蒸馏集只有 input-output 对
    - 学生静态题尚可、交互任务崩溃
  bound_to:
    - "模型调优路线"
  tags: [counter-example, distillation-signal, action-level]

- id: ce-106
  title: 未经验证的教师样本直接扩大规模
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.2）
  source_quote: |
    "过滤错误轨迹并保留失败原因，比单纯扩大未经验证的教师样本更重要。"
  failure_mode: |
    以"更多数据"思路扩大未验证教师样本规模，错误示范稀释正确信号，数据成本换来越差的迁移质量。
  mechanism: |
    示范价值 = 正确性 × 可验证性；验证层（规则/裁判/人工抽检）应先于扩量，错误轨迹的失败原因本身是补强素材。
  warning_signs:
    - 数据管线没有验证环节
    - 加数据后指标反降
  bound_to:
    - "模型调优路线"
  tags: [counter-example, data-verification, scaling]

- id: ce-107
  title: 离线蒸馏忽视学生的状态分布偏移
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.3）
  source_quote: |
    "学生一旦在部署时作出不同动作，就可能进入教师示范中从未出现的状态，后续误差因此持续累积。"
  failure_mode: |
    只用教师策略产生的状态做离线蒸馏，学生上线后第一步偏离即进入未见状态，误差沿多轮持续放大，早衰且难恢复。
  mechanism: |
    训练状态分布来自教师，部署状态分布来自学生，二者必然偏移；需要 on-policy 蒸馏（学生生成、教师在其到达的状态打分）覆盖学生真实会犯的错。
  warning_signs:
    - 离线指标好，线上多轮任务衰减快
    - 失败集中在第一偏离点之后
  bound_to:
    - "模型调优路线"
  tags: [counter-example, distribution-shift, offline-distillation]

- id: ce-108
  title: 把"教师高概率"等同于"任务正确"
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.3）
  source_quote: |
    ""教师高概率"不等同于"任务正确"。反向 KL 仍可能复制教师偏差、压低必要探索，甚至让学生固守一种局部最优策略"
  failure_mode: |
    用反向 KL 蒸馏后默认教师偏好即正确，不与任务成功验证和异常覆盖联合校验，学生固化教师的偏差与局部最优。
  mechanism: |
    反向 KL 的 mode-seeking 特性让学生收敛到教师的一条高概率路径；该性质有助于一致性，但同样忠实放大教师错误并抑制必要探索。
  warning_signs:
    - 蒸馏后学生行为高度单一
    - 未在独立任务上验证学生成功率
  bound_to:
    - "模型调优路线"
  tags: [counter-example, reverse-kl, teacher-bias]

- id: ce-109
  title: 只比较 token 单价判断蒸馏收益
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.5）
  source_quote: |
    "只比较 token 单价，会忽略学生因规划能力下降而产生的额外步骤和失败重试。"
  failure_mode: |
    用单次调用单价或"单次推理成本×平均重试次数÷成功率"的极简近似判断收益，忽略学生额外步骤、失败重试、教师兜底、验证器与工具成本，实际单位成功任务成本更高。
  mechanism: |
    总成本沿真实调用路径累积（学生+教师+验证器+工具+运行时），失败任务的消耗也计入分子；近似公式默认每次尝试同价、失败与成功路径等长，在多模型多工具 Agent 中不成立。
  warning_signs:
    - ROI 论证只有模型单价对比
    - 上线后端到端成本与预测不符
  bound_to:
    - "模型调优路线"
  tags: [counter-example, cost-accounting, unit-economics]

- id: ce-110
  title: 把厂商案例当普适收益基准
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.5）
  source_quote: |
    "该案例提供了灰度发布和持续监控的实践参考，但质量、时延、重试和运维成本缺少可审计的完整数据，应视为厂商案例，而不是普适收益基准。"
  failure_mode: |
    直接引用公开案例的相对收益（如"推理成本下降 70%/98%"）作为立项承诺，未用自身任务的实测点重新拟合。
  mechanism: |
    外部案例的任务分布、系统条件与成本口径不可审计；收益是条件量，跨场景不可迁移，正式项目必须以自有实测数据核算。
  warning_signs:
    - 立项材料里的收益数字无本企业数据支撑
    - 没有计划做小规模实测验证
  bound_to:
    - "模型调优路线"
  tags: [counter-example, vendor-benchmark, roi]

- id: ce-111
  title: '"成本下降但失败损失上升"的伪收益'
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.5）
  source_quote: |
    "若新方案的单任务净收益不高于旧方案，就不存在有限的正向盈亏平衡点。这一表达能够避免"成本下降但失败损失上升"的伪收益。"
  failure_mode: |
    只比执行成本不比失败损失（L）与成功率（p），新方案单价更低但失败率更高，净收益为负却被判定为降本成功。
  mechanism: |
    单任务净收益 g = pV − (1−p)L − C；忽略失败损失项时，任何用质量换价格的方案都会被误判为正收益。
  warning_signs:
    - 成本核算没有失败损失科目
    - 降本方案上线后业务赔付/返工上升
  bound_to:
    - "模型调优路线"
  tags: [counter-example, pseudo-gain, failure-cost]

- id: ce-112
  title: 把教师兜底视为蒸馏失败
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.5）
  source_quote: |
    "对仍需教师兜底的系统，应把兜底看作目标架构的一部分，而不是蒸馏失败的例外。"
  failure_mode: |
    把任何教师兜底都视为蒸馏不彻底，追求学生 100% 替代，导致在高风险/长尾任务上质量失守；或反过来因兜底存在就否定蒸馏价值。
  mechanism: |
    部分替代+路由兜底本身是可验证的商业价值形态；判断标准是单位成功任务成本与端到端时延是否优于原方案，而非替代率本身。
  warning_signs:
    - 项目目标写成"完全去掉教师"
    - 为压兜底率牺牲高风险任务质量
  bound_to:
    - "模型调优路线"
  tags: [counter-example, cascading, partial-replacement]

- id: ce-113
  title: 蒸馏产物一次性全量替换上线
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.6）
  source_quote: |
    "蒸馏产物上线不必是"一次性全量替换"。"
  failure_mode: |
    学生模型验证后直接全量切换生产流量，无影子流量、分档灰度、观察窗与自动回滚，长尾退化一次性暴露于全部用户。
  mechanism: |
    离线评测覆盖不了真实分布长尾；灰度纪律（影子→1%→…→100%，每档最短观察窗+回滚阈值）是把不可见风险分批变现的机制。
  warning_signs:
    - 上线计划没有分档与回滚条件
    - 无线上监控与黄金集回归交叉验证
  bound_to:
    - "模型调优路线"
    - "上线前仿真"
  tags: [counter-example, rollout, gradual-release]

- id: ce-114
  title: 学生模型在长尾场景悄悄退化
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.6）
  source_quote: |
    "维护一个固定的黄金数据集（golden set）加线上采样回流的回归评测集，在每个灰度档位跑离线回归并与线上指标交叉验证，防止学生模型在长尾场景悄悄退化"
  failure_mode: |
    上线后只监控总体指标，无黄金集+线上回流回归，学生模型在低频长尾场景的退化不被总体均值反映，悄然积累。
  mechanism: |
    长尾事件的统计权重低，聚合指标对其不敏感；需要固定的黄金集按档位回归与线上采样回流形成双通道检测。
  warning_signs:
    - 上线后只有大盘成功率监控
    - 无固定回归集或长期未更新
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, long-tail, regression-monitoring]

- id: ce-115
  title: 只看最终失败标签，不定位首个关键偏离
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.4）
  source_quote: |
    "多轮 Agent 的失败通常不是在最后一步突然发生，而是由早期偏差逐步放大。……能力补强的重点不应只看最终失败标签，而应定位学生相对于有效路径的首个关键偏离。"
  failure_mode: |
    补强数据按最终失败标签组织，不按首错位置聚类，未针对知识/决策/执行/恢复四类缺口补不同数据，同类失败反复出现。
  mechanism: |
    错误的工具选择污染后续上下文、未核验的中间结论建立在错误前提上；只有回溯到首个偏离点，才能切断放大链。
  warning_signs:
    - 失败分析报告只写"最终答错"
    - 补数据不分缺口类型
  bound_to:
    - "模型调优路线"
    - "轨迹组织"
  tags: [counter-example, first-deviation, gap-typology]

- id: ce-116
  title: 把训练集上的改善当作补强有效
  type: counter-example
  source_chapter: 17.4 模型蒸馏（17.4.4）
  source_quote: |
    "只有当学生在独立保留集和分布外压力集上都改善，才应把专项补强视为有效，而不是对训练集的记忆。"
  failure_mode: |
    用专项补强训练集自身的分数验证补强效果，把记忆当能力，分布外无改善甚至退化。
  mechanism: |
    专项数据针对特定缺口，易被记住而非泛化；必须以独立保留集和分布外压力集双验证。
  warning_signs:
    - 补强后只在同分布题上升分
    - 无分布外测试集
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, memorization, ood]

- id: ce-117
  title: 训练完成即认为具备上线条件
  type: counter-example
  source_chapter: 17.5 模型验收与上线（开篇）
  source_quote: |
    "训练完成并不等于模型已经具备上线条件。SFT、Agentic RL 或模型蒸馏改变的是模型参数，但真实应用效果由模型、Harness 与执行环境共同决定"
  failure_mode: |
    训练结束（指标达标）后跳过系统适配、准入门禁与灰度验证直接上线，真实效果由三层共同决定的部分全部未验证。
  mechanism: |
    参数改变只是候选；完整 Agent 中的协议兼容、行为兼容、预算兼容与安全边界兼容都可能否决候选模型。
  warning_signs:
    - 上线依据只有训练/离线分数
    - 无适配检查清单
  bound_to:
    - "模型调优路线"
    - "上线前仿真"
  tags: [counter-example, acceptance, full-system]

- id: ce-118
  title: 候选模型与新版 Prompt/宽松权限/简化环境混在一起测
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.1）
  source_quote: |
    "如果候选模型同时使用了新的 Prompt、更宽松的工具权限或更简单的任务环境，即使端到端成功率提高，也无法判断收益来自模型训练还是系统条件变化。"
  failure_mode: |
    建立可比较版本时同时变更模型、Prompt、权限与环境，端到端提升无法归因，且宽松条件下的结论在生产不可复现。
  mechanism: |
    需要纯模型对照（同 Harness 同环境比模型）与部署组合对照（适配后组合比生产）两组；混改变量使两组都失效。
  warning_signs:
    - 对比实验只跑一组配置
    - 报告无法回答"收益来自哪一层"
  bound_to:
    - "调优归因判据"
    - "模型调优路线"
  tags: [counter-example, controlled-comparison, confounds]

- id: ce-119
  title: 直接替换模型端点
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.2）
  source_quote: |
    "直接替换模型端点，可能出现离线能力分数提高但 Agent 无法解析工具调用、重复执行动作或不能正确终止的问题。"
  failure_mode: |
    以为换模型只是换端点，不做工具协议、上下文、执行行为、预算与安全边界兼容性适配，离线分数提高但线上无法解析、重复执行或不能终止。
  mechanism: |
    不同模型对消息模板、结构化输出、工具描述与停止信号的敏感性不同；协议层的微小差异在 Agent Loop 中被循环放大。
  warning_signs:
    - 替换后工具调用解析失败率上升
    - 出现无限规划或过早终止
  bound_to:
    - "模型调优路线"
  tags: [counter-example, endpoint-swap, compatibility]

- id: ce-120
  title: 因模型表现改善而取消系统护栏
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.2）
  source_quote: |
    "验证模型产生越权或高风险动作时，Harness 与执行环境仍能执行独立校验和强制拦截，不能因模型表现改善而取消系统护栏。"
  failure_mode: |
    新模型违规动作频率下降后移除 Harness/环境的独立校验，安全防线退化为模型自律，分布外场景失去兜底。
  mechanism: |
    模型安全是策略风险（频率降低），系统拦截是防护有效性（不可绕过）；二者是不同对象，前者改善不构成后者的替代。
  warning_signs:
    - 安全门禁因"新模型更安全"被移除
    - 违规穿透率不再被单独统计
  bound_to:
    - "Agent 安全防护"
    - "模型调优路线"
  tags: [counter-example, guardrail-removal, defense-in-depth]

- id: ce-121
  title: 局部格式正确率直接当上线依据
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.2）
  source_quote: |
    "只有闭环执行稳定，局部格式正确率才具有上线意义。"
  failure_mode: |
    适配验证停留在固定输入的格式检查，未在 Sandbox 中闭环自主运行与故障注入，循环内的不稳定（恢复失败、重复调用）未暴露。
  mechanism: |
    局部→闭环的测试顺序覆盖不同失效面；格式正确只是必要条件，闭环执行（含超时、无效返回、权限拒绝注入）才是充分性检验。
  warning_signs:
    - 适配测试全是固定输入用例
    - 无故障注入环节
  bound_to:
    - "模型调优路线"
    - "上线前仿真"
  tags: [counter-example, closed-loop-testing]

- id: ce-122
  title: 准入依赖一个加权总分
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.3）
  source_quote: |
    "准入不能依赖一个加权总分，而应采用"硬门槛 + 目标收益"：任何关键兼容性、安全或能力回归不合格都应阻断发布"
  failure_mode: |
    用加权总分做发布判定，安全或兼容性的关键缺陷被其他维度的高分平均掉，带病发布。
  mechanism: |
    不同维度不可互偿：硬门槛（兼容/安全/回归）是一票否决，目标收益只在其全部通过后判断；加权平均隐含"以长补短"的错误交换假设。
  warning_signs:
    - 发布标准只有一个综合分
    - 安全违规被质量提升"抵扣"
  bound_to:
    - "模型调优路线"
    - "上线前仿真"
  tags: [counter-example, gating, hard-thresholds]

- id: ce-123
  title: 看完候选结果后再定门槛与阈值
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.3）
  source_quote: |
    "门槛、非劣效界值、最低样本量、置信区间口径和观察窗口应在查看候选结果前确定。"
  failure_mode: |
    先看结果再定准入标准，门槛被无意识调整到能让当前候选通过，验收失去独立性。
  mechanism: |
    事后标准等于用数据反推结论；预注册门槛（含非劣效界值、样本量、口径、观察窗）才能保证发布判断可复现且不受确认偏误影响。
  warning_signs:
    - 阈值在评审会上临时协商
    - 每次发布"恰好"压线通过
  bound_to:
    - "模型调优路线"
    - "黄金集构建"
  tags: [counter-example, preregistration, moving-goalposts]

- id: ce-124
  title: 把"未产生副作用"误认为完成端到端验证
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.4）
  source_quote: |
    "或者只比较候选动作而不执行，不能把"未产生副作用"误认为完成了端到端验证。"
  failure_mode: |
    影子验证中对多步任务只比较候选动作而不在隔离环境真实执行，把"没出事"当作"验证通过"，状态依赖的行为缺陷未暴露。
  mechanism: |
    依赖状态变更的任务其行为正确性取决于执行反馈链；不真实执行就没有观察，"无副作用"是验证协议的结果而非质量证据。
  warning_signs:
    - 影子流量只 diff 输出文本
    - 多步任务从未在状态副本中重放
  bound_to:
    - "模型调优路线"
    - "上线前仿真"
  tags: [counter-example, shadow-testing, completion-semantics]

- id: ce-125
  title: 回滚时只回退模型权重
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.4）
  source_quote: |
    "如果新模型依赖新的 Prompt、工具 Schema 或上下文策略，仅回退模型可能形成不兼容组合。"
  failure_mode: |
    故障回滚只切换回旧模型权重，保留新 Prompt/Schema/上下文策略，形成从未验证过的不兼容组合引发二次故障。
  mechanism: |
    回滚对象应是完整候选发布单元（模型+Harness+环境+评测配置），依赖关系整体倒退；组件级回滚破坏已冻结的组合一致性。
  warning_signs:
    - 回滚脚本只切模型服务版本
    - Prompt/工具配置无对应快照
  bound_to:
    - "模型调优路线"
    - "资产注册发现"
  tags: [counter-example, rollback-unit, release-consistency]

- id: ce-126
  title: 以为回滚能撤销已完成的写操作
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.4）
  source_quote: |
    "回滚只能阻止后续请求继续使用故障版本，不能撤销已经在外部系统中完成的写操作；涉及资金、账户或业务状态的任务还需要独立的幂等、审计和补偿机制。"
  failure_mode: |
    把回滚当作完整恢复手段，不设幂等键、审计与补偿机制，故障版本在外部系统已完成的写操作（资金/账户/状态）无法收回。
  mechanism: |
    回滚作用于流量路由，是面向未来的控制；外部副作用是既成事实，只能靠幂等防重、审计定位与业务补偿挽回。
  warning_signs:
    - 高风险任务无幂等设计
    - 无补偿流程预案
  bound_to:
    - "模型调优路线"
    - "行动契约设计"
  tags: [counter-example, rollback-limits, side-effects]

- id: ce-127
  title: 单个偶发失败自动触发训练
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.5）
  source_quote: |
    "只有被持续观察到、影响明确且可验证的问题，才应进入下一轮优化；单个偶发失败不应自动触发训练。"
  failure_mode: |
    把单次偶发失败当作能力缺口证据启动训练轮次，训练成本浪费在噪声上，且每轮训练本身引入回归风险。
  mechanism: |
    偶发失败无法与采样随机性和环境波动区分；进入优化需要"独立样本中稳定复现+业务影响超阈值+责任层确认+可验证修复路径"的触发机制。
  warning_signs:
    - 训练任务由单条 Badcase 直接产生
    - 无触发门槛与责任层确认
  bound_to:
    - "Badcase 闭环"
    - "模型调优路线"
  tags: [counter-example, trigger-discipline, noise]

- id: ce-128
  title: 同一长程任务在新旧模型间切换
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.4）
  source_quote: |
    "线上 A/B 对照还应固定分流单元，通常按用户、会话或任务分桶，避免同一长程任务在新旧模型间切换"
  failure_mode: |
    灰度分流按请求随机，同一长程任务的各步分别落到新旧版本，行为不一致导致任务失败且指标失真。
  mechanism: |
    长任务的正确性依赖跨步骤一致性；分桶单元必须大于任务跨度（按用户/会话/任务分桶），否则实验设计本身制造故障。
  warning_signs:
    - 灰度期长任务失败率异常
    - 分流键是请求级随机数
  bound_to:
    - "模型调优路线"
  tags: [counter-example, ab-testing, routing-consistency]

- id: ce-129
  title: 把线上数据无差别自动回灌训练
  type: counter-example
  source_chapter: 17.5 模型验收与上线（17.5.5）
  source_quote: |
    "这里的目标不是把所有线上数据自动回灌训练，而是建立明确的触发机制"
  failure_mode: |
    把全部运行数据自动回灌训练闭环，噪声、偶发失败与未归因问题混入训练信号，迭代变成不可解释的线上试错。
  mechanism: |
    数据→训练需经过稳定复现、影响阈值、责任层确认与可验证修复路径四道触发条件；无门槛回灌让每轮训练都携带未诊断的混杂因素。
  warning_signs:
    - 训练数据集就是原始线上日志
    - 版本迭代无法解释每轮改了什么为什么
  bound_to:
    - "受控自进化"
    - "模型调优路线"
  tags: [counter-example, data-feedback, uncontrolled-iteration]

## 第 22 章 Badcase 分析与评估实验

- id: ce-130
  title: 多个评估器的结果简单平均当业务质量
  type: counter-example
  source_chapter: 22.1 先选一个业务问题，做出能用的评估器
  source_quote: |
    "若需要总分，在评分标准中明确计算方式；不能把多个评估器的结果简单平均，就默认代表业务质量。"
  failure_mode: |
    把回答质量、业务操作完成等多个评估器的分数简单平均成总分，掩盖单项失败，无法定位是哪一项下降。
  mechanism: |
    各评估器度量不同维度且量纲/权重不一；未定义聚合规则的平均会稀释关键维度（如业务操作未完成被礼貌分抵消）。
  warning_signs:
    - 看板只有一个平均分
    - 总分正常但业务投诉持续
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, metric-aggregation]

- id: ce-131
  title: 只评价回复是否礼貌，不检查业务结果
  type: counter-example
  source_chapter: 22.1 先选一个业务问题，做出能用的评估器
  source_quote: |
    "涉及退款、权限变更等操作时，再增加业务结果检查，避免只评价回复是否礼貌。"
  failure_mode: |
    评估器只覆盖回复态度与格式，退款、权限变更等操作是否真实完成不在评估范围内，"优质回复"掩盖业务未交付。
  mechanism: |
    评估范围决定可见性；无业务结果检查时，执行类失败在评估层结构性不可见。
  warning_signs:
    - Rubric 全部是语言质量条款
    - 高分样本中工具调用失败频发
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, business-outcome, evaluation-scope]

- id: ce-132
  title: 评估字段误接（最终回答接到工具调用输出）
  type: counter-example
  source_chapter: 22.2 用几条真实样本试评
  source_quote: |
    "例如，需要"最终回答"的位置不能误接到某次工具调用的输出。"
  failure_mode: |
    评估任务的变量映射接错（最终回答接到工具调用输出、轨迹缺失），评估器看到的是错误对象，结论全部失真且看似正常运行。
  mechanism: |
    评估器只能判断收到的输入；映射错误不报错只产生系统性偏移的分数，必须用已知好/坏/信息不完整样本试评来暴露。
  warning_signs:
    - 未做字段映射试评就批量运行
    - 已知坏样本被判高分
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, field-mapping, evaluator-sanity]

- id: ce-133
  title: 仅凭文件链接或一句"已完成"作判断
  type: counter-example
  source_chapter: 22.2 用几条真实样本试评
  source_quote: |
    "判断报销金额是否正确，需要看到票据和填报结果；仅收到文件链接或一句"已完成"不足以作出判断。"
  failure_mode: |
    评估器输入只有文件链接或 Agent 的"已完成"声明，没有票据与填报结果等证据材料，却仍然输出分数，制造有依据的假象。
  mechanism: |
    评估需要的是证据而非声明；输入不含可核验材料时评估器应输出"无法判断"，否则是幻觉式打分。
  warning_signs:
    - 评估器对缺证据样本也给出确定分数
    - 输入映射里没有原始材料字段
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, evidence-input, evaluator]

- id: ce-134
  title: 评估器只检查关键词出现
  type: counter-example
  source_chapter: 22.2 用几条真实样本试评
  source_quote: |
    "比如评估器只检查是否提到"评估器"这个词，就可能把泛泛介绍当作操作指导，需要补充"能否让用户完成下一步"的标准。"
  failure_mode: |
    评分条款退化为关键词匹配，泛泛而谈的回答被判高分，操作性缺失不可见。
  mechanism: |
    关键词存在性与任务达成是弱相关信号；条款必须写成可判定的行为标准（用户能否完成下一步），并以已知好坏样本校准。
  warning_signs:
    - Rubric 条款是"是否提到 X"
    - 已知好回答与泛泛回答得分接近
  bound_to:
    - "黄金集构建"
  tags: [counter-example, rubric-design, keyword-matching]

- id: ce-135
  title: 看到低分立即改 Prompt
  type: counter-example
  source_chapter: 22.3 从结果里选出值得修复的 Badcase
  source_quote: |
    "不要看到低分便立即改 Prompt。"
  failure_mode: |
    低分出现后不经解释与轨迹确认直接改 Prompt，把评估器误判、执行异常、材料缺失也当作模型问题修。
  mechanism: |
    正确顺序是原始问题、Agent 回答、分项判断、解释、轨迹放在一起读，先确认问题成立再决定动作（纳入调优样本/修评估器/补执行条件）。
  warning_signs:
    - 改动频率高且无解释依据
    - 部分低分复核后是评估器或环境问题
  bound_to:
    - "Badcase 闭环"
  tags: [counter-example, prompt-patching, diagnosis-first]

- id: ce-136
  title: 不同标准、不同业务的数据混在一起比较
  type: counter-example
  source_chapter: 22.3 从结果里选出值得修复的 Badcase
  source_quote: |
    "查看低分时还要带上任务和时间范围，避免把不同标准、不同业务的数据混在一起比较。"
  failure_mode: |
    跨任务、跨评估器、跨时间范围的分数被放在同一列表比较排序，选出的"最差样本"只是标准最严的业务的样本。
  mechanism: |
    分数只在同一评分标准与业务口径内可比；混比把评分标准差异误读为质量差异，误导修复优先级。
  warning_signs:
    - 低分榜混合多种任务类型
    - 筛选时不带任务与时间范围维度
  bound_to:
    - "Badcase 闭环"
  tags: [counter-example, comparability, filtering]

- id: ce-137
  title: 把所有低分样本都收进实验集
  type: counter-example
  source_chapter: 22.3 从结果里选出值得修复的 Badcase
  source_quote: |
    "最初不需要收入所有低分样本：同一种遗漏选择有代表性的几条，同时保留边界场景和一部分原本正常的任务，才能验证修复是否带来退化。"
  failure_mode: |
    实验集收进全部低分样本且不含正常任务与边界场景，修复后无法检测退化，同类问题重复占位挤占实验预算。
  mechanism: |
    修复验证需要对照组：代表性同质样本降低冗余，正常样本提供退化检测，边界样本防止过窄优化。
  warning_signs:
    - 实验集全红（全是失败样本）
    - 修复后无法回答"别的题受影响吗"
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, sample-selection, control-group]

- id: ce-138
  title: 只看低分，不抽检高分样本
  type: counter-example
  source_chapter: 22.3 从结果里选出值得修复的 Badcase
  source_quote: |
    "高分样本要适当抽检，避免只看低分而漏掉评估器未识别的严重问题。"
  failure_mode: |
    分析只从低分入手，评估器漏判的严重问题（被判高分的坏回答）永久留在盲区。
  mechanism: |
    评估器存在假阴性；只按分数筛选会把评估器的盲区变成系统的盲区，高分抽检是校准评估器召回的唯一手段。
  warning_signs:
    - 从未复核过高分样本
    - 用户投诉的案例在评估中是高分
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, evaluator-recall, sampling]

- id: ce-139
  title: 评估器误判样本被直接丢弃
  type: counter-example
  source_chapter: 22.3 从结果里选出值得修复的 Badcase
  source_quote: |
    "人工发现评估器误判的样本也很有价值，应留作校准材料。"
  failure_mode: |
    人工复核发现评估器判错的样本后直接丢弃，不沉淀为校准材料，下一轮无法检验评估器是否仍犯同样的错误。
  mechanism: |
    误判样本是评估器的失败用例；留档后每轮可同时检查 Agent 改善与评估器纠错，丢弃等于放弃免费的评估器回归集。
  warning_signs:
    - 误判样本无留档机制
    - 同类误判反复出现
  bound_to:
    - "黄金集构建"
    - "Badcase 闭环"
  tags: [counter-example, evaluator-calibration]

- id: ce-140
  title: 一轮实验同时换模型、改 Prompt、增添 Skill
  type: counter-example
  source_chapter: 22.4 把确认的问题配成实验
  source_quote: |
    "若同时换模型、改 Prompt、增添 Skill，分数变化就难以解释；第一轮可以先只改变最有依据的一项。"
  failure_mode: |
    一轮实验同时变更多个变量，分数变化无法归因到任何单项，实验结论不可用且无法复用。
  mechanism: |
    实验的价值在于隔离变量；单变量原则配合 Baseline 记录才能建立因果，多变量混改只能得到"变了"而非"为什么变"。
  warning_signs:
    - 实验计划里并列多项修改
    - 结果讨论无法指出哪个改动起效
  bound_to:
    - "Badcase 闭环"
    - "调优归因判据"
  tags: [counter-example, single-variable, experiment-design]

- id: ce-141
  title: 实验评到旧回答而非新生成答案
  type: counter-example
  source_chapter: 22.4 把确认的问题配成实验
  source_quote: |
    "实验与前面的历史评估有一个关键区别：被评分的必须是本次生成的新回答和新轨迹。"
  failure_mode: |
    实验的变量映射误接历史数据，评的是旧回答/旧轨迹，候选方案的效果完全未被测量，实验空转。
  mechanism: |
    历史评估评存量、实验评增量；映射到旧数据时输入输出无对应关系，分数与候选方案零因果，需先修映射再重跑。
  warning_signs:
    - 候选与 Baseline 分数完全相同
    - 实验未实际调用 Agent
  bound_to:
    - "Badcase 闭环"
  tags: [counter-example, stale-data, experiment-validity]

- id: ce-142
  title: 把 API 受理成功当作题目完成
  type: counter-example
  source_chapter: 22.5 把企业自己的 Agent 接进来
  source_quote: |
    "如果 API 先返回任务受理信息，还需要继续等待最终回答，不能把受理成功当作题目完成。"
  failure_mode: |
    异步 Agent 接入时把接口受理/任务受理响应当作最终回答，实验在任务真正完成前就采样评分，结论系统性偏差。
  mechanism: |
    异步协议的受理与完成是两个语义节点；完成判定需要等待最终响应或轮询状态，否则评的是"已受理"而非"已交付"。
  warning_signs:
    - 有会话状态的 Agent 被同步方式调用
    - 大量样本的"回答"是受理报文
  bound_to:
    - "Badcase 闭环"
    - "任务契约设计"
  tags: [counter-example, async-semantics, completion]

- id: ce-143
  title: 相互独立的样本共用一个会话
  type: counter-example
  source_chapter: 22.5 把企业自己的 Agent 接进来
  source_quote: |
    "相互独立的样本应使用独立会话，避免前一道题的上下文影响后一道题。"
  failure_mode: |
    评测样本串行复用同一会话，前一道题的上下文污染后一道题，评测结果混入跨样本记忆效应。
  mechanism: |
    会话状态把独立样本耦合为序列实验；每题的输入不再可控，分数波动无法归因于候选方案本身。
  warning_signs:
    - 样本顺序不同导致分数不同
    - 后题回答中出现前题实体
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, session-isolation, evaluation-hygiene]

- id: ce-144
  title: 仅沿用同一服务地址就认为配置相同
  type: counter-example
  source_chapter: 22.5 把企业自己的 Agent 接进来
  source_quote: |
    "把版本写入实验的上下文或记录中，并确认服务已经使用该版本。仅沿用同一个服务地址，无法说明两次运行的配置相同。"
  failure_mode: |
    对比实验假设同一服务地址等于同一配置，实际服务端已更新版本，Baseline 与候选在不同系统条件下被比较。
  mechanism: |
    服务地址是路由标识不是版本标识；必须把版本写入实验记录并确认服务使用该版本，否则对照实验的等价前提不成立。
  warning_signs:
    - 实验记录无版本字段
    - 服务端有独立发布节奏
  bound_to:
    - "Badcase 闭环"
    - "调优归因判据"
  tags: [counter-example, version-identity, controlled-comparison]

- id: ce-145
  title: 输出变长被当作改善
  type: counter-example
  source_chapter: 22.6 查看同一道题的前后差异
  source_quote: |
    "输出变长本身不代表改善，新增内容需要对应评分要求。"
  failure_mode: |
    把回答更长/更详细当作改进证据，不核对新增内容是否对应评分条款，注水式输出获得升级。
  mechanism: |
    长度与质量弱相关且部分评估器有长度偏好；改善的证据是关键条款通过率，需逐项核对分项结果与输出差异。
  warning_signs:
    - 升级理由包含"回答更全面了"
    - 平均输出长度与平均分同步上涨
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, length-bias, acceptance]

- id: ce-146
  title: 总分提高但关键条款未通过就升级
  type: counter-example
  source_chapter: 22.6 查看同一道题的前后差异
  source_quote: |
    "若候选总分提高，但关键条款仍未通过，这轮还不适合升级"
  failure_mode: |
    以总分提升为升级标准，忽略关键条款（安全性、必要步骤）仍未通过，带缺陷的候选进入发布流程。
  mechanism: |
    总分是加权平均，可被次要项改善抬高；关键条款是硬门槛，未通过时总分提升只说明"平均变好"而非"底线达标"。
  warning_signs:
    - 升级评审只看总分
    - 关键失败模式未列入阻断条件
  bound_to:
    - "Badcase 闭环"
  tags: [counter-example, hard-clause, gating]

- id: ce-147
  title: 分数起伏一律归因于评分标准
  type: counter-example
  source_chapter: 22.6 查看同一道题的前后差异
  source_quote: |
    "看到分数起伏时，先打开对应的回答和运行记录，不能一律归因于 Rubric。"
  failure_mode: |
    重复运行分数波动时笼统归因于 Rubric 不稳定，不区分评估器判断波动与 Agent 执行变化（回答、工具、环境），问题定位错误。
  mechanism: |
    需要两类对照实验：对同一份固定回答重复评分（测评估器）、让 Agent 重新执行同批题（测执行稳定性）；不做区分则把执行不稳定误诊为评估器问题。
  warning_signs:
    - 波动分析从不重跑固定回答
    - '"评估器不稳"成为万能解释'
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, variance-attribution, diagnosis]

- id: ce-148
  title: 省略必要步骤得到的低耗时当升级理由
  type: counter-example
  source_chapter: 22.6 查看同一道题的前后差异
  source_quote: |
    "反过来，省略必要步骤得到的低耗时不能作为升级理由。"
  failure_mode: |
    候选以跳过必要步骤换取低耗时/低成本，把表面效率收益当升级依据，交付质量实际下降。
  mechanism: |
    耗时与成本只有在完成同等任务口径下才可比；省略步骤的"提速"是用质量换指标，需结合关键条款与业务价值判断。
  warning_signs:
    - 升级理由主要是更快更便宜
    - 耗时下降与某类必要动作消失同时出现
  bound_to:
    - "Badcase 闭环"
  tags: [counter-example, shortcut-efficiency, acceptance]

- id: ce-149
  title: 结果出来后临时更改统计方式
  type: counter-example
  source_chapter: 22.6 查看同一道题的前后差异
  source_quote: |
    "不要在结果出来后临时更改统计方式。"
  failure_mode: |
    看到不理想的结果后临时换统计口径（剔除失败样本、改聚合方式）让候选通过，比较失去公允性。
  mechanism: |
    统计口径是比较协议的一部分；事后更改等于对结果选择性采信，任何一轮结论都无法被后续复现，需事先约定失败样本的处理规则。
  warning_signs:
    - 评审中临时决定"把超时的去掉再算"
    - 不同轮次用不同口径汇报
  bound_to:
    - "Badcase 闭环"
    - "黄金集构建"
  tags: [counter-example, statistics-discipline, cherry-picking]
