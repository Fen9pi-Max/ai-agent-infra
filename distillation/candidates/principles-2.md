# principle extractor 分区2：第 8-9/13-14/17.1/24 章

```yaml
# ============ 第 8 章 状态存储 ============

- id: p2-01
  title: 先定义正确性边界，再选存储引擎
  type: principle
  source_chapter: 8.1.3 不同状态对象的一致性要求
  source_quote: |
    "正确的起点不是先选择某一种引擎，而是先定义不同状态对象的正确性边界，再为其匹配相应的存储能力"
  summary: |
    存储选型的第一步不是挑选数据库产品，而是先明确每类状态对象（任务事实/工作成果/记忆知识/治理状态）
    各自需要的一致性与访问保证（顺序、原子、幂等、可回滚、可检索、可审计），再据此匹配物理承载。
  tags: [principle, storage, decision-rule]

- id: p2-02
  title: Agent 状态四类分层地图
  type: checklist
  source_chapter: 8.1.2 Agent 需要存储哪些状态
  source_quote: |
    "第一类是运行时状态……第二类是工作区与产物……第三类是记忆与知识……第四类是业务语义与治理状态"
  summary: |
    Agent 状态分四类，各自生命周期、访问方式和一致性要求不同：
    1) 运行时状态（事件日志、检查点、租约、状态指针）；2) 工作区与产物（文件、快照、版本、分支）；
    3) 记忆与知识（摘要、偏好、向量索引、知识图谱）；4) 业务语义与治理状态（本体、规则、租户、权限、审计）。
  tags: [checklist, storage, architecture]

- id: p2-03
  title: 事件与状态必须原子同步提交
  type: rule
  source_chapter: 8.2.2 Event Log
  source_quote: |
    "事件记录与当前状态同时维护时，应在事务中提交，或通过可靠的 Outbox 等机制保证最终同步，并以事件标识去重"
  summary: |
    Event Log 与当前状态视图同时维护时，二者必须在同一事务提交，或用 Outbox 机制保证最终同步；
    去重以事件标识为准，避免事件丢失或重复导致任务编排错乱。
  tags: [rule, event-sourcing, consistency]

- id: p2-04
  title: 强一致的边界是任务，不是全局日志
  type: principle
  source_chapter: 8.2.2 Event Log
  source_quote: |
    "更合理的边界通常是任务、会话或业务对象：同一任务内部的状态变化需要有明确先后关系，而相互独立的任务可以并发执行"
  summary: |
    不要让所有 Agent 任务争用同一条全局日志。顺序性保证的范围应收敛到任务/会话/业务对象级别；
    相互独立的任务并发执行，既保证单任务正确性又避免全局串行低效。
  tags: [principle, event-sourcing, scalability]

- id: p2-05
  title: 事件日志可作重放依据的条件
  type: rule
  source_chapter: 8.2.2 Event Log
  source_quote: |
    "事件日志只有包含完整的状态变更和必要输入时，才可作为重放依据；诊断日志和模型输出不能直接替代这份记录"
  summary: |
    只有包含完整状态变更和必要输入的事件日志才能作为重放依据；诊断日志和模型输出不能直接替代。
    用于审计/恢复的事件与用于排查的诊断数据要分开管理。
  tags: [rule, event-sourcing, replay]

- id: p2-06
  title: 事件保留分层策略
  type: rule
  source_chapter: 8.2.2 Event Log
  source_quote: |
    "对近期任务保留细粒度事件，对稳定完成的任务形成可验证快照，并依据业务、合规和审计要求设置保留周期"
  summary: |
    在保留审计事实与控制存储成本之间平衡：近期任务保留细粒度事件，稳定完成任务转为可验证快照，
    保留周期按业务/合规/审计要求设置。关键是影响任务正确性和外部动作的事实始终可追溯，而非永久保存模型输出。
  tags: [rule, retention, cost]

- id: p2-07
  title: 有效 Checkpoint 的内容清单
  type: checklist
  source_chapter: 8.2.3 Checkpoint
  source_quote: |
    "一个有效的 Checkpoint 不应只是模型上下文的简单拷贝，而应包含恢复所需的关键状态：当前任务阶段、已完成步骤、未完成步骤、工具调用结果引用、工作区版本、子任务状态、等待条件以及状态版本"
  summary: |
    Checkpoint 七要素：当前任务阶段、已完成步骤、未完成步骤、工具调用结果引用、工作区版本、
    子任务状态、等待条件与状态版本。模型上下文拷贝不算有效 Checkpoint。
  tags: [checklist, checkpoint, durability]

- id: p2-08
  title: 主动打 Checkpoint 的关键边界清单
  type: checklist
  source_chapter: 8.2.3 Checkpoint
  source_quote: |
    "外部调用发起前后、长时间计算完成后、人工确认前后、工作区发生重要变更后，以及任务准备交接给其他 Agent 时"
  summary: |
    长链路任务应在五个关键边界主动形成 Checkpoint：外部调用发起前后、长时间计算完成后、
    人工确认前后、工作区重要变更后、任务交接给其他 Agent 时。
  tags: [checklist, checkpoint, long-running-task]

- id: p2-09
  title: Checkpoint 频率与任务成本匹配
  type: principle
  source_chapter: 8.2.3 Checkpoint
  source_quote: |
    "对于只需数秒完成、失败后可以低成本重试的查询型任务，过度频繁地保存快照反而会增加额外开销"
  summary: |
    秒级可低成本重试的查询型任务不需要频繁快照；调用外部系统、处理长链路数据、需人工介入的任务
    才在关键边界主动快照。检查点策略按任务类型分别设定，在成本、性能与恢复精度间形成清晰边界。
  tags: [principle, checkpoint, cost]

- id: p2-10
  title: 增量快照恢复组合规则
  type: rule
  source_chapter: 8.2.3 Checkpoint
  source_quote: |
    "平台可以基于状态版本保存变化集，并在恢复时组合最近完整快照与后续增量"
  summary: |
    状态变化常只涉及局部字段，不必每次复制完整状态：基于状态版本保存变化集，
    恢复时 = 最近完整快照 + 后续增量，兼顾恢复效率与高并发脉冲负载。
  tags: [rule, checkpoint, incremental]

- id: p2-11
  title: Durable Execution 恢复公式：最新快照 + 增量事件回放
  type: principle
  source_chapter: 8.2.4 跨副本恢复与 Durable Execution
  source_quote: |
    "恢复时，新的执行实例首先读取任务的最新 Checkpoint，再按需回放该快照之后的事件。它不必重新执行已经确认完成的步骤"
  summary: |
    执行实例是可替换的计算单元，不是任务事实的唯一拥有者。恢复 = 读取最新 Checkpoint + 按需回放其后事件，
    从最近可信状态继续，不重跑已确认完成的步骤，也不依赖单一可变状态而失去过程证据。
  tags: [principle, durable-execution, recovery]
  inputs: [最新 Checkpoint, 快照之后的事件序列]
  formula: "恢复点 = 最新 Checkpoint + 其后事件按需回放"
  output: 任务从最近可信状态继续
  missing_conditions: 事件日志须满足 p2-05 的重放条件

- id: p2-12
  title: 执行租约与围栏：只有持最新接力棒者可提交
  type: rule
  source_chapter: 8.2.4 跨副本恢复
  source_quote: |
    "只有持有最新接力棒的实例才能提交下一次状态变化；旧实例即使仍在运行，也不能覆盖新的执行结果"
  summary: |
    跨副本恢复必须解决"谁有权继续执行"：通过执行租约确定当前执行权，状态写入附带版本或围栏标识；
    旧实例即使仍在运行也不能覆盖新实例的执行结果。
  tags: [rule, lease, fencing]

- id: p2-13
  title: Durable Execution ≠ 外部动作恰好执行一次
  type: principle
  source_chapter: 8.2.4 跨副本恢复
  source_quote: |
    "对于外部副作用，还需要结合幂等标识、结果确认和补偿机制……运行时状态存储必须同时保存调用意图、调用标识和调用结果，而不能只记录一句'正在执行'"
  summary: |
    平台只能保证任务状态变化有明确事实边界，不能保证外部动作恰好一次。跨系统边界的动作需幂等标识、
    结果确认和补偿机制；状态存储必须同时保存调用意图、调用标识和调用结果。
  tags: [principle, idempotency, side-effect]

- id: p2-14
  title: 工作区不应是实例上的临时目录
  type: principle
  source_chapter: 8.3 工作区与沙箱快照的存储后端
  source_quote: |
    "实例本地文件可以提供快速读写，却不应成为唯一事实来源……把'可运行的临时环境'演进为'可持续管理的任务资产空间'"
  summary: |
    实例本地文件不应成为唯一事实来源。工作区应拥有独立身份、访问权限、版本历史和生命周期，
    可被新执行实例重新挂载，也可被其他 Agent 或人工协作者有选择地共享。
  tags: [principle, workspace, state-externalization]

- id: p2-15
  title: 分层工作区：共享不变部分，隔离变化部分
  type: principle
  source_chapter: 8.3.2 分层工作区
  source_quote: |
    "这种结构的核心思想是'共享不变部分，隔离变化部分'……底部是可复用、只读的基础层……顶部则是当前任务独占的可写层"
  summary: |
    工作区分三层：只读基础层（运行环境/依赖/模板）、任务或租户可共享数据层、任务独占可写层。
    多 Agent 共用基础环境不必完整复制镜像，每任务只记录自己的变化，降低启动时间与存储成本。
    公共基础层应由平台或受控团队维护，不允许 Agent 随意修改共享依赖。
  tags: [principle, workspace, copy-on-write]

- id: p2-16
  title: 试错与交付并存：确认后才提升为任务资产
  type: rule
  source_chapter: 8.3.2 分层工作区
  source_quote: |
    "它可以在私有可写层中修改脚本、生成数据、尝试多种方案；但只有经过确认的结果，才会被提升为可共享、可引用的任务资产"
  summary: |
    Agent 在私有可写层自由试错，但只有经过确认的结果才显式进入产物库，获得稳定身份、版本和保留策略。
    避免临时文件、失败尝试和最终成果混在同一空间。
  tags: [rule, workspace, artifact]

- id: p2-17
  title: 可信快照五问
  type: checklist
  source_chapter: 8.3.3 快照、回滚与分支
  source_quote: |
    "一个可信快照应能够回答'这是哪个任务、哪个阶段、基于什么输入、由谁生成、后续产生了哪些变化'"
  summary: |
    快照可信度判据五问：哪个任务、哪个阶段、基于什么输入、由谁生成、后续产生了哪些变化。
    能回答这五问的快照才能支持试验、审阅、回滚和分支。
  tags: [checklist, snapshot, auditability]

- id: p2-18
  title: 环境回滚不撤销已提交到外部系统的操作
  type: principle
  source_chapter: 8.3.3 快照、回滚与分支
  source_quote: |
    "环境回滚不撤销已经提交到外部系统的操作"
  summary: |
    工作区快照回滚只恢复文件环境，不撤销已提交到外部系统的操作。涉及外部副作用的恢复需另行补偿，
    不能假设"回滚即无事发生"。
  tags: [principle, rollback, side-effect]

- id: p2-19
  title: Checkpoint 必须记录工作区快照引用
  type: rule
  source_chapter: 8.3.3 快照、回滚与分支
  source_quote: |
    "运行时 Checkpoint 应记录对应的工作区快照引用……避免出现'任务状态已经恢复，但文件状态已经变化'的不一致"
  summary: |
    任务进入关键阶段、完成重要工具调用、准备人工审批或切换 Agent 时，Checkpoint 应记录对应工作区快照引用，
    使恢复实例能重新挂载当时准确的工作环境，避免任务状态与文件状态不一致。
  tags: [rule, checkpoint, workspace]

- id: p2-20
  title: 工作区写入必须验证执行代次
  type: rule
  source_chapter: 8.3.4 多副本并发恢复
  source_quote: |
    "支持围栏的写入接口应验证当前执行代次……仅更新任务表中的租约，无法阻止仍持有文件句柄的旧实例继续写入"
  summary: |
    可写层的写入约束独立于任务状态租约：写入接口应验证执行代次（围栏）；普通文件系统无法逐次校验时，
    采用独占挂载、撤销旧端访问或私有分支 + 持有者发布。只更新任务表租约挡不住仍持有文件句柄的旧实例。
  tags: [rule, fencing, workspace]

- id: p2-21
  title: 多 Agent 协作不共写同一目录
  type: rule
  source_chapter: 8.3.4 多副本并发恢复
  source_quote: |
    "平台不应简单地让所有 Agent 同时修改同一份目录，而应显式区分共享与私有边界……避免把多 Agent 协作退化为不可审计的文件覆盖竞争"
  summary: |
    多 Agent 协作时各自在私有可写分支工作，通过稳定快照或 Artifact 引用读取他人结果；
    合并需经明确的版本合并与审批过程形成新可信状态，不允许并发覆盖同一目录。
  tags: [rule, multi-agent, workspace]

- id: p2-22
  title: Artifact 不可变原则
  type: rule
  source_chapter: 8.3.4 多副本并发恢复
  source_quote: |
    "一旦进入 Artifact Store，就不应再被原地修改；后续变更应生成新版本"
  summary: |
    已完成的报表、数据集、测试结果、交付包等 Artifact 一旦入库不可原地修改，后续变更生成新版本，
    保证引用方始终知道自己读取的是哪一份内容。
  tags: [rule, artifact, immutability]

- id: p2-23
  title: Artifact 元数据清单
  type: checklist
  source_chapter: 8.3.5 Artifact Store
  source_quote: |
    "它需要为每份成果建立清晰身份，并附带来源任务、创建时间、版本、生成 Agent、输入依据、审批状态、权限范围和保留策略等元数据"
  summary: |
    每份 Artifact 必须附带八项元数据：来源任务、创建时间、版本、生成 Agent、输入依据、审批状态、
    权限范围、保留策略。可寻址（稳定引用）+ 可保留（生命周期策略）是其区别于临时文件的本质。
  tags: [checklist, artifact, metadata]

- id: p2-24
  title: 独立元数据索引是规模化运营的必要条件
  type: principle
  source_chapter: 8.3.6 规模化工作空间
  source_quote: |
    "这些元数据既不能只保存在运行时上下文中（实例退出后即丢失），也不能只保存在产物文件中（无法高效检索）"
  summary: |
    产物元数据（由哪个任务/Agent/时间生成、基于哪些输入、经历哪些审批）不能只放运行时上下文（实例退出即丢）
    也不能只放产物文件（无法检索），必须建立独立元数据索引支撑检索、审计和合规检查。
  tags: [principle, metadata, operations]

- id: p2-25
  title: 长期记忆双正交维度分类
  type: framework-rule
  source_chapter: 8.4.2 记忆分层模型
  source_quote: |
    "参考架构以两个独立的正交维度组织长期记忆，避免把'来源'与'稳定性'混为同一分类轴"
  summary: |
    记忆按两个正交维度组织：来源三类（用户显式表达 / Agent 行为归纳 / 外部业务事实）×
    稳定性三层（人设与身份层 / 画像层 / 事件偏好层）。同一条记忆可同时落不同层，不混用分类轴。
  tags: [rule, memory, taxonomy]

- id: p2-26
  title: Persona 显式维护，不作为经验性记忆
  type: rule
  source_chapter: 8.4.2 记忆分层模型
  source_quote: |
    "Persona（Agent 人设与身份配置）不作为经验性记忆，而由构建方或治理方显式维护，并受版本与权限控制"
  summary: |
    Agent 人设与身份配置由构建方/治理方显式维护并受版本与权限控制，不由 Agent 经历自动生成或修改。
  tags: [rule, memory, persona]

- id: p2-27
  title: 记忆写入判据：什么值得记、什么应该忘
  type: rule
  source_chapter: 8.4.3 记忆写入管线
  source_quote: |
    "寒暄、临时调试指令和一次性查询不宜进入长期记忆，只有对后续交互有预测价值的信息才应沉淀"
  summary: |
    记忆写入以"对后续交互有预测价值"为判据；寒暄、临时调试指令、一次性查询不进入长期记忆。
  tags: [rule, memory, write-policy]

- id: p2-28
  title: 新记忆必须匹配、合并、消解冲突后再落库
  type: rule
  source_chapter: 8.4.3 记忆写入管线
  source_quote: |
    "新记忆不会被简单追加，而是与已有记忆进行语义匹配、合并与去重……当新旧记忆产生冲突时，系统结合时效性、来源可靠度与上下文完成覆盖或保留待确认状态"
  summary: |
    记忆不是简单追加：先语义匹配、合并、去重；冲突时结合时效性、来源可靠度与上下文决定覆盖或保留待确认。
    多次独立交互指向同一结论时可信度增强。
  tags: [rule, memory, conflict-resolution]

- id: p2-29
  title: 记忆混合召回：三路召回 + Rerank + 时间衰减
  type: rule
  source_chapter: 8.4.4 记忆检索
  source_quote: |
    "语义向量检索……关键词检索以 BM25 等机制补充词项匹配与排序；实体关系检索……候选结果经 Rerank 精排后，结合时间衰减和类别过滤输出"
  summary: |
    记忆召回 = 语义向量（Embedding）+ 关键词（BM25）+ 实体关系三路召回，候选统一 Rerank 精排，
    再结合时间衰减和类别过滤输出。记忆价值不仅取决于语义相关性，还与时近性、引用频率和场景适配度有关。
  tags: [rule, memory, retrieval]

- id: p2-30
  title: 时间衰减与引用频率的适用边界
  type: rule
  source_chapter: 8.4.4 记忆检索
  source_quote: |
    "长期有效的约束不应仅因较少被调用而淡出；引用频率也不等于正确性，应与来源和实际验证结果共同使用"
  summary: |
    时间衰减只适用于易变信息：长期有效的约束不应因少被调用而淡出；引用频率不等于正确性，
    必须与来源可信度和实际验证结果共同使用。
  tags: [rule, memory, decay]

- id: p2-31
  title: 记忆删除必须同步清除全部衍生物并防重建
  type: rule
  source_chapter: 8.4.5 记忆更新与遗忘
  source_quote: |
    "用户要求删除的信息需要同步处理原始记录、摘要、索引和缓存，并防止后续重建将其重新引入"
  summary: |
    归档、降低召回权重与删除是三种不同操作。用户删除请求须同步处理原始记录、摘要、索引和缓存，
    并防止后续处理（如摘要重建）把已删信息重新引入；依法保留记录应隔离用途按保留策略处理。
  tags: [rule, memory, deletion, compliance]

- id: p2-32
  title: 记忆与知识库的边界
  type: principle
  source_chapter: 8.5.1 知识库与长期记忆的边界
  source_quote: |
    "记忆是 Agent 自己'经历'的，来自对话和行为积累……知识库是 Agent 外部'引入'的，来自企业文档、产品手册、行业报告和业务数据"
  summary: |
    记忆 = Agent 自己经历的（对话与行为积累，由应用/用户/维护者共同管理）；
    知识库 = Agent 外部引入的（企业文档等，由知识管理员或业务团队维护）。来源与维护责任不同，不可混同。
  tags: [principle, memory, knowledge-base]

- id: p2-33
  title: 记忆和知识都需要来源核验
  type: principle
  source_chapter: 8.5.1 知识库与长期记忆的边界
  source_quote: |
    "知识库内容并不因被导入而自动权威，记忆也不因来自真实交互就必然正确"
  summary: |
    知识库内容不因被导入而自动权威，记忆不因来自真实交互就必然正确——两者召回使用前都需要来源核验。
  tags: [principle, memory, knowledge-base, verification]

- id: p2-34
  title: 知识切片过大引入噪声、过小丢失上下文
  type: rule
  source_chapter: 8.5.3 索引构建与增量更新
  source_quote: |
    "切片过大会引入噪声，切片过小则会丢失必要上下文。Lakebase 支持以标题、段落和语义转折为边界进行智能切片，并保留父级标题等上下文信息"
  summary: |
    知识切片策略：以标题、段落和语义转折为边界智能切片，保留父级标题等上下文锚点；
    过大引入噪声、过小丢失上下文。解析深度（标题层级、段落关系、表格、实体、时间数值）决定检索质量上限。
  tags: [rule, rag, chunking]

- id: p2-35
  title: 知识索引增量更新：可用水位后才切流量
  type: rule
  source_chapter: 8.5.3 索引构建与增量更新
  source_quote: |
    "新版本完成构建与验证后再切换查询入口；更新时效应按实际数据规模和管线能力设定，不能由'增量'直接推导固定生效时间"
  summary: |
    知识更新流程：变更检测只处理变化部分 → 记录源版本/解析版本/索引水位 → 新索引构建并验证达到可用水位
    → 才切换查询流量；失败则重放或回退旧版本，不让 Agent 在不完整数据上作判断。
  tags: [rule, rag, incremental-update]

- id: p2-36
  title: 结构化数据探查必须只读账号 + 授权 Schema
  type: rule
  source_chapter: 8.5.4 混合检索与多路召回
  source_quote: |
    "此类探查应使用只读账号，并将可访问的表与字段范围约束在授权 Schema 内"
  summary: |
    Agent 对关系型数据库的自然语言探查（DataProbe 类）必须使用只读账号，可访问表与字段范围约束在授权 Schema 内，
    不以自然语言转 SQL 的方式获得写能力。
  tags: [rule, data-access, least-privilege]

- id: p2-37
  title: 知识权限过滤覆盖检索到生成全链路
  type: rule
  source_chapter: 8.5.6 知识治理
  source_quote: |
    "这种控制需要覆盖检索到生成的全链路，避免推理过程成为受限信息的旁路"
  summary: |
    权限过滤基于用户角色和文档密级在检索阶段控制，且必须覆盖检索到生成的全链路，
    防止 Agent 直接或间接使用当前用户无权获取的内容，避免推理过程成为受限信息旁路。
  tags: [rule, rag, access-control]

- id: p2-38
  title: 答案溯源：每个回答可回溯至文档、段落与版本
  type: rule
  source_chapter: 8.5.6 知识治理
  source_quote: |
    "答案溯源让每个基于知识库生成的回答都能回溯至具体文档、段落与版本，是用户验证和审计的基础"
  summary: |
    基于知识库生成的每个回答必须能回溯至具体文档、段落与版本，作为用户验证和审计的基础。
  tags: [rule, rag, traceability]

- id: p2-39
  title: GraphRAG 适用边界：关系推理类问题
  type: rule
  source_chapter: 8.5.5 GraphRAG
  source_quote: |
    "许多业务问题的答案并不存在于某一段文本中，而是分散在多个文档、多条事实之间的关系推理结果"
  summary: |
    答案分散在多文档、需跨实体关系推理的问题（股权穿透、供应链关联、跨法规条款审查）才启用 GraphRAG 路径，
    且按经过验证的路由规则启用；抽取关系与模型推断均需保留来源并验证。普通语义相似检索交给 RAG。
  tags: [rule, graphrag, routing]

- id: p2-40
  title: 本体引入时机判据
  type: rule
  source_chapter: 8.6.1 超越 RAG
  source_quote: |
    "当任务只需要检索少量文档时，可以从常规检索开始；当实体身份、关系约束和多跳查询成为持续需求时，再引入本体并承担相应建模和维护成本"
  summary: |
    只有当实体身份、关系约束和多跳查询成为持续需求时才引入本体并承担建模维护成本；
    少量文档检索场景从常规检索开始。
  tags: [rule, ontology, adoption]

- id: p2-41
  title: 本体建模四要素
  type: checklist
  source_chapter: 8.6.2 本体建模
  source_quote: |
    "Lakebase 的本体建模围绕对象、关系、动作和规则四个要素展开"
  summary: |
    本体建模四要素：对象（业务实体及属性约束）、关系（关联方向/基数/业务语义）、
    动作（触发条件/执行逻辑/权限约束）、规则（专家经验显式化、约束自主决策边界）。
  tags: [checklist, ontology, modeling]

- id: p2-42
  title: 语义定义、执行与授权三责任分离
  type: principle
  source_chapter: 8.6.2 本体建模
  source_quote: |
    "语义定义、执行与授权三种责任相互分离，避免执行逻辑和治理决策混入语义模型"
  summary: |
    本体承担语义建模（对象/关系/动作语义与规则元数据）；动作执行生命周期由 Runtime 按语义完成；
    权限与 Agent 绑定的授权决策由治理责任方作出。三者分离，本体通过版本管理独立演进。
  tags: [principle, ontology, separation-of-concerns]

- id: p2-43
  title: 实例数据更新与模型定义变更分开处理
  type: rule
  source_chapter: 8.6.3 本体动态管理
  source_quote: |
    "实例数据更新与模型定义变更应分开处理：前者更新对象的当前属性和关系，后者改变 Schema 或业务规则，需要版本管理与影响评估"
  summary: |
    本体的实例数据更新（当前属性关系）与模型定义变更（Schema/业务规则）走不同流程：后者需版本管理与影响评估，
    核心概念保持稳定、外围属性灵活演进，配合同步水位与校验发现本体和业务数据差异。
  tags: [rule, ontology, versioning]

- id: p2-44
  title: 语义层反馈受控更新：模型推测不得直接写成组织规则
  type: rule
  source_chapter: 8.6.6 本体、知识与记忆的协同
  source_quote: |
    "交互可以提出候选事实或模型修订建议，由相应维护者验证后生效；不得将模型推测直接写成组织规则"
  summary: |
    记忆/知识/本体之间的反馈必须受控：交互产生的候选事实或模型修订建议由相应维护者验证后生效，
    模型推测不得直接写入组织规则。
  tags: [rule, knowledge-governance, controlled-update]

- id: p2-45
  title: 多租户隔离必须贯穿访问全过程
  type: principle
  source_chapter: 8.7.1 多租户隔离模型
  source_quote: |
    "这个边界不仅存在于业务数据库中，也必须贯穿记忆召回、知识检索、文件访问、向量搜索、工具执行和运维观测的全过程"
  summary: |
    隔离若只停留在业务表查询条件中，Agent 仍可能通过共享索引、缓存命中、产物链接或日志检索接触越权信息；
    边界必须贯穿记忆召回、知识检索、文件访问、向量搜索、工具执行和运维观测全过程。
  tags: [principle, multi-tenant, isolation]

- id: p2-46
  title: 隔离三层次：物理、逻辑、行级分层组合
  type: rule
  source_chapter: 8.7.1 多租户隔离模型
  source_quote: |
    "物理隔离面向强监管、高敏感数据……逻辑隔离适用于多数企业级业务空间……行级隔离面向大规模共享服务与细粒度协作"
  summary: |
    隔离按敏感度和规模分三层：物理隔离（强监管/高敏感/重要客户专属）、逻辑隔离（多数企业业务空间，独立库/Schema/
    命名空间）、行级隔离（大规模共享，按租户/角色/用户/数据标签）。三层互斥不必要，实际平台分层组合使用。
  tags: [rule, multi-tenant, isolation-levels]

- id: p2-47
  title: 语义检索必须前置租户与权限过滤
  type: rule
  source_chapter: 8.7.1 多租户隔离模型
  source_quote: |
    "语义检索若先跨租户召回、再过滤结果，可能已在候选生成阶段引入不必要的数据暴露风险。因此，平台应在索引空间、检索条件和重排序策略中前置租户与权限约束"
  summary: |
    向量、图谱等语义数据不能先跨租户召回再过滤结果；租户与权限约束必须前置到索引空间、检索条件和重排序策略中，
    让 Agent 只在被授权的语义空间内召回。
  tags: [rule, multi-tenant, retrieval-security]

- id: p2-48
  title: 权威事实与派生视图分级一致
  type: principle
  source_chapter: 8.7.2 存储组件之间的一致性
  source_quote: |
    "对影响任务正确性的运行时状态……应以强一致或可验证提交为目标；对向量索引、全文检索、统计聚合等派生数据，则可接受短暂的最终一致，但必须让系统明确知道其新鲜度"
  summary: |
    不以"所有组件同时成功"定义一致性：影响任务正确性的运行时状态（租约、支付确认、关键检查点）用强一致或
    可验证提交；派生数据（向量索引、检索、缓存、读副本）可最终一致，但系统必须知道其新鲜度和对应源数据版本。
    写入时先可靠提交权威事实，再异步推进索引与副本。
  tags: [principle, consistency, derived-data]

- id: p2-49
  title: 统一元数据最小描述清单
  type: checklist
  source_chapter: 8.7.3 统一元数据管理
  source_quote: |
    "对象类型与唯一标识、所属租户和工作空间、创建主体、来源系统、内容摘要、敏感等级、版本、存储位置、访问策略、生命周期状态，以及与其他对象之间的关联关系"
  summary: |
    每个数据对象的元数据至少十一项：类型与唯一标识、租户与工作空间、创建主体、来源系统、内容摘要、
    敏感等级、版本、存储位置、访问策略、生命周期状态、对象关联关系。策略（保留期/脱敏/审批/删除）与元数据绑定。
  tags: [checklist, metadata, governance]

- id: p2-50
  title: 成本治理第一原则：按数据价值分层
  type: principle
  source_chapter: 8.7.4 成本治理
  source_quote: |
    "成本治理的第一原则，是按数据价值而非技术类型进行分层"
  summary: |
    冷热分层依据访问频率、业务价值、合规要求和恢复成本确定数据位置，而非按技术类型搬旧数据：
    近期任务状态/热门知识低延迟层，已完成事件/低频文档温冷层，审计内容归档层。
  tags: [principle, cost, tiering]

- id: p2-51
  title: 保留期必须成为显式策略
  type: principle
  source_chapter: 8.7.4 成本治理
  source_quote: |
    "第二原则是让保留期成为显式策略……平台应将这些规则与对象类型、数据分类和租户策略绑定，自动执行到期归档、索引失效和安全删除"
  summary: |
    不同对象保留逻辑不同：运行时临时状态任务完成后快速清理、检查点保留至恢复窗口结束、用户记忆支持更新撤销删除、
    知识历史版本按审计需要长期保存。规则绑定对象类型/数据分类/租户策略自动执行。
  tags: [principle, retention, lifecycle]

- id: p2-52
  title: 减少无效复制，记忆具备适度遗忘能力
  type: principle
  source_chapter: 8.7.4 成本治理
  source_quote: |
    "长期记忆和多模态内容，不应因为未来或许有用而无限累积；平台需要定期评估其访问价值、时效性和可信度，让记忆系统具备适度遗忘的能力"
  summary: |
    通过内容去重、增量索引、摘要压缩、失效检测和按需物化控制数据衍生规模；
    长期记忆和多模态内容不因"未来或许有用"无限累积，定期评估访问价值、时效性和可信度。
  tags: [principle, cost, memory]

- id: p2-53
  title: 存储选型验收清单
  type: checklist
  source_chapter: 8.7.5 存储选型
  source_quote: |
    "关键状态能否原子提交，产物引用是否稳定，知识和记忆的权限撤回是否及时，各层能否独立备份与恢复，以及数据是否能够完整导出"
  summary: |
    存储选型（组件组合 vs 一体化 Agent 数据库）按对象逐一验证五项：关键状态能否原子提交、产物引用是否稳定、
    权限撤回是否及时、各层能否独立备份恢复、数据能否完整导出。验收落到实际任务和故障场景，而非产品名称。
  tags: [checklist, storage, selection]

- id: p2-54
  title: 统一指数据模型与治理界面，非单一底层引擎
  type: principle
  source_chapter: 8.7.5 存储选型
  source_quote: |
    "这里的统一指的是数据模型与治理界面，而非单一底层引擎——不同数据对象仍各自采用合适的承载方式"
  summary: |
    一体化 Agent 数据库的"统一"指统一对象模型、服务接口与治理控制面（身份/版本/权限/生命周期/可用性一致契约），
    底层仍可按对象特性用不同引擎；统一接口不等于所有对象自动获得相同保证。
  tags: [principle, storage, architecture]

- id: p2-55
  title: 恢复流程五步：定位事件→加载检查点→校验租约→确认外部状态→决定动作
  type: checklist
  source_chapter: 8.7.6 高可用与容灾
  source_quote: |
    "系统需要定位最后一个已确认事件，加载对应检查点，校验执行租约与幂等标识，确认外部调用状态后再决定继续执行、重试、补偿或转人工处理"
  summary: |
    关键业务 Agent 恢复五步：定位最后已确认事件 → 加载对应检查点 → 校验执行租约与幂等标识 →
    确认外部调用状态 → 决定继续执行/重试/补偿/转人工。仅恢复底层数据而无法判断工具调用与租约状态，
    可能重复执行高风险操作。恢复目标（RPO/RTO）按数据对象定义并与业务等级关联。
  tags: [checklist, disaster-recovery, durability]

- id: p2-56
  title: 容灾能力必须经过持续演练验证
  type: principle
  source_chapter: 8.7.6 高可用与容灾
  source_quote: |
    "只有这些能力被真正验证，Agent 的可靠性才不只是架构图上的承诺"
  summary: |
    定期演练验证四项：主区域不可用时运行中 Agent 能否切换、损坏索引能否依据源数据重建、
    误删记忆/产物能否在权限边界内恢复、跨组件任务恢复后能否保持幂等与可追溯。
  tags: [principle, disaster-recovery, drill]

# ============ 第 9 章 AI 网关与统一流量治理 ============

- id: p2-57
  title: 网关只保证准入转发记录，不判定任务完成
  type: principle
  source_chapter: 9.1.1 从接入模型到治理调用
  source_quote: |
    "网关只保证准入、转发与记录的一致性，既不能仅凭请求成功断言任务完成，也不能替代执行系统保证状态恢复"
  summary: |
    任务成功标准、业务状态、检查点内容和恢复流程由业务模型与执行系统定义；网关职责边界是准入、转发与记录的一致性，
    不凭请求成功断言任务完成，不替代执行系统保证状态恢复。
  tags: [principle, gateway, responsibility]

- id: p2-58
  title: 三种网关语义是分析框架，非成熟度阶梯
  type: principle
  source_chapter: 9.1 三种可组合的治理语义
  source_quote: |
    "它们是本章采用的分析框架，不是行业统一的成熟度阶梯，也不要求企业依次建设三个独立产品"
  summary: |
    LLM Gateway / MCP Gateway / Agent Gateway 是按治理对象划分的可组合语义，可部署在同一数据面或由不同组件承接；
    不是线性能力升级路径，不要求依次建设三个独立产品。
  tags: [principle, gateway, framing]

- id: p2-59
  title: TTFT 统计必须明确起止点，不等于 HTTP 首字节
  type: rule
  source_chapter: 9.1.2 AI 流量约束
  source_quote: |
    "首 token 时延（TTFT）的统计必须明确起止点，不能简单等同于 HTTP 首字节时延"
  summary: |
    SSE 只是传输形式，不能直接充当完整业务事件的边界：字节切片可能含半个或多个事件，首个 SSE 事件可能只是心跳。
    TTFT 指首个有效 token 前的等待，须区分连接、首字节、首 token、流式空闲及总时限，识别流内错误。
  tags: [rule, metrics, ttft]

- id: p2-60
  title: KV cache 复用是性能优化，不是业务状态恢复
  type: rule
  source_chapter: 9.1.2 AI 流量约束
  source_quote: |
    "键值缓存（KV cache）的复用是推理性能优化，不是业务状态恢复……缓存未命中通常应表现为性能变化，而不应造成业务 Task 丢失"
  summary: |
    把相似上下文路由到同一推理实例可减少重复预填充，但 KV cache 未命中应表现为性能变化（延迟升高），
    不得造成业务 Task 丢失；业务连续性依赖状态持久化和恢复，不依赖缓存。
  tags: [rule, gateway, cache-vs-state]

- id: p2-61
  title: 统一治理四条件
  type: checklist
  source_chapter: 9.1.3 三种可组合的治理语义
  source_quote: |
    "统一的关键不是所有能力位于同一进程，而是身份映射一致、策略边界清晰、调用记录能够关联，且同一次上游调用不会因穿过多个组件而被重复计费"
  summary: |
    网关统一治理的四条件：身份映射一致、策略边界清晰、调用记录可关联、同一次上游调用不因穿过多个组件被重复计费。
    不要求所有能力位于同一进程。
  tags: [checklist, gateway, unified-governance]

- id: p2-62
  title: 网关建设进度按独立验收目标衡量
  type: rule
  source_chapter: 9.1.5 集中治理不等于集中所有执行
  source_quote: |
    "每一步都应有独立的验收目标，例如凭证是否不再分散到客户端、用量是否能归属到项目、拒绝是否有可追溯依据，而不是以插件数量衡量建设进度"
  summary: |
    统一治理渐进路径（先凭证托管与模型入口 → 用量归因与工具授权 → 路由实验与评估回归）的每步要有独立验收目标
    （凭证不再分散、用量可归属项目、拒绝可追溯），不以插件数量衡量进度。
  tags: [rule, gateway, adoption]

- id: p2-63
  title: 网关自身按关键基础设施设计
  type: principle
  source_chapter: 9.1.5 集中治理不等于集中所有执行
  source_quote: |
    "不能因为网关是多副本，就认为其依赖已经没有单点，也不能因为状态写入了 Redis，就认为所有一致性要求自然成立"
  summary: |
    网关是高价值关键基础设施：多副本只解决实例故障，受控共享状态支撑跨实例配额；共享存储、外部策略服务和
    内容检测服务的故障行为需逐项定义，不因多副本或写入 Redis 就默认无单点、一致性自然成立。
  tags: [principle, gateway, reliability]

- id: p2-64
  title: 统一接口必须声明能力子集，不静默忽略差异
  type: rule
  source_chapter: 9.2.2 统一接口与能力差异
  source_quote: |
    "统一接口首先应声明支持的能力子集，再约定不支持的参数如何返回错误，而不是静默忽略差异"
  summary: |
    协议适配（OpenAI 风格 / Anthropic 风格互转）中工具调用、图像输入、推理内容、缓存用量等字段并不总能无损映射：
    应声明支持的能力子集，约定不支持参数的报错方式；契约需记录适配器版本、实际 provider 与模型、请求别名和原始用量来源以便对账。
  tags: [rule, gateway, api-contract]

- id: p2-65
  title: 模型选择与推理端点选择分开
  type: principle
  source_chapter: 9.2.3 模型选择与端点选择应分开
  source_quote: |
    "模型选择与端点选择应分开"
  summary: |
    路由分两层决策：模型级（静态映射与权重、成本感知、语义路由——决策对象是模型别名/版本/服务）与
    端点级（同一模型的实例选择——依据队列、在途请求、缓存信号）。语义路由需分类器经评估且有明确回退路径；
    网关记录的前缀关联只是缓存可能可用的信号，不证明引擎仍持有该缓存。
  tags: [principle, gateway, routing]

- id: p2-66
  title: 错误分类先于重试
  type: rule
  source_chapter: 9.2.4 可靠性
  source_quote: |
    "错误分类应先于重试。限流响应需要结合重置时间、账户配额和替代端点策略处理；服务端错误只有在调用语义允许时才适合重试；认证错误一般应终止并修复凭证"
  summary: |
    重试前先分类错误：限流（结合重置时间/配额/替代端点）、服务端错误（仅调用语义允许时重试）、
    认证错误（终止并修复凭证）、上下文超限（不应简单截断历史——那会改变任务输入，应由应用/Harness 决定压缩、
    切长上下文模型或明确失败）。
  tags: [rule, gateway, retry]

- id: p2-67
  title: 尚未收到响应体不等于上游尚未执行
  type: rule
  source_chapter: 9.2.4 可靠性
  source_quote: |
    ""尚未收到响应体"不等于"上游尚未执行"：请求可能已经被计费，甚至已经触发某些副作用"
  summary: |
    流式响应一旦向客户端提交，网关通常不能透明重放整段结果。重试许可需同时考虑接口幂等性、
    上游执行不确定性和客户端协议，不能仅根据 HTTP 状态或是否收到首包决定。
  tags: [rule, gateway, retry, idempotency]

- id: p2-68
  title: 未经验证的兜底路径应返回可解释失败
  type: rule
  source_chapter: 9.2.4 可靠性
  source_quote: |
    "没有经过验证的兜底路径，应返回可解释的失败，而不是以'自动降级'为名改变业务契约"
  summary: |
    切换 provider 前检查工具、结构化输出、数据地域和模型能力兼容性；未经验证的兜底路径返回可解释失败，
    不以"自动降级"为名改变业务契约。每次实际尝试有独立 attempt 标识，受最大次数、总截止时间和成本额度约束。
  tags: [rule, gateway, fallback]

- id: p2-69
  title: 重试预算必须跨层传递，防乘积放大
  type: calculation
  source_chapter: 9.2.4 可靠性
  source_quote: |
    "如果 SDK 客户端对一次逻辑调用最多尝试 3 次，网关对每次转发又最多重试 3 次，那么一次用户可见的调用最多会到达上游 9 次……重试次数、并发压力和费用会以乘积方式放大"
  summary: |
    每层重试上限以乘积放大端到端尝试次数（SDK 3 次 × 网关 3 次 = 上游最多 9 次）。总截止时间应是绝对时间，
    剩余尝试额度由调用方给出、网关按 call_id 原子扣减，耗尽返回明确的预算耗尽错误；每次尝试用稳定 call_id 与
    attempt_id 关联，重试、摘除与模型切换分开记录。
  tags: [calculation, gateway, retry-budget]
  inputs: [SDK 层最大尝试次数, 网关层最大重试次数, 总截止时间, 成本上限]
  formula: "端到端最大上游尝试次数 ≈ SDK上限 × 网关上限（× 供应商侧不可控）；预算跨层传递：剩余额度按 call_id 原子扣减"
  output: 端到端尝试次数与费用受控，一次故障可归因为一次逻辑调用
  missing_conditions: 供应商侧重投行为不可控，按"可能已执行、可能已计费"处理

- id: p2-70
  title: 五类超时并存且不互相代替
  type: checklist
  source_chapter: 9.2.5 超时与流式解析
  source_quote: |
    "连接超时用于约束建连，首字节超时用于识别上游长时间无响应，TTFT 描述有效生成开始前的等待，流式空闲超时约束连续事件间隔，总截止时间控制整个调用的资源占用。它们可以同时存在，但不应互相代替"
  summary: |
    五类超时：连接超时（建连）、首字节超时（上游无响应）、TTFT（有效生成开始前等待）、流式空闲超时
    （连续事件间隔）、总截止时间（整个调用资源占用）。可同时存在但不互相代替——持续发心跳的流可永不触发
    空闲超时却始终没有有效内容。
  tags: [checklist, gateway, timeout]

- id: p2-71
  title: 缺少 usage 不能记为零消耗
  type: rule
  source_chapter: 9.2.5 超时与流式解析
  source_quote: |
    "缺少 usage 不能记录为零消耗，客户端取消也不能证明上游停止生成；这些记录需要保留估算或未结算状态，后续与服务商账单或执行记录对账"
  summary: |
    流式 usage 未返回或客户端取消时，消耗未知：不能记零、不能默认停止生成；保留估算或未结算状态，
    后续与账单或执行记录对账。SSE 解析需跨字节切片重组事件并设单事件长度、累计缓冲与解析时间上限。
  tags: [rule, gateway, metering]

- id: p2-72
  title: 速率限制、配额与严格预算是三种不同机制
  type: rule
  source_chapter: 9.2.6 预算语义
  source_quote: |
    ""请求前检查、响应后扣减"无法单独保证硬上限"
  summary: |
    四机制控制目标不同：请求速率限制（准入速度，不控 token 总成本）、token 时间窗限制（用量增长，存在在途消耗
    与并发超限窗口）、余额型配额（近实时额度，非严格并发预算）、严格预算（需可证明最大消耗、原子预留、结算与退款）。
  tags: [rule, gateway, budget]

- id: p2-73
  title: 严格预算准入不变量公式
  type: calculation
  source_chapter: 9.2.6 预算语义
  source_quote: |
    "可用额度 = 已授权额度 − 已结算消耗 − 在途预留；准入条件 = 本次最大可计费消耗 ≤ 可用额度。检查和预留必须在同一原子操作或等效一致性事务中完成"
  summary: |
    严格预算两个不变量：可用额度 = 已授权额度 − 已结算消耗 − 在途预留；
    准入条件 = 本次最大可计费消耗 ≤ 可用额度。检查与预留必须在同一原子操作（或等效一致性事务）中完成，
    调用结束按实际消耗幂等结算并释放多余预留。
  tags: [calculation, gateway, budget]
  inputs: [已授权额度, 已结算消耗, 在途预留, 本次最大可计费消耗]
  formula: "可用额度 = 已授权额度 − 已结算消耗 − 在途预留；准入 ⇔ 本次最大可计费消耗 ≤ 可用额度"
  units: token 数或金额（token 配额与金额预算分开建模）
  output: 严格上限保障（在计费边界和执行约束均成立时）
  missing_conditions: 若实际消耗可能超过预留（最大值不可靠），只能声明有界超额或软预算

- id: p2-74
  title: 检查后扣减模式的并发超限反例
  type: calculation
  source_chapter: 9.2.6 预算语义
  source_quote: |
    "假设余额为 100，两次最大消耗分别为 80 的请求同时通过余额检查，最终消耗就可能达到 160。响应后的原子扣减可以避免丢账，但不能撤销已经发生的消耗"
  summary: |
    "请求前检查余额、响应后扣减"无法保证硬上限：余额 100 时两个最大消耗 80 的并发请求都通过检查，最终消耗 160。
    仅限制并发只能缩小超支范围；只有对在途调用的最坏消耗也施加约束才可能形成严格上限。
  tags: [calculation, gateway, budget]
  inputs: [余额, 并发请求数, 每请求最大消耗]
  formula: "最坏实际消耗 = 并发通过检查的请求数 × 每请求最大消耗，可远大于余额"
  output: 证明阈值检查 + 后结算不构成硬上限
  missing_conditions: 无

- id: p2-75
  title: 预留不可靠时只能声明软预算
  type: rule
  source_chapter: 9.2.6 预算语义
  source_quote: |
    "如果实际消耗可能超过预留，说明所谓最大值并不可靠，此时只能声明有界超额或软预算，而不能继续承诺严格上限"
  summary: |
    若实际消耗可能超过预留（最大值估计不可靠），产品只能声明有界超额或软预算，不得继续承诺严格上限。
  tags: [rule, gateway, budget]

- id: p2-76
  title: 预留覆盖范围与流式中断退款规则
  type: rule
  source_chapter: 9.2.6 预算语义
  source_quote: |
    "预留需要覆盖输入、受约束的最大输出及该接口的其他计费项，重试也必须单独占用额度。对流式中断或执行结果未知的请求，不能因租约到期就无条件退款"
  summary: |
    预留覆盖输入 + 受约束的最大输出 + 其他计费项；重试单独占额。流式中断或结果未知的请求不因租约到期无条件退款，
    应先确认执行终止或按保守规则等待对账。
  tags: [rule, gateway, budget]

- id: p2-77
  title: 多层预算一致性与 token/金额分离
  type: rule
  source_chapter: 9.2.6 预算语义
  source_quote: |
    "组织、团队、项目和任务额度同时生效时，准入必须验证相关层级的可用额度，并避免只预留一层、另一层失败后留下悬挂记录。token 配额与金额预算还应分开"
  summary: |
    多层额度同时生效时准入须验证所有相关层级并避免悬挂预留；token 配额与金额预算分开建模——
    相同 token 数在不同模型、缓存命中类型和价格版本下费用不同。
  tags: [rule, gateway, budget]

- id: p2-78
  title: 语义缓存键必须隔离租户与版本
  type: rule
  source_chapter: 9.2.8 缓存与方案选择
  source_quote: |
    "缓存键还必须区分租户、权限范围、模型版本、系统提示和知识版本，不能让语义相似绕过数据隔离"
  summary: |
    语义缓存键必须区分租户、权限范围、模型版本、系统提示和知识版本；含副作用的工具调用不简单重放缓存结果；
    强时效或创造性任务明确适用边界与失效策略。缓存命中不代表零成本（向量计算、检索、缓存维护仍有开销）。
  tags: [rule, gateway, semantic-cache]

- id: p2-79
  title: 可解释可回退的简单策略优先于自动切换
  type: principle
  source_chapter: 9.2.8 缓存与方案选择
  source_quote: |
    "一个可解释、可回退的简单策略，往往比缺少测量依据的自动切换更容易建立可信的生产基线"
  summary: |
    路由稳定性、缓存命中、失败恢复和任务返工需共同评估（便宜模型导致大量返工则总成本可能更高）；
    可解释、可回退的简单策略优先于缺少测量依据的自动切换。
  tags: [principle, gateway, routing]

- id: p2-80
  title: 工具在清单中不等于已授权、健康或可执行
  type: principle
  source_chapter: 9.3.1 工具接通之后
  source_quote: |
    "一个工具出现在清单中，只能说明它被服务端通告；调用者是否有权执行、依赖系统是否可用、这次调用是否成功，仍需独立判断"
  summary: |
    工具出现在清单只说明被服务端通告；是否有权执行、依赖是否可用、调用是否成功需独立判断。
    协议代理、资源目录集成、授权和实际执行结果分开治理。
  tags: [principle, mcp, discovery-vs-authorization]

- id: p2-81
  title: MCP 准入四步：认证—协议校验—授权—转发
  type: rule
  source_chapter: 9.3.3 逐请求元数据
  source_quote: |
    "MCP 请求的准入可以按'认证—协议校验—授权—转发'四步理解……协议说明不是调用者身份证明"
  summary: |
    准入四步各自独立：认证（谁在调用，凭据主体映射）、协议校验（版本/头部镜像/方法约束）、
    授权（主体×工具×参数）、转发（按声明策略重构上游请求）。协议元数据只服务第二、四步，
    不替代身份和权限判断。
  tags: [rule, mcp, admission]

- id: p2-82
  title: 过滤 tools/list 不能替代 tools/call 再次鉴权
  type: rule
  source_chapter: 9.3.5 安全边界
  source_quote: |
    "过滤 tools/list 可以减少模型误选无权工具，但 tools/call 仍必须再次鉴权"
  summary: |
    可见性过滤（tools/list）只是减少模型误选；实际调用（tools/call）必须再次鉴权。
    Schema 校验只保证类型结构合规，不证明业务授权成立。
  tags: [rule, mcp, authorization]

- id: p2-83
  title: 权限取交集，不取模型自述
  type: rule
  source_chapter: 9.3.5 安全边界
  source_quote: |
    "权限应取调用者原有授权、工作负载授权、任务委托范围和资源侧限制的交集，不能由模型声明的身份、工具描述或 clientInfo 扩大"
  summary: |
    有效权限 = 调用者原有授权 ∩ 工作负载授权 ∩ 任务委托范围 ∩ 资源侧限制；
    模型声明身份、工具描述、clientInfo 均不得扩大权限。
  tags: [rule, mcp, permission]

- id: p2-84
  title: 网关"不可绕过"的五个部署前提
  type: checklist
  source_chapter: 9.3.5 安全边界
  source_quote: |
    "只有在凭证不下发、出口网络受控、服务发现和域名解析收敛、直连路径被禁止且旁路受到审计时，才有条件成为不可绕过的策略执行点"
  summary: |
    网关成为不可绕过策略执行点的五个前提：凭证不下发、出口网络受控、服务发现与域名解析收敛、
    直连路径被禁止、旁路受审计。若 Agent 仍持有直连凭证和网络出口，网关只是集中入口，不能声称"天然不可绕过"。
  tags: [checklist, gateway, security]

- id: p2-85
  title: 上游凭证不透传，尊重令牌受众
  type: rule
  source_chapter: 9.3.5 安全边界
  source_quote: |
    "上游凭证应来自明确的凭证托管或委托机制，不能任意透传为网关签发的下游令牌。长期密钥应由受控秘密管理设施提供"
  summary: |
    代理认证尊重令牌的目标受众与委托关系：HTTP 资源服务器校验令牌受众、权限和生命周期；
    上游凭证来自明确托管或委托机制，不任意透传；长期密钥由受控秘密管理设施提供，不进客户端配置、明文日志和版本库。
  tags: [rule, mcp, credentials]

- id: p2-86
  title: 健康检查分层，不为探活执行真实副作用
  type: rule
  source_chapter: 9.3.4 Registry 与服务发现
  source_quote: |
    "连接成功回答的是端点可达，协议发现回答的是能否通告兼容能力，受控的业务探测才可能验证工具依赖。对写入、支付等工具，不应为了健康检查而执行真实副作用"
  summary: |
    健康检查三层：连接成功=端点可达、协议发现=能力通告、受控业务探测=验证依赖。写入/支付类工具用专用探测或
    只读路径验证，不为健康检查执行真实副作用；真实模型探活可能产生费用，应限频并避免多副本重复放大。
  tags: [rule, mcp, health-check]

- id: p2-87
  title: 协议桥接方向与能力子集必须显式声明
  type: rule
  source_chapter: 9.3.6 协议桥接
  source_quote: |
    "协议策略、桥接方向和能力范围应当是显式配置和验收项，不能靠隐式探测与重试掩盖不兼容"
  summary: |
    跨代际桥接不是简单替换 HTTP 头：支持方向（如仅 Modern 下游 → Legacy 上游）、能力子集（发现/调用/订阅/Tasks）
    必须显式声明并验收；支持核心工具调用不等于支持所有扩展，协议无状态不等于后端业务无状态。
  tags: [rule, mcp, protocol-bridging]

- id: p2-88
  title: 出口请求重构，默认不透传下游标识
  type: rule
  source_chapter: 9.3.6 协议桥接
  source_quote: |
    "出口请求应按上游 RPC 重新构造，默认不透传下游 Cookie、会话标识、内部路由头和不适用的参数头"
  summary: |
    出口请求按上游 RPC 重新构造，默认丢弃下游 Cookie、会话标识、内部路由头；认证信息仅按明确策略生成或委托；
    日志避免记录带密钥的地址与请求头。
  tags: [rule, mcp, egress]

- id: p2-89
  title: REST-to-MCP 模板必须限制出站目标
  type: rule
  source_chapter: 9.3.7 实践案例
  source_quote: |
    "请求模板必须进行正确的参数编码，并限制可变主机、路径和出站目标，避免把模板工具变成服务端请求伪造（SSRF）的入口"
  summary: |
    把 REST API 暴露为 MCP 工具时：模板正确参数编码、限制可变主机/路径/出站目标，防止变成 SSRF 入口；
    响应可裁剪为必要字段减少无关上下文；服务端凭证由受控配置注入。
  tags: [rule, mcp, ssrf]

- id: p2-90
  title: 字符串前缀判断"只读 SQL"不可靠
  type: rule
  source_chapter: 9.3.7 实践案例
  source_quote: |
    "对数据库工具，使用字符串前缀判断'只读 SQL'并不可靠，应结合受限数据库账户、受支持的查询接口与资源端权限"
  summary: |
    数据库工具的只读保证不靠 SQL 前缀判断，而靠受限数据库账户、受支持的查询接口与资源端权限三重约束。
    参数级治理示例：支付工具约束金额阈值、收款方范围、审批和幂等键；文件读取约束允许目录、租户归属、符号链接与数据分级。
  tags: [rule, mcp, database-security]

- id: p2-91
  title: 自然语言治理意图先转结构化策略再执行
  type: rule
  source_chapter: 9.3.7 实践案例
  source_quote: |
    "它应先转化为结构化策略，经冲突检查、影响预览、回归验证和授权发布之后执行。运行时不宜临时依赖模型自由解释权限"
  summary: |
    自然语言可帮助管理员表达治理意图（如"生产环境只允许查询"），但必须先转化为结构化策略，经冲突检查、影响预览、
    回归验证和授权发布后执行；运行时不临时依赖模型自由解释权限，相同身份、资源和参数应得到可解释的一致判定。
  tags: [rule, policy-as-code, authorization]

- id: p2-92
  title: 工具输出不因经过网关就升级为高可信指令
  type: rule
  source_chapter: 9.3.7 实践案例
  source_quote: |
    "工具输出仍属于需要按来源和风险处理的数据，不能因为经过网关就自动升级为高可信指令"
  summary: |
    工具输出仍按来源和风险处理，不因过网关自动升级为可信指令；提示注入防护依赖权限隔离、参数约束、
    Harness 工具结果处理与资源端最小权限，不能由单一检测插件包办。
  tags: [rule, mcp, prompt-injection]

- id: p2-93
  title: 框架、网关、资源服务器纵深防御各留检查
  type: principle
  source_chapter: 9.3.8 集中入口是纵深防御的一层
  source_quote: |
    "框架理解当前任务意图，网关执行跨框架的统一策略，资源服务器限制最终操作。三者协作提供的是纵深防御，而不是由任何一层宣称整个调用链已经绝对安全"
  summary: |
    框架（理解任务意图）、网关（跨框架统一策略）、资源服务器（限制最终操作）各自保留必要检查；
    任何一层不得宣称整个调用链绝对安全。集中入口的另一面：网关成为高价值安全目标，需保护配置、秘密、管理接口和审计数据。
  tags: [principle, defense-in-depth, gateway]

- id: p2-94
  title: 任务成本归因链路
  type: calculation
  source_chapter: 9.4.5 从 Session 分析扩展到 Task 分层账本
  source_quote: |
    "task_id → session_id → turn_id → model/tool call → attempt_id"
  summary: |
    Session 聚合只回答"一段交互花多少成本"，Task 账本才回答长程任务总成本。归因下钻链路：
    task_id → session_id → turn_id → model/tool call → attempt_id。多 Agent 用 parent_task_id 归集；
    Session 承载多任务/并行 Turn/共享调用时需关联表和明确分摊规则，不能任意重复归因。
  tags: [calculation, observability, cost-attribution]
  inputs: [task_id, session_id, turn_id, call_id, attempt_id, 实际用量与价格版本]
  formula: "归因链路：task_id → session_id → turn_id → model/tool call → attempt_id"
  output: 长程任务总成本、完成效率、恢复成本（Attempt 层看哪次重试耗成本；Call/Turn 看本轮为何慢；Session 看重复调用/上下文膨胀；Task 看跨会话总账）
  missing_conditions: 网关记录不冒充完整任务账本（Runtime 资源费、绕过网关路径、审批等待、业务恢复事件需各责任系统补充）

- id: p2-95
  title: 关联标识的权威来源
  type: rule
  source_chapter: 9.4.5 从 Session 分析扩展到 Task 分层账本
  source_quote: |
    "task_id 应来自业务应用，session_id 和 turn_id 应来自其权威执行上下文，网关可以生成请求或尝试标识"
  summary: |
    标识权威分工：task_id 来自业务应用；session_id、turn_id 来自其权威执行上下文；网关只生成请求/尝试标识。
    缺失可靠关联时记为待归因，不自行编造业务 Task。
  tags: [rule, observability, identifiers]

- id: p2-96
  title: 客户端关联头必须绑定已认证主体
  type: rule
  source_chapter: 9.4.5 从 Session 分析扩展到 Task 分层账本
  source_quote: |
    "入口收到客户端关联头时，需要绑定已认证主体并校验租户范围，防止通过伪造 Task 或项目标识转移费用、污染他人的账本"
  summary: |
    客户端传来的 Task/项目关联头不可直接信任：须绑定已认证主体并校验租户范围，防止伪造标识转移费用、污染他人账本。
  tags: [rule, gateway, anti-spoofing]

- id: p2-97
  title: 网关不解释业务 State、不定义 Checkpoint
  type: rule
  source_chapter: 9.4.2 任务关联对象与责任来源
  source_quote: |
    "即使某种实现出于路由需要缓存了不透明的执行引用，也不应解释业务 State、定义 Checkpoint 结构或决定从哪个业务步骤恢复。入口实例重启不应成为任务真相丢失的原因"
  summary: |
    网关可保存路由亲和键、任务标识映射、在途计数、配额预留和观测关联，但不解释业务 State、不定义 Checkpoint 结构、
    不决定从哪个业务步骤恢复；Task/State/Checkpoint/Outcome 的权威在业务模型、Runtime 与获授权 Verifier。
  tags: [rule, gateway, responsibility]

- id: p2-98
  title: 配额绑定认证主体与财务归属，两维正交
  type: rule
  source_chapter: 9.4.4 租户隔离
  source_quote: |
    "配额应绑定经过认证的主体和明确的财务归属，而不是客户端 IP 或连接数……组织、团队、项目则描述财务归属，两者是正交维度"
  summary: |
    配额绑定认证主体（人/工作负载/Agent 实例/Task）与财务归属（组织/团队/项目）两个正交维度，
    不绑客户端 IP 或连接数；一个 Agent 可服务多个项目，一个项目可用多个 Agent，不混成固定单一层级。
  tags: [rule, gateway, quota]

- id: p2-99
  title: 入口并发与执行任务并发分开治理
  type: rule
  source_chapter: 9.4.4 租户隔离
  source_quote: |
    "HTTP 请求结束之后，异步任务可能继续运行；只有 Runtime 或相应调度系统能准确判断执行槽位是否释放"
  summary: |
    入口在途请求 ≠ Runtime 活动任务：HTTP 请求结束后异步任务可能继续运行，执行槽位释放只能由 Runtime/调度系统判断。
    网关限入口并发与发起速率，任务并发与执行系统账本协同。借用空闲容量不等于可收回已执行操作或已产生费用，
    抢占策略需明确取消、检查点和补偿行为。
  tags: [rule, gateway, concurrency]

- id: p2-100
  title: 网关日志与 Trace 不冒充权威任务账本
  type: rule
  source_chapter: 9.4.5 从 Session 分析扩展到 Task 分层账本
  source_quote: |
    "链路追踪（Trace）帮助关联调用路径，但采样、缺失 span 和跨恢复分段使其不能自动成为财务或 Task 的权威账本"
  summary: |
    网关记录只是调用账本的一部分：Runtime 资源费、绕过网关的受控路径、审批等待和业务恢复事件需各责任系统补充；
    Trace 因采样、缺失 span、跨恢复分段不能自动成为财务或 Task 权威账本。
  tags: [rule, observability, accounting]

- id: p2-101
  title: 模型工具调用意图不等于工具已执行
  type: rule
  source_chapter: 9.4.5 从 Session 分析扩展到 Task 分层账本
  source_quote: |
    "模型返回的工具调用意图只是模型输出，不代表工具已经执行；工具实际费用应以执行记录结算"
  summary: |
    模型返回的 tool_calls 只是模型输出，不代表工具已执行；工具实际费用以执行记录结算并通过调用标识关联。
    记录"模型生成的 tool_calls"与"工具实际执行结果"应使用可区分的事件类型。
  tags: [rule, observability, intent-vs-execution]

- id: p2-102
  title: 请求成功率与业务任务完成率分别统计
  type: rule
  source_chapter: 9.4.7 结果判定与设计权衡
  source_quote: |
    "网关的请求成功率与业务任务完成率分别统计……避免把 HTTP 成功、会话结束或模型自述完成计入业务成功"
  summary: |
    请求成功率与业务任务完成率分开统计；Outcome 由业务应用或获授权验收组件依据成功标准、Task State 与 Evidence 判定，
    不把 HTTP 成功、会话结束或模型自述完成计入业务成功。
  tags: [rule, metrics, outcome]

- id: p2-103
  title: Task 委托四条硬约束
  type: rule
  source_chapter: 9.5.2 身份到转发的映射
  source_quote: |
    "Task 委托必须可验证、可撤销、范围清晰，且不能超出委托者原有权限，也不要求每个 Task 都签发独立令牌"
  summary: |
    Task 委托硬约束：可验证、可撤销、范围清晰、不超委托者原有权限；不要求每个 Task 签发独立令牌。
    入口必须清除或覆盖客户端伪造的内部主体头，并为跨代理传递定义信任边界。
  tags: [rule, delegation, identity]

- id: p2-104
  title: 放行前完成三准入并记录判定依据
  type: rule
  source_chapter: 9.5.3 策略执行
  source_quote: |
    "网关必须在放行前完成认证、鉴权与预算准入，并为每次判定记录命中的规则、版本和拒绝原因，避免权限矩阵变成不可解释的黑箱"
  summary: |
    认证、鉴权与预算准入三项都在放行前完成；每次判定记录命中的规则、版本和拒绝原因。
    未通过的请求终止并记录判定，需要审批的进入审批工作流。
  tags: [rule, gateway, policy-enforcement]

- id: p2-105
  title: 放行日志不能代替执行成功日志
  type: rule
  source_chapter: 9.5.3 策略执行
  source_quote: |
    "审计既记录准入依据，也记录实际执行结果，不能以'放行日志'代替'执行成功日志'"
  summary: |
    审计双记录：准入依据（命中的策略与版本）+ 实际执行结果。只记放行不记结果不构成完整审计。
  tags: [rule, audit, gateway]

- id: p2-106
  title: 审批未决不得执行，挂起不占连接
  type: rule
  source_chapter: 9.5.4 审批如何约束转发
  source_quote: |
    "审批未决的请求不得转为实际执行。挂起期间网关不长时间持有 HTTP 连接，等待与通知由业务侧承接"
  summary: |
    审批是持久化的业务状态，不是网关里挂住 HTTP 请求的同步步骤：未决期间不执行高风险操作，连接可结束，
    等待与通知由业务侧承接；审批记录含调用者、工具及版本、参数摘要、风险与有效期。
  tags: [rule, approval, gateway]

- id: p2-107
  title: 批准不等于放行，执行前必须重新准入
  type: rule
  source_chapter: 9.5.4 审批如何约束转发
  source_quote: |
    "批准不意味着后续必定放行。等待期间身份可能过期、参数可能改变、预算可能被其他调用占用，因此执行前必须重新准入"
  summary: |
    批准后执行前重新校验身份、委托范围、参数摘要与预算预留（重新准入）。审批记录绑定工具、版本、参数摘要和
    任务上下文；回调需认证并防重放，重复回调和调用重试不能产生重复副作用。
  tags: [rule, approval, re-admission]

- id: p2-108
  title: 审批超时不自动降级为只读执行
  type: rule
  source_chapter: 9.5.4 审批如何约束转发
  source_quote: |
    "也不应将'审批超时'自动解释为'改成只读执行'。这会改变原始请求含义，可能造成意外数据暴露；如需只读替代方案，应形成新的、可见的请求并重新授权"
  summary: |
    审批超时或撤销后保持未执行状态，不自动降级为只读执行（会改变原始请求含义、可能造成数据暴露）；
    如需只读替代方案，应形成新的可见请求并重新授权。无幂等支持的外部操作结果未知时交由业务查询或补偿，不盲目重试。
  tags: [rule, approval, timeout]

- id: p2-109
  title: 审计材料不天然构成业务 Evidence
  type: principle
  source_chapter: 9.5.5 审计材料与业务 Evidence 的区别
  source_quote: |
    "它为复盘提供可追溯材料，但并不天然构成业务判定需要的 Evidence。哪些来源、完整性保证和内容可以用于判断任务成功，应由成功标准与 Verifier 明确"
  summary: |
    结构化审计记录（谁在何时申请什么操作、命中哪条策略、执行结果）是复盘材料，不天然构成业务判定的 Evidence；
    哪些来源与完整性保证可用于判定任务成功，由成功标准与 Verifier 明确。
  tags: [principle, audit, evidence]

- id: p2-110
  title: 检测失效行为按风险预先定义
  type: rule
  source_chapter: 9.5.5 审计材料与业务 Evidence 的区别
  source_quote: |
    "检测失效时放行、拒绝或转人工，应按风险预先定义，而不是在故障时临时决策"
  summary: |
    DLP 与内容安全检测失效时的行为（放行/拒绝/转人工）按风险预先定义，不在故障时临时决策。
  tags: [rule, dlp, fail-safe]

- id: p2-111
  title: 实时准入账与财务报表须有明确对账规则
  type: rule
  source_chapter: 9.5.6 组合式实现与财务归因
  source_quote: |
    "实时准入账与财务报表可以存在时延差异，但应有明确的对账规则，不能声称二者'天然一致'"
  summary: |
    FinOps 基础是可核对的主体、用量和价格：组织/团队/项目映射保留历史有效期，实际模型、缓存 token 类别、
    价格版本、币种和账期共同决定费用；实时账与报表的差异要有明确对账规则。
  tags: [rule, finops, reconciliation]

- id: p2-112
  title: Evaluation 不直接拥有生产发布权
  type: principle
  source_chapter: 9.6.1 从运行数据到受控发布
  source_quote: |
    "Evaluation 不天然拥有发布授权，也不应直接修改生产路由或预算。完整路径应是观测形成候选建议，建议进入构建与验证，再经过治理授权、发布门禁和灰度进入控制面"
  summary: |
    评估系统生成评分与诊断，但不直接修改生产路由或预算。完整路径：观测形成候选建议 → 构建与验证 →
    治理授权、发布门禁和灰度 → 控制面。
  tags: [principle, evaluation, release-gate]

- id: p2-113
  title: 候选变更进入生产的六阶段链路
  type: checklist
  source_chapter: 9.6.5 候选变更进入生产的完整链路
  source_quote: |
    "观测与诊断→候选建议→构建与验证→治理授权与发布门禁→灰度与控制面发布→运行反馈"
  summary: |
    六阶段链路及准入条件：观测与诊断（数据来源与口径明确）→ 候选建议（变更范围/预期收益/约束清楚）→
    构建与验证（协议、质量、授权、成本和故障测试达标）→ 治理授权与发布门禁（权限、依赖检查、回滚方案）→
    灰度与控制面发布（分批生效、监测达标，异常暂停或回滚）→ 运行反馈（回流数据集）。
  tags: [checklist, release, change-management]

- id: p2-114
  title: 自动化缩短审批但不消除授权边界
  type: rule
  source_chapter: 9.6.5 候选变更进入生产的完整链路
  source_quote: |
    "低风险调参可以在预先批准的范围、幅度和资源集合内自动进行，仍需留存版本、验证结果及回滚记录；超出范围的变化必须重新授权"
  summary: |
    自动化只缩短时间不消除授权边界：低风险调参在预先批准的范围、幅度和资源集合内自动进行并留存版本/验证/回滚记录；
    超出范围必须重新授权。缓存策略可能影响数据隔离和新鲜度，不能仅凭名称认定低风险。
  tags: [rule, automation, governance]

- id: p2-115
  title: 运行时自适应只在已授权候选集合内工作
  type: rule
  source_chapter: 9.6.6 区分运行时自适应与治理策略变更
  source_quote: |
    "算法只能在已授权的候选集合和约束内工作，不能自行扩大模型范围、数据地域、工具权限或预算"
  summary: |
    已发布路由策略内部的在线端点选择（如 EWMA 自适应评分）是运行时自适应，不是 Evaluation 获得发布权：
    不能自行扩大模型范围、数据地域、工具权限或预算；效果需在目标负载下测量，不能由存在自适应评分推导质量必然改善。
  tags: [rule, gateway, adaptive-routing]

- id: p2-116
  title: 回滚不止恢复配置文本
  type: rule
  source_chapter: 9.6.7 让闭环可验证、可停止、可回滚
  source_quote: |
    "回滚不仅是恢复配置文本，还要考虑已执行的工具副作用、未结算额度和仍在运行的任务。网关可以回滚入口策略，业务系统仍需处理变更已经造成的业务结果"
  summary: |
    回滚需考虑已执行的工具副作用、未结算额度和仍在运行的任务；网关只能回滚入口策略，业务系统另行处理已造成的业务结果。
    观测/预算/授权系统不可用时行为由已定义策略决定，不同依赖不共用未经验证的降级口号。
  tags: [rule, rollback, operations]

- id: p2-117
  title: 指标维度控制基数
  type: rule
  source_chapter: 9.6.3 可观测性
  source_quote: |
    "模型、路由和受控的服务维度适合指标聚合，Task、Session 和调用标识通常更适合日志、事件或 Trace；将所有动态标识都写为指标标签可能导致监控系统资源失控"
  summary: |
    低基维度（模型、路由、受控服务）进指标聚合；高基动态标识（Task/Session/调用标识）进日志、事件或 Trace，
    不写成指标标签。平均时延从已确认的计数器或直方图计算并与分位数一起观察。
  tags: [rule, metrics, cardinality]

- id: p2-118
  title: AI 观测五类数据的常见误区清单
  type: checklist
  source_chapter: 9.6.3 可观测性
  source_quote: |
    "将缺失 usage 当作零，或把 credits 当作统一货币……以 HTTP 首包代替首个有效 token……只用 HTTP 200 判断成功……只记录放行，不记录最终结果……将一条 Trace 或 Session 当作完整 Task"
  summary: |
    五类误区：用量成本（缺失 usage 记为零、credits 当统一货币）；时延（HTTP 首包代替首个有效 token）；
    可靠性（只用 HTTP 200 判断成功）；治理（只记录放行不记录结果）；关联（一条 Trace 或 Session 当完整 Task）。
  tags: [checklist, metrics, anti-pattern]

- id: p2-119
  title: OpenTelemetry GenAI 约定处于演进态，采用须固定版本
  type: rule
  source_chapter: 9.6.3 可观测性
  source_quote: |
    "采用时应固定版本，区分稳定通用属性与仍在演进的生成式字段，并为版本迁移保留映射，不应称其为已全部稳定的统一标准"
  summary: |
    OpenTelemetry 生成式 AI 语义约定（gen_ai.*）整体处于 Development 状态：采用时固定版本、区分稳定与演进字段、
    保留版本迁移映射，不称其为已全部稳定的统一标准。
  tags: [rule, observability, opentelemetry]

- id: p2-120
  title: 内容日志不保证可重放整个任务
  type: rule
  source_chapter: 9.6.3 可观测性
  source_quote: |
    "内容日志可以支持复核，却不保证可重放整个任务。外部系统状态、工具副作用、资源版本和缺失事件都可能影响重放"
  summary: |
    内容日志支持复核但不可重放任务（外部系统状态、工具副作用、资源版本、缺失事件都影响重放）；
    可重复实验应在受控环境固定输入和依赖，禁止对生产副作用无保护重演。
  tags: [rule, observability, replay]

# ============ 第 13 章 可观测性与审计 ============

- id: p2-121
  title: 请求成功不等于任务成功
  type: principle
  source_chapter: 13.1.2 AI原生应用的观测挑战
  source_quote: |
    "Agent 的问题还可能表现为错误规划、无效检索、工具误用，或者任务表面完成但结果不符合预期，因此'请求成功'并不等同于'任务成功'"
  summary: |
    即使输入相同模型也可能生成不同响应、选择不同工具或路径；Agent 问题可能表现为错误规划、无效检索、工具误用、
    表面完成但结果不符预期。观测必须区分请求成功与任务成功。
  tags: [principle, observability, task-success]

- id: p2-122
  title: AI 可观测性四维度
  type: checklist
  source_chapter: 13.1.3 核心维度与观测对象
  source_quote: |
    "运行表现……稳定性与性能……成本与效率……行为审计"
  summary: |
    观测四维度：运行表现（任务是否完成、输出是否符合预期）、稳定性与性能（错误/超时/重试/异常循环/延迟）、
    成本与效率（Token 消耗、模型与工具调用成本、资源效率）、行为审计（关键操作完整记录、可追溯主体过程结果）。
  tags: [checklist, observability, dimensions]

- id: p2-123
  title: 观测对象三层
  type: checklist
  source_chapter: 13.1.3 核心维度与观测对象
  source_quote: |
    "任务与交互层……Agent执行层……AI基础设施层"
  summary: |
    观测对象三层：任务与交互层（用户请求、消息轮次、会话、任务）、Agent 执行层（Agent、工作流、模型调用、检索、
    工具调用）、AI 基础设施层（AI 网关、推理引擎、执行沙箱、Pod 与 GPU 资源），通过指标/日志/Trace/事件建立跨层关联。
  tags: [checklist, observability, layers]

- id: p2-124
  title: 审计、可观测、评测与 Guardrail 职责区分
  type: principle
  source_chapter: 13.4.1 Agent审计的定义与边界
  source_quote: |
    "可观测性侧重还原'发生了什么、问题在哪里'；评测侧重衡量'任务效果是否达到预期'；审计侧重判断'行为是否合规、责任如何归因、证据是否完整'"
  summary: |
    四者职责：可观测性=发生了什么、问题在哪里；评测=效果是否达预期；审计=行为是否合规、责任如何归因、证据是否完整；
    Guardrail=执行前/中实施允许、拒绝、脱敏、审批和限权控制。
  tags: [principle, audit, responsibility]

- id: p2-125
  title: 事后审计不能撤销已发生的操作
  type: principle
  source_chapter: 13.4.1 Agent审计的定义与边界
  source_quote: |
    "事后审计可以发现风险并推动策略改进，但不能撤销已经发生的文件修改、外部请求或数据泄露"
  summary: |
    审计是事后能力：可发现风险并推动策略改进，但不能撤销已发生的文件修改、外部请求或数据泄露——
    事前/事中控制须由 Guardrail 承担。
  tags: [principle, audit, guardrail]

- id: p2-126
  title: Agent 审计最小证据链四要素
  type: checklist
  source_chapter: 13.4.2 审计事实与可复核证据链
  source_quote: |
    "主体与范围……意图与授权……执行事实……结果与证据"
  summary: |
    最小证据链四要素：主体与范围（用户/Agent/子 Agent/服务身份及租户、应用、会话、任务、Trace、轮次标识）、
    意图与授权（用户目标、指令来源、可用工具、权限范围、审批、策略版本）、执行事实（模型请求响应、检索记忆操作、
    工具参数结果、进程文件网络凭证副作用）、结果与证据（操作状态、受影响对象、风险命中、原始事件引用、时间戳、
    检测规则或模型版本）。只记 Prompt、最终回答或工具名称之一都不足以构成完整审计。
  tags: [checklist, audit, evidence-chain]

- id: p2-127
  title: 模型调用意图不等于工具成功执行
  type: rule
  source_chapter: 13.4.2 审计事实与可复核证据链
  source_quote: |
    "但不能把'模型提出调用意图'直接当成'工具已经成功执行'"
  summary: |
    应用侧埋点表达任务/消息/工具的业务语义，运行环境遥测验证进程/文件/网络层真正发生的副作用，
    二者通过稳定会话、Trace、工具调用和进程关系关联；模型提出调用意图不直接当作工具已成功执行。
  tags: [rule, audit, intent-vs-execution]

- id: p2-128
  title: eBPF 证据须互证，不单独定论
  type: rule
  source_chapter: 13.4.2 审计事实与可复核证据链
  source_quote: |
    "应与应用埋点、Hook、AI 网关日志和策略记录互证，不能单独据此认定越权或攻击"
  summary: |
    eBPF 补充"实际发生"的运行时证据（进程创建退出、命令行、文件读写、网络连接），把工具调用意图与系统副作用关联，
    为异构/闭源 Agent 提供统一事实入口；但缺少用户目标、业务授权和应用上下文，对加密流量可见性有限，
    须与埋点、Hook、网关日志、策略记录互证。
  tags: [rule, audit, ebpf]

- id: p2-129
  title: 原始事实追加写入，结论作派生记录
  type: rule
  source_chapter: 13.4.2 审计事实与可复核证据链
  source_quote: |
    "原始事实宜采用追加写入并保留稳定标识，检测结论通过引用证据形成派生记录，避免为修正结论而改写原始事件"
  summary: |
    原始事实追加写入并保留稳定标识；检测结论通过引用证据形成派生记录，不回改原始事件。
    审计数据本身是高敏资产：最小采集、脱敏、独立存储、访问控制、加密、租户隔离和保留期限管理；
    完整审计不等于无边界保存全部内容。
  tags: [rule, audit, data-integrity]

- id: p2-130
  title: 审计规则围绕信任边界组织，而非搜索关键词
  type: principle
  source_chapter: 13.4.3 面向风险的审计检测
  source_quote: |
    "审计规则应围绕信任边界、授权范围和实际后果组织，而不只是搜索危险关键词"
  summary: |
    Agent 风险来自非确定性决策与可改变外部状态的权限叠加，审计规则应围绕信任边界、授权范围和实际后果组织，
    而非只搜危险关键词。典型场景：敏感数据流转、提示注入与目标劫持、工具误用与危险操作、身份与权限滥用、
    上下文与供应链污染。
  tags: [principle, audit, threat-modeling]

- id: p2-131
  title: OWASP 清单是威胁建模起点，不是认证标准
  type: rule
  source_chapter: 13.4.3 面向风险的审计检测
  source_quote: |
    "它适合作为威胁建模的起点，而不是认证标准或穷尽清单。落地时仍需结合具体业务定义'允许做什么、需要审批什么、绝不能做什么'"
  summary: |
    OWASP Top 10 for Agentic Applications 适合作为威胁建模起点而非认证标准或穷尽清单：同一条命令或数据访问
    在不同主体、环境和任务授权下可能对应完全不同的风险等级，需结合业务定义允许/需审批/绝不能做。
  tags: [rule, audit, owasp]

- id: p2-132
  title: 分层审计链路：事实→候选→研判→事件
  type: framework-rule
  source_chapter: 13.4.4 从候选信号到可处置事件
  source_quote: |
    ""原始事实 → 候选信号 → 上下文研判 → 已确认事件"的分层链路"
  summary: |
    审计分层链路：原始事实 → 候选信号（确定性规则/敏感信息识别/策略匹配/异常检测）→ 上下文研判
    （按会话、任务、主体和风险对象回捞上下文，检查指令来源、授权范围、工具是否真正执行、副作用与影响扩散）→
    已确认事件。
  tags: [rule, audit, pipeline]

- id: p2-133
  title: 研判三状态，证据缺失不是安全结论
  type: rule
  source_chapter: 13.4.4 从候选信号到可处置事件
  source_quote: |
    "证据足以确认风险；证据足以说明风险链不成立或行为仍在授权范围内；关键证据缺失，暂时无法判断。第三种状态不能被当成安全结论"
  summary: |
    研判结果三状态：确认风险 / 风险链不成立 / 关键证据缺失暂时无法判断。第三种不能当成安全结论。
  tags: [rule, audit, triage]

- id: p2-134
  title: 严重性与置信度分别记录
  type: rule
  source_chapter: 13.4.4 从候选信号到可处置事件
  source_quote: |
    "影响很大但证据不完整的事件，和证据充分但影响有限的事件，不应进入同一优先级队列"
  summary: |
    严重性与置信度两个维度分别记录排序：影响大但证据不完整 vs 证据充分但影响有限，不进同一优先级队列。
  tags: [rule, audit, prioritization]

- id: p2-135
  title: 模型可辅助审计降噪但不作唯一证据源
  type: rule
  source_chapter: 13.4.4 从候选信号到可处置事件
  source_quote: |
    "送入模型的审计材料本身可能含有提示词注入，因此需要把数据与指令隔离，限制模型可用工具，使用结构化输出，并保留规则版本、证据引用和判定说明"
  summary: |
    模型用于理解长上下文、归纳行为链和辅助降噪，但不作唯一证据来源。防护四措施：数据与指令隔离、
    限制模型可用工具、结构化输出、保留规则版本/证据引用/判定说明；模型解释属待验证输出，不替代原始事实、
    可复算规则和人工复核。
  tags: [rule, audit, llm-assisted]

- id: p2-136
  title: 检测与拦截分开建模
  type: principle
  source_chapter: 13.4.5 调查、处置与控制闭环
  source_quote: |
    "适合实时阻断的策略必须确定、低延迟、可解释、可回放，并具备影子运行、灰度发布和快速回滚能力；上下文不足或依赖开放式语义判断的结论，更适合进入异步调查和人工确认"
  summary: |
    审计结论可反哺 Guardrail，但检测与拦截分开建模：适合实时阻断的策略须确定、低延迟、可解释、可回放，
    具备影子运行/灰度/快速回滚；上下文不足或依赖开放式语义判断的结论进异步调查和人工确认。
  tags: [principle, guardrail, detection-vs-blocking]

- id: p2-137
  title: 审计质量五指标
  type: checklist
  source_chapter: 13.4.5 调查、处置与控制闭环
  source_quote: |
    "并通过误报、漏报、平均确认时间、平均关闭时间和复发率持续评估审计质量"
  summary: |
    审计质量用五指标持续评估：误报、漏报、平均确认时间（MTTA）、平均关闭时间（MTTR）、复发率。
    高质量审计交付物是可调查、可分派、可验证的风险工作队列，而非不断增长的告警列表。
  tags: [checklist, audit, metrics]

- id: p2-138
  title: 审计闭环五步
  type: framework-rule
  source_chapter: 13.4.5 调查、处置与控制闭环
  source_quote: |
    "最终闭环应是'观测事实 → 审计判断 → 调查处置 → 策略更新 → 执行验证'，而不是把所有可疑信号直接变成同步阻断"
  summary: |
    审计闭环五步：观测事实 → 审计判断 → 调查处置 → 策略更新 → 执行验证。关闭后验证旧凭证是否继续使用、
    同类行为是否复发、策略是否实际生效。
  tags: [rule, audit, closed-loop]

# ============ 第 14 章 安全 ============

- id: p2-139
  title: Agent 安全必须防护与管控兼具（盾与缰绳）
  type: principle
  source_chapter: 14.1 Agent安全风险与挑战
  source_quote: |
    "盾保证它不被利用，缰绳保证它不被放纵"
  summary: |
    Agent 既是被攻击对象（供应链投毒、提示注入越狱、系统网络入侵）也是行为主体（持身份权限、自主调工具、
    接触敏感数据，未被攻击也可能越界）。防护=盾（全栈纵深设防，不被打穿）；管控=缰绳（身份鉴权、意图识别、
    逐次调用校验、高危操作二次授权、数据出域阻断，不越边界）。
  tags: [principle, security, protection-and-control]

- id: p2-140
  title: Agent 本身应被视为潜在攻击发起点
  type: principle
  source_chapter: 14.2.1 背景和挑战
  source_quote: |
    "企业部署的 Agent 本身也需要被视为潜在的攻击发起点，能够访问的中间服务也可能成为突破网络边界的通道"
  summary: |
    高能力 Agent 能持续探索攻击路径、根据反馈调整策略、串联多系统漏洞（如 OpenAI 评测 Agent 借 Artifactory
    代联网入侵 Hugging Face 事件）；企业部署的 Agent 自身是潜在攻击发起点，可访问的中间服务可能成为突破网络边界的通道。
  tags: [principle, security, attack-origin]

- id: p2-141
  title: 外部内容与任务指令分开处理
  type: rule
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "对于网页、邮件、文档和工具返回值等外部内容，应保留来源信息，将其与任务指令分开处理，避免外部数据经过摘要、转述或多次调用后被当作新的执行要求"
  summary: |
    网页/邮件/文档/工具返回值等外部内容保留来源信息，与任务指令分开处理，防止经摘要、转述或多次调用后
    被当作新的执行要求；结合安全护栏识别提示注入与可疑调用，按原任务检查实际操作的目标、参数和业务范围。
  tags: [rule, prompt-injection, data-instruction-separation]

- id: p2-142
  title: 工具接入与更新时必须评估行为影响
  type: rule
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "工具的实现、描述、参数定义及版本变更，都可能改变 Agent 的行为，需要在接入和更新时进行评估"
  summary: |
    MCP 服务、连接器、技能配置纳入管理；工具的实现、描述、参数定义及版本变更都可能改变 Agent 行为，
    接入和更新时评估；对是否夹带额外操作、实际行为是否符合声明需审查和验证。
  tags: [rule, tool-governance, supply-chain]

- id: p2-143
  title: 请求发出或业务提交前校验执行范围
  type: rule
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "应根据业务需要明确 Agent 可访问的目标、可执行的操作和可处理的数据范围，并在实际请求发出或业务提交前校验"
  summary: |
    明确 Agent 可访问目标、可执行操作、可处理数据范围，并在实际请求发出或业务提交前校验：
    数据库访问参数化查询、业务接口检查操作对象/数量/状态、网络请求校验实际连接地址与重定向、
    输出在渲染/发送/发布前完成安全处理和敏感数据检查。
  tags: [rule, execution-boundary, validation]

- id: p2-144
  title: 高影响操作设确认环节且确认内容与执行一致
  type: rule
  source_chapter: 14.2.3 防护思路
  source_quote: |
    "对于影响较大的操作，还应按业务规则设置确认环节，并保证确认内容与实际执行一致"
  summary: |
    影响较大的操作按业务规则设置确认环节（人工确认/二次授权），且确认内容与实际执行一致——防止确认的是 A、执行的是 B。
  tags: [rule, hitl, confirmation]

- id: p2-145
  title: 拒绝钱包攻击（DoW）：约束累计消耗
  type: rule
  source_chapter: 14.2.2 新的攻击面
  source_quote: |
    "攻击者无需让某次请求异常庞大，只需不断制造补充工作、失败反馈或新的依赖，就可能使任务长时间消耗 Token、付费 API、并发槽位和下游服务容量"
  summary: |
    DoW 利用重试、并发、递归委派和并行调用制造补充工作，即使每次调用符合接口限制、最终回答正常，
    累计消耗仍可能远超合理成本。防护：对请求大小、调用频率和高成本操作设置约束，按异常行为限速或阻断。
  tags: [rule, dow, resource-control]

- id: p2-146
  title: AI 安全护栏九大防护维度
  type: checklist
  source_chapter: 14.3.2 防护机制
  source_quote: |
    "内容合规审核……提示词攻击防御……敏感信息防护……恶意文件检测……恶意 URL 拦截……提示词反爬机制……模型越狱检测……模型幻觉抑制……数字水印标识"
  summary: |
    面向 AI 应用的护栏九维度：内容合规审核、提示词攻击防御（同步检测+异步分析多模型混合）、敏感信息防护（PII 识别脱敏）、
    恶意文件检测、恶意 URL 拦截、提示词反爬（防 RAG 知识库系统性窃取）、模型越狱检测（输出侧再判）、
    模型幻觉抑制（上下文一致性比对+外部知识核对）、数字水印标识（AIGC 生成有痕、责任可溯）。
  tags: [checklist, guardrail, nine-dimensions]

- id: p2-147
  title: 大模型数据安全六阶段
  type: checklist
  source_chapter: 14.4.1 背景和挑战
  source_quote: |
    "大模型应用过程中经历了6个数据阶段，数据采集和接入、数据传输、数据存储、数据访问、数据使用、数据删除等"
  summary: |
    数据安全保障覆盖六阶段：采集和接入、传输、存储、访问、使用、删除；保障对象包括训练数据、提示词、
    知识库、多模态数据与日志。
  tags: [checklist, data-security, lifecycle]

- id: p2-148
  title: 数据分类分级 S1-S4
  type: rule
  source_chapter: 14.4.3 构建全数据全生命周期的安全保障
  source_quote: |
    "从数据价值、敏感性、数据合规和业务需求等多角度将数据分为4个安全级别：S1、S2、S3、S4"
  summary: |
    分类分级是数据安全和治理的基础：按数据价值、敏感性、合规和业务需求分为 S1-S4 四级，
    基于国标/行业模板（个人信息、金融、车联网等）+ 用户自定义模板生成安全策略；高敏感数据靠 AI 自适应调整识别范围。
  tags: [rule, data-security, classification]

- id: p2-149
  title: 提示词推理加密的最小必要解密原则
  type: rule
  source_chapter: 14.4.3 构建全数据全生命周期的安全保障
  source_quote: |
    "解密只会发生在两个地方：根据输入 Prompt 进行 RAG 片段召回，以及大模型 Prompt 生成回答时。可以遵循最小必要原则对 Prompt 进行解密和使用，并且该过程只在内存中瞬间存在，不做任何的持久化存储"
  summary: |
    提示词与答案全程加密，解密只发生在两处：RAG 片段召回时和大模型生成回答时；只在内存中瞬间存在，
    不做持久化存储。
  tags: [rule, encryption, least-necessary]

- id: p2-150
  title: 仅靠加密+拒答防蒸馏存在先天缺陷
  type: rule
  source_chapter: 14.4.3 构建全数据全生命周期的安全保障
  source_quote: |
    "生成式模型面对相同提示词攻击的拒答能力并不稳定，一定概率会指令遵循失败……用大尺寸模型返回的加密后令牌，去询问同系列小尺寸安全能力弱的模型"
  summary: |
    提示词加密叠加模型拒答不能独立防数据蒸馏：拒答能力不稳定（一定概率指令遵循失败），且存在用大模型返回的
    加密令牌询问同系列小模型绕过拒答的攻击；需要第三方专业安全防护能力。
  tags: [rule, model-security, distillation-attack]

- id: p2-151
  title: 用户大模型数据在自有云账号下独立存储
  type: rule
  source_chapter: 14.4.3 构建全数据全生命周期的安全保障
  source_quote: |
    "用户大模型相关数据需在用户自有的云账号下独立存储使用"
  summary: |
    模型推理、训练、RAG 应用的用户数据支持外接存储部署（OSS/ES/ADB/SLS 等），归属客户实例、100% 自主可控，
    各数据服务租户化安全隔离；账号注销后按隐私政策删除或匿名化，支持删除指定数据及其索引关系和过程文件。
  tags: [rule, data-security, storage-isolation]

- id: p2-152
  title: 关键数据节点引入第三方安全告警
  type: rule
  source_chapter: 14.4.3 构建全数据全生命周期的安全保障
  source_quote: |
    "错误的提示词引导会导致AI错误删除重要数据……人类仍然可能惯性误点……因此在关键数据节点上，建议引入第三方安全告警机制把好最后一道关"
  summary: |
    AI 替代人操作占比攀升，存在权限过大、自主提权、错误提示词引导误删、人类惯性误点确认、攻击者借 AI 脱库等风险；
    关键数据节点（删除等）应引入独立于执行链的第三方安全告警机制作最后防线。
  tags: [rule, data-security, third-party-alert]

- id: p2-153
  title: Agent 身份安全八环节贯通
  type: checklist
  source_chapter: 14.5.1 背景和挑战
  source_quote: |
    "Agent 身份安全因此必须贯通资产发现、身份注册、动态凭据、入站与出站授权、运行时控制、全链路审计、生命周期治理和事件恢复"
  summary: |
    Agent 身份安全八个环节：资产发现、身份注册、动态凭据、入站与出站授权、运行时控制、全链路审计、
    生命周期治理、事件恢复。传统 IAM 不失效但治理对象、授权粒度和决策时机需扩展。
  tags: [checklist, agent-identity, lifecycle]

- id: p2-154
  title: 单应用账号或长期密钥回答不了身份五问
  type: principle
  source_chapter: 14.5.1 背景和挑战
  source_quote: |
    "只为 Agent 配置一个应用账号或长期密钥，无法回答'谁在行动、代表谁行动、凭什么行动、为什么此刻允许、发生异常后如何收权'"
  summary: |
    身份治理自检五问：谁在行动、代表谁行动、凭什么行动、为什么此刻允许、发生异常后如何收权。
    单一应用账号或长期密钥无法回答这五问。配套挑战：资产黑箱、权限逃逸（借 Agent 越岗）、凭据失控、责任链模糊。
  tags: [principle, agent-identity, five-questions]

- id: p2-155
  title: 每个 Agent 发唯一数字工牌
  type: rule
  source_chapter: 14.5.2 Agent身份安全管控闭环
  source_quote: |
    "统一 Agent 身份（Agent Identity）；每个 Agent 都发放唯一的'数字工牌'……实现'用户 → 客户端 → Agent → 访问资源'端到端身份传递，防止身份伪造"
  summary: |
    每个 Agent（无论平台自动创建还是手动创建）发放唯一身份标识，与人员身份、机器身份关联；
    通过 OIDC/OAuth 2.0 对接企业身份源，实现"用户 → 客户端 → Agent → 访问资源"端到端身份传递，防止身份伪造。
  tags: [rule, agent-identity, identity-propagation]

- id: p2-156
  title: Token Vault 动态凭据，杜绝硬编码长期密钥
  type: rule
  source_chapter: 14.5.2 Agent身份安全管控闭环
  source_quote: |
    "API Key、OAuth Secret、LLM Key 等敏感凭据由 KMS 加密后集中托管在 Token Vault。Agent 代码不接触长期明文凭证，运行时才按 Agent ID 和用户授权获取短期令牌"
  summary: |
    所有 API Key/OAuth Secret/LLM Key 由 KMS 加密集中托管于 Token Vault；Agent 代码不接触长期明文凭证，
    运行时按 Agent ID 和用户授权获取短期 Access Token/STS Token，凭据仅在运行时注入并与出站授权一一绑定。
  tags: [rule, credentials, token-vault]

- id: p2-157
  title: Agent 默认无权限且不超过用户权限
  type: rule
  source_chapter: 14.5.2 Agent身份安全管控闭环
  source_quote: |
    "最小权限：Agent 默认无权限，仅在用户授权后以其身份和权限范围访问下游资源，且 Agent 权限不超过用户权限"
  summary: |
    最小权限三要点：默认无权限、仅用户授权后以其身份和权限范围访问下游资源、Agent 权限永不超过用户权限
    （防权限逃逸，如普通销售借报销助手查 CEO 差旅）。
  tags: [rule, least-privilege, agent-identity]

- id: p2-158
  title: 员工异动时 Agent 权限同步变更
  type: rule
  source_chapter: 14.5.2 Agent身份安全管控闭环
  source_quote: |
    "员工入职、转岗、离职时，其创建或授权的 Agent 权限可同步变更……解决'影子 Agent'和权限残留问题"
  summary: |
    Agent 生命周期与人员生命周期绑定：入职/转岗/离职时其创建或授权的 Agent 权限同步变更；
    注册、权限授予、凭据轮换、下线注销在统一控制台管理，解决影子 Agent 与权限残留（离职员工 Agent 继续运行）。
  tags: [rule, agent-identity, lifecycle]

- id: p2-159
  title: 双向授权模型：入站 + 出站
  type: rule
  source_chapter: 14.5.3 身份授权治理
  source_quote: |
    "入站授权（Client → Agent）：控制哪些客户端/用户能调用该 Agent……出站授权（Agent → 下游服务）：控制 Agent 能访问哪些大模型、企业应用、三方 SaaS 或 MCP Server"
  summary: |
    把 Agent 视为兼具"资源服务器"和"客户端"双重身份：入站授权控制谁能调用该 Agent 及可用 scope；
    出站授权控制 Agent 能访问哪些大模型/企业应用/三方 SaaS/MCP Server。新增节点自动加入出站规则、删除节点即撤销。
  tags: [rule, authorization, bidirectional]

- id: p2-160
  title: Token Exchange 权限收敛
  type: rule
  source_chapter: 14.5.3 身份授权治理
  source_quote: |
    "原令牌 audience 是 Agent，新令牌 audience 是下游服务；新令牌仅包含被授权的最小 scope；保留用户信息，又记录 Agent 调用链。这避免了 Agent 成为'超级应用'"
  summary: |
    Agent 调用下游服务时用 Token Exchange 收敛权限：新令牌 audience 换成下游服务、仅含最小授权 scope、
    保留用户信息并记录调用链。即使 Agent 被攻破，泄露的也只是对单一服务的短期受限令牌。
  tags: [rule, token-exchange, privilege-convergence]

- id: p2-161
  title: On-Behalf-Of 需用户明确同意且可即时撤销
  type: rule
  source_chapter: 14.5.3 身份授权治理
  source_quote: |
    "用户首次使用时需明确同意，用户撤销同意后 Agent 立即失去出站调用能力"
  summary: |
    Agent 以用户身份访问下游资源（On-Behalf-Of）：首次使用需用户明确同意；撤销同意后立即失去出站调用能力。
  tags: [rule, consent, on-behalf-of]

- id: p2-162
  title: 上下文感知的动态授权
  type: rule
  source_chapter: 14.5.3 身份授权治理
  source_quote: |
    "可基于用户、Agent、工具上下文，与用户请求中的上下文进行动态授权，如：允许市场部的用户，使用订单Agent，下单金额<1000 的订单"
  summary: |
    授权决策可依赖请求上下文（用户、Agent、工具、请求参数）：如"市场部用户 + 订单 Agent + 金额<1000"才允许下单；
    通过 Cedar 类策略语言 + AI 网关实现，只在对话满足上下文条件时授予权限。
  tags: [rule, abac, dynamic-authorization]

- id: p2-163
  title: 资产盘点从计算资源延伸到 Agent 层
  type: rule
  source_chapter: 14.6.2 基础设施的统一安全态势管理
  source_quote: |
    "资产盘点的对象还需从计算资源延伸到 Agent 层：Agent 实例及其任务会话、接入的 MCP 服务与连接器、技能与工具配置，以及 Agent 持有的各类 NHI 凭据，都应纳入统一的资产清单"
  summary: |
    安全资产清单覆盖：Agent 实例及任务会话、MCP 服务与连接器、技能与工具配置、Agent 持有的 NHI 凭据，
    避免游离于安全管理之外的"影子 Agent"。
  tags: [rule, asset-management, shadow-agent]

- id: p2-164
  title: 态势管理须与运行时管控联动闭环
  type: principle
  source_chapter: 14.6.2 基础设施的统一安全态势管理
  source_quote: |
    "态势管理解决的是'看得见'的问题，发现的风险还必须与运行时的管控手段联动闭环：对存在高危漏洞、异常暴露或行为失真的 Agent 工作负载，应能联动网关、防火墙与任务编排层及时限流、隔离或暂停任务"
  summary: |
    态势管理只解决"看得见"；风险须联动网关、防火墙与任务编排层及时限流、隔离或暂停任务，
    把资产与风险视图转化为实际处置能力。
  tags: [principle, posture-management, closed-loop]

- id: p2-165
  title: 管理端口仅对堡垒机 IP 开放
  type: rule
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "遵循最小权限原则，关闭非必要端口，限制 SSH/RDP 等管理端口仅对运维堡垒机IP开放，避免公网暴露"
  summary: |
    主机安全组最小权限：关闭非必要端口；SSH/RDP 等管理端口仅对运维堡垒机 IP 开放，避免公网暴露。
  tags: [rule, host-security, network]

- id: p2-166
  title: 镜像先扫描签名，集群只部署有效签名镜像
  type: rule
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "开发人员对合规镜像签名，ACK 集群仅允许携带有效签名的镜像部署，防止供应链投毒"
  summary: |
    镜像供应链安全：CI/CD 自动扫描（系统/应用漏洞、恶意样本、硬编码密钥）→ 合规镜像签名 →
    集群仅允许携带有效签名的镜像部署。安全左移至开发阶段（扫描）+ 运行阶段（沙箱隔离）闭环。
  tags: [rule, supply-chain, image-security]

- id: p2-167
  title: Agent 会话级隔离，环境用完即弃
  type: rule
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "为每个 Agent 会话分配独立的沙箱或轻量级虚拟机（microVM），实现内核级隔离、环境用完即弃，并在空闲超时或达到最大生命周期后自动回收，避免残留环境与凭据被后续任务或攻击者复用"
  summary: |
    2026 年以来隔离单位从服务收敛到会话：每个 Agent 会话独立沙箱或 microVM，内核级隔离、用完即弃，
    空闲超时或达最大生命周期自动回收，防止残留环境与凭据被复用。Agent 运行环境本身当作不可信负载对待。
  tags: [rule, sandbox, session-isolation]

- id: p2-168
  title: 按会话注入最小化临时凭据
  type: rule
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "并按会话注入最小化的临时凭据，替代长期有效的环境变量密钥"
  summary: |
    沙箱内凭据按会话注入最小化临时凭据，替代长期有效的环境变量密钥。
  tags: [rule, credentials, sandbox]

- id: p2-169
  title: 多 Agent 相互隔离防横向波及
  type: rule
  source_chapter: 14.6.3 计算层安全加固
  source_quote: |
    "Agent 之间也应遵循相互隔离的原则，按任务边界划分运行环境与网络策略，防止单个 Agent 被攻陷后横向波及其他 Agent 及其可访问的数据与工具"
  summary: |
    多 Agent 协作场景按任务边界划分运行环境与网络策略、相互隔离，防止单个 Agent 被攻陷后横向波及。
  tags: [rule, multi-agent, isolation]

- id: p2-170
  title: Agent 出站流量默认拒绝、按需放行
  type: rule
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "出站方向应默认拒绝、按需放行：Agent 运行时所在的 VPC 或子网默认禁止出站，仅按域名、目的地显式放行业务必需的端点"
  summary: |
    Agent 出站默认拒绝：VPC/子网默认禁止出站，仅按域名、目的地显式放行业务必需端点（模型 API、内部知识库）。
    背景是 HF 入侵事件中攻击 Agent 借可访问的中间服务代联网突破网络边界。
  tags: [rule, egress, default-deny]

- id: p2-171
  title: 网络层封禁云元数据端点与内网保留地址段
  type: rule
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "在网络层封禁云元数据服务端点与内网保留地址段，防止 SSRF 与提示注入演化为内网探测和凭据窃取"
  summary: |
    网络层封禁云元数据服务端点（如 169.254.169.254 类）与内网保留地址段，防止 SSRF 与提示注入演化为内网探测和凭据窃取。
  tags: [rule, ssrf, metadata-endpoint]

- id: p2-172
  title: Agent 出站访问统一关口收敛
  type: rule
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "Agent 对互联网及 MCP 服务的访问经由 AI 网关或云防火墙的出站管控集中收敛，校验访问目标、记录访问日志，并与 NDR 联动发现异常外联与数据外发"
  summary: |
    Agent 对互联网及 MCP 服务的访问经 AI 网关或云防火墙出站管控集中收敛：校验访问目标、记录日志、
    与 NDR 联动发现异常外联与数据外发。
  tags: [rule, egress, unified-gateway]

- id: p2-173
  title: 点-线-面立体网络防御
  type: framework-rule
  source_chapter: 14.6.4 网络隔离与访问控制
  source_quote: |
    "通过安全组实现节点级防护（点），VPC 边界防火墙拦截跨区流量威胁（线），互联网防火墙把控公网入口并约束 Agent 出站（面）"
  summary: |
    网络防御三层组合：安全组=节点级防护（点）、VPC 边界防火墙=跨区流量威胁拦截（线）、
    互联网防火墙=公网入口 + Agent 出站约束（面）。内网同时实践零信任：所有内部流量经身份认证与策略验证。
  tags: [rule, network, defense-in-depth]

# ============ 17.1 Agent 优化方法总述 ============

- id: p2-174
  title: 失败归因基本原则：先查信息与接口，再定模型
  type: principle
  source_chapter: 17.1.1 判断优化对象
  source_quote: |
    "若必要信息缺失、工具协议含混、状态不可见或反馈被截断，应优先修复 Harness；……只有在信息、动作空间和反馈条件均明确且稳定时，同类决策错误仍跨任务变体反复出现，才有充分理由将其列为模型能力优化目标"
  summary: |
    归因判据：模型作出错误决策时是否已获得准确充分的信息、清晰可用的动作接口、正常的执行环境。
    信息缺失/协议含混/状态不可见/反馈截断 → 修 Harness；工具不可用/资源不足/外部异常 → 修执行环境；
    只有信息、动作空间和反馈均明确稳定且同类错误跨任务变体反复出现，才列为模型优化目标。
  tags: [principle, attribution, optimization]

- id: p2-175
  title: 归因次序：执行环境 → Harness → 模型
  type: principle
  source_chapter: 17.1.1 判断优化对象
  source_quote: |
    "实际排查时，应先排除能够独立复现的执行环境故障，再检查 Harness 是否提供了充分的信息和可靠的运行控制，最后判断是否属于模型能力缺口……为了避免用模型训练弥补本应由系统工程解决的问题"
  summary: |
    归因固定次序：先排除可独立复现的执行环境故障 → 再检查 Harness 是否提供充分信息与可靠运行控制 →
    最后判断模型能力缺口。目的是避免用模型训练弥补本应由系统工程解决的问题（代价最高的误判）。
  tags: [principle, attribution, debugging-order]

- id: p2-176
  title: 三层归因的验证方法
  type: checklist
  source_chapter: 17.1.1 判断优化对象（表 17.1-1）
  source_quote: |
    "执行环境：绕过模型直接调用相应工具或服务；若故障仍能复现，则归因于执行环境……Harness：固定模型和执行环境，修正上下文、工具协议或运行控制……模型：固定 Harness 和执行环境进行重复测试或替换模型"
  summary: |
    验证方法对照：执行环境=绕过模型直接调用工具/服务，故障仍复现则归因环境；Harness=固定模型与环境，
    修正上下文/工具协议/运行控制，问题明显减少则归因 Harness；模型=固定 Harness 与环境重复测试或替换模型，
    错误跨任务变体稳定出现或随模型变化显著改善则归因模型。
  tags: [checklist, attribution, verification]

- id: p2-177
  title: 失败现象本身不能直接决定归因
  type: principle
  source_chapter: 17.1.1 判断优化对象
  source_quote: |
    "失败现象本身不能直接决定归因。例如，工具选错既可能源于模型能力不足，也可能源于工具描述含混"
  summary: |
    相同失败现象可由不同层引起（工具选错可能源于模型能力不足，也可能源于工具描述含混），
    必须将现象与判定条件结合验证后再定优化对象。
  tags: [principle, attribution, phenomenon-vs-cause]

- id: p2-178
  title: 模型与 Harness 边界不清时用四组对照
  type: rule
  source_chapter: 17.1.1 判断优化对象
  source_quote: |
    "可以在执行环境稳定的前提下组织四组对照：原模型与原 Harness、原模型与候选 Harness、候选模型与原 Harness、候选模型与候选 Harness"
  summary: |
    执行环境稳定时组织 2×2 四组对照：原模型×原 Harness、原模型×候选 Harness、候选模型×原 Harness、
    候选模型×候选 Harness，在一致任务集和预算下分离模型改动、Harness 改动及交互收益；
    候选模型需要的专门提示或协议适配应纳入对应配置记录，不把组合变化全归因于模型。
  tags: [rule, attribution, ablation]

- id: p2-179
  title: 模型优化可由成本目标驱动
  type: rule
  source_chapter: 17.1.1 判断优化对象
  source_quote: |
    "如果任务分布相对稳定、有效行为可以学习，就可以评估通过 SFT 或蒸馏减少这些开销"
  summary: |
    高频任务已靠长提示、反复纠错、多模型复核完成但开销超预算时，若任务分布稳定且有效行为可学习，
    可评估 SFT/蒸馏减少执行开销；需同时调整模型和 Harness，并保留权限控制、执行约束和独立验证。
  tags: [rule, cost-driven, optimization]

- id: p2-180
  title: 模型决策能力四分类
  type: checklist
  source_chapter: 17.1.2 Agent 模型能力的要求
  source_quote: |
    "任务理解与约束识别……工具使用与反馈理解……多步决策与状态利用……失败恢复与合理终止"
  summary: |
    模型决策能力四类：任务理解与约束识别（自然语言→可执行目标、完成标准、前置条件、操作范围，信息不足时
    提出澄清也是有效推进）；工具使用与反馈理解（调用成功≠结果可用，需判断完整性/口径/需否核验）；
    多步决策与状态利用（按反馈调整行动顺序、确认用户操作）；失败恢复与合理终止（修参数/补信息/换路径/求助；
    满足完成条件后判断可否终止）。
  tags: [checklist, model-capability, taxonomy]

- id: p2-181
  title: 单位成功任务成本公式
  type: calculation
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "可采用'单位成功任务成本'，即统计期内全部任务的执行成本除以成功完成的任务数量，失败任务消耗的资源也计入分子。比较时应固定任务构成并同时报告成功率"
  summary: |
    单位成功任务成本 = 统计期内全部任务的执行成本 ÷ 成功完成的任务数量（失败任务消耗也计入分子）。
    成本包含模型调用、工具执行、失败重试和人工介入，同时观察端到端时延及高分位。比较时固定任务构成并
    同时报告成功率，避免因放弃困难任务形成表面成本下降。
  tags: [calculation, cost, success-metric]
  inputs: [统计期内全部任务执行成本（含失败任务消耗）, 成功完成的任务数量]
  formula: "单位成功任务成本 = 全部任务执行成本 ÷ 成功任务数"
  units: 货币单位/成功任务
  output: 可比的成本效率指标
  missing_conditions: 比较须固定任务构成并同时报告成功率

- id: p2-182
  title: 最终验收回到完整任务
  type: rule
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "工具调用格式正确率等局部指标可以辅助定位问题，最终验收仍应回到完整任务"
  summary: |
    质量衡量除最终答案外检查关键动作是否正确、结果是否满足业务口径、约束是否遵守；
    局部指标（工具调用格式正确率）只辅助定位，最终验收回到完整任务。
  tags: [rule, evaluation, end-to-end]

- id: p2-183
  title: 可靠性评估：多次运行且关键场景不回退
  type: rule
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "同一任务应进行多次运行，并覆盖输入变化、长交互、工具异常和用户补充条件等情形。平均成功率提高时，仍需确认关键业务场景没有回退"
  summary: |
    可靠性评估：同一任务多次运行，覆盖输入变化、长交互、工具异常、用户补充条件；平均成功率提高时仍需确认
    关键业务场景没有回退；权限或执行边界被违反的情况单独报告。
  tags: [rule, reliability, evaluation]

- id: p2-184
  title: SFT 降成本的条件与任务频率判据
  type: rule
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "对于低频或变化较快的任务，轻量的 Harness 调整可能更经济；对于高频且相对稳定的任务，模型训练或蒸馏带来的单次执行节省才更可能形成累计收益"
  summary: |
    SFT 只有减少提示长度、降低重试与复核次数、或使更小模型胜任目标任务时才可能降低端到端成本；
    否则数据准备、训练、评测、发布、维护的一次性投入可能抵消运行节省。低频/变化快任务用轻量 Harness 调整，
    高频/稳定任务模型训练或蒸馏才可能形成累计收益。
  tags: [rule, cost, sft]

- id: p2-185
  title: 安全护栏三层分工：模型识别、Harness 控制、环境强制
  type: rule
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "模型负责识别约束并提出动作，但不能仅依赖模型自行遵守：Harness 负责调用侧的权限校验、动作放行、重试上限、停止条件和独立验证，执行环境则负责资源与服务侧的强制校验和动作执行"
  summary: |
    安全护栏三层：模型识别约束提出动作（不依赖其自行遵守）；Harness 负责调用侧权限校验、动作放行、重试上限、
    停止条件、独立验证；执行环境负责资源与服务侧强制校验和动作执行。
  tags: [rule, safety, layered-guardrails]

- id: p2-186
  title: 训练前定七要素，训练后同口径复测
  type: checklist
  source_chapter: 17.1.3 设定优化目标
  source_quote: |
    "训练前应明确目标任务、对照基线、质量门槛、可靠性要求、成本预算、安全约束和回归范围；训练后采用同一口径复测"
  summary: |
    训练前明确七要素：目标任务、对照基线、质量门槛、可靠性要求、成本预算、安全约束、回归范围；
    训练后同一口径复测，才能判断收益来自预期能力提升，且没有以可靠性或安全性换取局部指标改善。
  tags: [checklist, training, acceptance]

- id: p2-187
  title: 模型优化方法三分支选择
  type: rule
  source_chapter: 17.1.4 选择合适的模型优化方法
  source_quote: |
    "有明确示范时选择 SFT……需要交互探索且环境能够提供可靠反馈时选择 Agentic RL……教师模型已具备能力，且目标是迁移、压缩或部署时选择模型蒸馏"
  summary: |
    方法选择：有明确有效示范而执行不稳 → SFT；存在多种执行路径、优劣需实际执行判断且环境能提供可验证反馈
    （环境可运行、反馈与真实目标一致、成本可承担）→ Agentic RL；教师模型已具备能力、目标是迁移/压缩/部署
    → 蒸馏（学生可能进入教师未覆盖状态，需检验偏差累积和失败恢复）。
  tags: [rule, model-optimization, method-selection]

- id: p2-188
  title: 三类优化方法不互斥
  type: principle
  source_chapter: 17.1.4 选择合适的模型优化方法
  source_quote: |
    "三类方法并不互斥……蒸馏通常通过教师生成数据上的 SFT 实现；SFT 后的模型也可以继续开展 Agentic RL"
  summary: |
    SFT、Agentic RL、蒸馏可组合排序（蒸馏通常经教师数据上的 SFT 实现，SFT 后可继续 RL）；
    组合与排序由基础模型能力、训练信号质量、目标 Harness 和成本预算共同决定，训练阶段应覆盖生产主要上下文
    组织、工具协议和反馈方式，或验证训练与生产差异可被可靠适配。
  tags: [principle, model-optimization, combination]

# ============ 第 24 章 边缘 ============

- id: p2-189
  title: Agent 端到端延迟七因素拆解
  type: calculation
  source_chapter: 24.1 全球化场景的边缘优化维度
  source_quote: |
    "Agent 端到端延迟可拆解为七个因素：排队、环境准备、模型推理、工具调用、网络传输、沙箱执行和人工等待。其中六个因素在 Region 内产生，只有网络传输由物理距离决定"
  summary: |
    延迟七因素：排队、环境准备、模型推理、工具调用、网络传输、沙箱执行、人工等待。六个在 Region 内产生，
    只有网络传输由物理距离决定——模型推理再快也无法补偿跨洋网络瓶颈（示例：网络传输在 TTFT 中占比可超 40%），
    边缘优化精准命中网络传输因素。
  tags: [calculation, latency, edge]
  inputs: [排队, 环境准备, 模型推理, 工具调用, 网络传输, 沙箱执行, 人工等待]
  formula: "端到端延迟 = 排队 + 环境准备 + 模型推理 + 工具调用 + 网络传输 + 沙箱执行 + 人工等待（仅网络传输由物理距离决定）"
  units: 时间
  output: 延迟归因与优化切入点选择
  missing_conditions: 占比数字（如 40%）为示例假设，需企业基线校准

- id: p2-190
  title: 边缘/Region 分工原则：边缘能处理的不回源
  type: principle
  source_chapter: 24.1 全球化场景的边缘优化维度
  source_quote: |
    "边缘能处理的（缓存命中、安全拦截、就近路由），就不回源 Region；边缘处理不了的（复杂推理、状态变更、长任务执行），才进入 Region"
  summary: |
    边缘与 Region 分层协作：边缘负责缓存命中、安全拦截、就近路由、重复推理缓存、回源带宽、边缘内容转换；
    Region 负责正确性判定、模型/工具延迟优化、行为安全、版本管理、任务完成度。
  tags: [principle, edge, division-of-labor]

- id: p2-191
  title: 请求可就地边缘完成的三条件
  type: rule
  source_chapter: 24.1 全球化场景的边缘优化维度
  source_quote: |
    "只有无需权威状态判定、无需写副作用、且符合数据驻留要求的请求才适合在边缘就地完成；涉及一致性、顺序和恢复语义的操作必须回到 Region 执行"
  summary: |
    边缘就地完成判据三条件：无需权威状态判定、无需写副作用、符合数据驻留要求。
    涉及一致性、顺序和恢复语义的操作必须回 Region；判断"能处理"不只看计算能力，还要看事实权威、身份权限、
    写副作用、数据驻留与故障恢复语义。
  tags: [rule, edge, eligibility]

- id: p2-192
  title: 边缘评估四维度指标
  type: checklist
  source_chapter: 24.2 边缘评估
  source_quote: |
    "性能维度关注全球各区域的 TTFT（P50/P95/P99）……成本维度追踪端到端调用成本（Token + 带宽 + 边缘请求费用）……安全维度衡量边缘拦截率……体验维度通过区域体验一致性评分"
  summary: |
    边缘评估四维度：性能（全球 TTFT P50/P95/P99、边缘到 Region 传输延迟、长连接建立时间）；
    成本（端到端调用成本=Token+带宽+边缘请求费用、语义缓存节省量、回源流量占比）；安全（边缘拦截率、DDoS 吸收量、
    Prompt Injection 检出率、Bot 识别准确率）；体验（区域体验一致性评分、流式输出完整性、断连恢复成功率）。
  tags: [checklist, edge, evaluation]

- id: p2-193
  title: Release 准入基线必须同时含边缘指标
  type: rule
  source_chapter: 24.2 边缘评估 / 24.7 边缘生产验证
  source_quote: |
    "Agent Release 的准入基线应同时包含 Region 内指标和边缘指标——一个版本如果在全球 P95 延迟上未达标，即使 Region 内评估全部通过，也不应被发布"
  summary: |
    发布准入基线 = Region 内指标（回答正确率、任务完成率）+ 边缘指标（全球延迟分布、缓存命中率、安全拦截率）；
    全球 P95 延迟或边缘缓存命中率未达标，即使 Region 内仿真全部通过也不发布。边缘指标数据回流与 Region 内
    评估数据合并形成"全球交付质量报告"。
  tags: [rule, release-gate, edge]

- id: p2-194
  title: 语义缓存限定公开幂等低风险请求
  type: rule
  source_chapter: 24.3 边缘性能与成本优化
  source_quote: |
    "语义缓存应限定在公开、幂等、低风险请求范围内，并把租户、权限域、地区、模型、Prompt 和知识版本纳入缓存键，定义 TTL、失效、来源标记和回源策略"
  summary: |
    语义缓存三约束：适用范围（公开、幂等、低风险请求）；缓存键（租户、权限域、地区、模型、Prompt、知识版本）；
    策略（TTL、失效、来源标记、回源策略），避免跨租户泄露、越权、版本陈旧和个性化答案误命中。
  tags: [rule, semantic-cache, edge]

- id: p2-195
  title: 区域性失败归因：多区域失败查 Skill，单区域失败查路由
  type: rule
  source_chapter: 24.4 边缘数据驱动持续优化
  source_quote: |
    "某个工具在多个区域都失败，问题可能出在 Skill 本身；如果只在一个区域失败，更可能是网络或服务路由问题。前者需要修改 Skill，后者则应调整运行编排层（Harness）中的路由策略"
  summary: |
    边缘数据归因规则：工具在多个区域失败 → Skill 本身问题（修改 Skill）；只在单一区域失败 → 网络或服务路由
    问题（调整 Harness 路由策略）。用区域对比避免"看到失败就修改 Prompt"的错误归因。
  tags: [rule, attribution, edge]

- id: p2-196
  title: 全球统一基线 + 区域自适应策略
  type: principle
  source_chapter: 24.4 边缘数据驱动持续优化
  source_quote: |
    "Prompt、核心 Skill 和基础安全规则由全球统一治理，保证 Agent 的基本能力和行为一致；模型路由、服务节点、缓存、安全规则和工具调用策略，则可以根据各区域的真实流量进行调整"
  summary: |
    全球化配置策略：能力与安全基线（Prompt、核心 Skill、基础安全规则）全球统一治理保证行为一致；
    运行参数（模型路由、服务节点、缓存、安全规则、工具调用策略）按区域真实流量自适应，避免统一配置在所有区域
    都次优，也避免各区域独立演进造成能力割裂。
  tags: [principle, edge, global-strategy]

- id: p2-197
  title: 版本钉扎与激活协议防边缘版本漂移
  type: rule
  source_chapter: 24.5 边缘内容分发
  source_quote: |
    "如果一个边缘节点运行的是 Skill v1.2 而 Region 内已经是 v1.3，就会产生行为不一致和评估偏差……通过版本钉扎和激活协议明确各执行点当前生效的版本，避免并发写和断网降级下的版本漂移"
  summary: |
    能力资产版本须在所有边缘执行点保持一致：每次发布触发边缘缓存刷新，用版本钉扎和激活协议明确各执行点生效版本，
    防止并发写和断网降级下的版本漂移导致行为不一致与评估偏差。
  tags: [rule, versioning, edge]

- id: p2-198
  title: 两种一致性：治理规则全球一致，数据分布按驻留差异
  type: rule
  source_chapter: 24.5 边缘内容分发
  source_quote: |
    "治理规则全球一致（版本管理、变更评审、发布流程统一），数据分布按驻留要求差异化（分类分级、脱敏、授权、驻留、保留和删除机制按区域合规执行）"
  summary: |
    区域适配区分两种一致性：治理规则（版本管理、变更评审、发布流程）全球统一；数据分布（分类分级、脱敏、授权、
    驻留、保留、删除）按区域合规差异化执行，支持按区域配置 Context 变体。
  tags: [rule, edge, data-residency]

- id: p2-199
  title: 边缘三层安全过滤
  type: checklist
  source_chapter: 24.6 边缘安全
  source_quote: |
    "网络层：DDoS 流量在边缘吸收清洗……应用层：WAF/Bot 管理识别传统 Web 攻击……语义层：AI 护栏检测 Prompt Injection、越狱尝试、不合规内容、敏感数据泄露"
  summary: |
    边缘三层过滤：网络层（DDoS 吸收清洗，只让合法流量回源）、应用层（WAF/Bot 管理：SQL 注入、XSS、恶意爬虫）、
    语义层（AI 护栏：Prompt Injection、越狱、不合规内容、敏感数据泄露）。AI 护栏与 WAF 职责不同：
    WAF 管已知签名规则攻击，AI 护栏管模型交互层攻击，协同运行。
  tags: [checklist, edge, security-layers]

- id: p2-200
  title: 边缘负责过滤，Region 负责判断
  type: principle
  source_chapter: 24.6 边缘安全
  source_quote: |
    "边缘安全负责'过滤'（拦截已知威胁、吸收攻击流量），Region 内安全负责'判断'（复杂的权限校验、行为分析、审计追踪）"
  summary: |
    边缘/Region 安全分层：边缘过滤已知威胁、吸收攻击流量；Region 做复杂权限校验、行为分析、审计追踪。
    两方向事件共享联动：边缘拦截的攻击模式同步到 Region 策略引擎，Region 新威胁规则下发边缘节点。
  tags: [principle, edge, security-division]

- id: p2-201
  title: 边缘只能覆盖直接注入
  type: rule
  source_chapter: 24.6 边缘安全
  source_quote: |
    "边缘只能覆盖直接注入，检索内容、工具返回等间接注入仍需 Region 结合完整上下文执行检测与审计"
  summary: |
    边缘 AI 护栏只完成直接注入的前置识别与初筛；检索内容、工具返回等间接注入需 Region 结合完整上下文检测与审计。
  tags: [rule, prompt-injection, edge]

- id: p2-202
  title: 仿真与边缘生产验证互补闭环
  type: principle
  source_chapter: 24.7 边缘生产验证
  source_quote: |
    "仿真负责上线前的合成测试，边缘生产验证负责上线后的真实观测……形成'仿真 → 上线 → 边缘验证 → 回流仿真'的闭环"
  summary: |
    仿真（上线前：合成场景、边界长尾、模拟网络、红队攻击样本）与边缘生产验证（上线后：真实用户请求、主流场景、
    实际网络状况、真实攻击流量）互补；边缘观测到的真实参数（如某区域 5% 请求超 5 秒延迟）回流补充仿真场景。
  tags: [principle, simulation, edge-verification]

- id: p2-203
  title: 边缘灰度发布三环境
  type: rule
  source_chapter: 24.7 边缘生产验证
  source_quote: |
    "支持开发、灰度、生产三套环境。每个版本基于现有配置克隆生成，可以先在开发环境测试，再提升到灰度环境按请求规则导入部分流量验证，最终提升到生产环境全量生效"
  summary: |
    边缘版本管理：开发 → 灰度（按请求规则导入部分真实流量，持续观察任务成功率、时延、缓存命中率、安全拦截率）
    → 生产全量；版本支持切换和回滚，异常时快速回退。真实流量既是验证对象也是发布标准依据。
  tags: [rule, canary-release, edge]

- id: p2-204
  title: 边缘优化三阶段演进
  type: framework-rule
  source_chapter: 24.8 三阶段演进与企业成熟度模型
  source_quote: |
    "阶段一：边缘基础设施增强 Agent……阶段二：Agent 能力部署到边缘……阶段三：Agent 原生部署在边缘"
  summary: |
    三阶段：阶段一（边缘基础设施增强——业务代码不变，只加边缘加速与安全层：Anycast、智能路由、DDoS、WAF、
    静态缓存、SSL 卸载）；阶段二（Agent 能力部署到边缘——语义缓存、内容转换、轻量推理）；阶段三（Agent 原生
    部署在边缘——边缘优先、中心兜底，两级运行时 + 边缘 AI 加速网关）。
  tags: [rule, edge, maturity-stages]

- id: p2-205
  title: 边缘阶段判断信号表
  type: checklist
  source_chapter: 24.8 三阶段演进 / 24.9 企业决策
  source_quote: |
    "全球用户抱怨延迟高，但 Region 内指标正常→需要阶段一；Token 成本随用户增长线性上升，无明显缓存效果→需要阶段二；需要为每个区域独立部署完整 Agent 系统，运维成本不可控→需要阶段三"
  summary: |
    阶段判断信号：全球用户抱怨延迟高但 Region 内指标正常 → 阶段一；Token 成本随用户增长线性上升且无缓存效果
    → 阶段二；需每区域独立部署完整 Agent 系统且运维成本不可控 → 阶段三；只在单一 Region 运行且用户同区域
    → 暂不需要边缘优化。阶段三补充信号：全球 TTFT 中网络传输占比超 30%（示例）。
  tags: [checklist, edge, decision-signals]

- id: p2-206
  title: 两级运行时分工：EdgeFunction 与 RegionFunction
  type: rule
  source_chapter: 24.8 三阶段演进
  source_quote: |
    "EdgeFunction……处理对延迟敏感的前置逻辑——鉴权校验、路由决策、请求改写、缓存查询、安全过滤……RegionFunction……处理需要持久状态和深度计算的 Agent 逻辑——多轮对话上下文管理、工具调用编排、模型推理调度、长期记忆读写、沙箱隔离执行"
  summary: |
    两级运行时：EdgeFunction（近用户侧：鉴权校验、路由决策、请求改写、缓存查询、安全过滤）+
    RegionFunction（近源侧：多轮上下文、工具编排、模型推理调度、长期记忆读写、沙箱执行）。
    核心思路：不是所有 Agent 请求都需回数据中心；边缘函数轻量执行实例 + 云函数完整逻辑单例，
    通过状态外置（权威源、版本钉扎、激活协议、并发写控制、断网降级、回滚规则）保持行为一致。
  tags: [rule, edge, two-tier-runtime]

- id: p2-207
  title: 两级运行时关键调优指标
  type: calculation
  source_chapter: 24.8 三阶段演进
  source_quote: |
    "边缘处理比例……按任务风险与业务 SLO 设置目标，正确性优先，而非单一最大化……RegionFunction 会话保持率……不可中断"
  summary: |
    关键指标口径：边缘处理比例（EdgeFunction 完成请求占比——按任务风险与业务 SLO 设目标，正确性优先而非单一
    最大化）、EdgeFunction 冷启动（示例 <1ms）、路由决策延迟（示例 <5ms）、RegionFunction 会话保持率
    （不可中断）、边缘-Region 故障转移时间（示例 <3s）。均为架构示例目标，需企业基线校准。
  tags: [calculation, edge, sli]
  inputs: [EdgeFunction 完成请求数, 总请求数, 冷启动时间, 路由决策延迟, 会话保持率, 故障转移时间]
  formula: "边缘处理比例 = EdgeFunction 完成请求 ÷ 总请求；其余指标为时延/比率类 SLI"
  output: 边缘优先架构的调优目标与验收
  missing_conditions: 所有阈值为示例目标，须按企业基线和压测校准

- id: p2-208
  title: 边缘优化成熟度模型 L1-L4
  type: framework-rule
  source_chapter: 24.8 三阶段演进与企业成熟度模型
  source_quote: |
    "L1 无边缘优化……L2 接入边缘加速与安全，独立看板……L3 边缘指标纳入 Agent Release 准入基线……L4 边缘数据驱动自进化"
  summary: |
    成熟度四级：L1 无边缘优化直连 Region；L2 接入边缘加速与安全但未进评估闭环（延迟改善无法量化 ROI、安全规则
    手动维护）；L3 边缘指标纳入 Release 准入基线（版本发布过边缘性能回归、缓存策略与版本绑定）；L4 边缘数据驱动
    自进化（缓存/路由/安全策略按边缘 Trace 自动优化、灰度验证后生效）。与部署成熟度 M1-M5 大致呼应但不一一对应，
    每级能力应有可验证门槛。
  tags: [rule, edge, maturity-model]

- id: p2-209
  title: 成熟度不是越高越好
  type: principle
  source_chapter: 24.8 三阶段演进与企业成熟度模型
  source_quote: |
    "成熟度不是越高越好。L1 适合仅在单一区域运营的企业；L2 适合全球化初期；L3 适合 Agent 进入生产后需要严格质量门禁的阶段；L4 适合大规模全球化运营"
  summary: |
    边缘优化与"最低充分架构"原则一致，不追求最高阶段：L1 适合单区域运营、L2 全球化初期、L3 严格质量门禁、
    L4 大规模全球化；大多数企业长期停留在阶段一或阶段二即可获得显著收益。
  tags: [principle, edge, sufficient-architecture]

- id: p2-210
  title: 边缘优化不适用场景清单
  type: checklist
  source_chapter: 24.9 能力状态、边界与企业决策
  source_quote: |
    "单区域业务……严格数据驻留要求……高正确性风险的回答……边缘算力受限……区域能力差异……成本反转"
  summary: |
    六类慎用场景：单区域业务（延迟收益有限投入不成比例）；严格数据驻留（不允许跨区域缓存复制，需按区域合规配置
    或放弃边缘缓存）；高正确性风险回答（个性化强、权限敏感、时效性高不进语义缓存）；边缘算力受限（复杂推理、长任务、
    大上下文仍需回源）；区域能力差异（全球统一策略需按区域适配）；成本反转（流量小或命中率低时边缘额外成本超收益）。
  tags: [checklist, edge, boundary]

- id: p2-211
  title: 个性化、权限敏感、时效性高的内容不进语义缓存
  type: rule
  source_chapter: 24.9 能力状态、边界与企业决策
  source_quote: |
    "个性化强、权限敏感、时效性高的内容不应进入语义缓存，否则可能造成错误答案误命中"
  summary: |
    语义缓存排除三类内容：个性化强、权限敏感、时效性高——否则可能造成错误答案误命中。
  tags: [rule, semantic-cache, risk]

- id: p2-212
  title: 成本反转时"不优化"是正确选择
  type: principle
  source_chapter: 24.9 能力状态、边界与企业决策
  source_quote: |
    "流量规模小或缓存命中率低时，边缘层的额外成本可能超过收益，此时阶段一甚至'不优化'才是正确选择"
  summary: |
    边缘优化存在成本反转点：流量规模小或缓存命中率低时，边缘层额外成本（边缘请求费、维护）可能超过收益，
    此时选择低阶段甚至不优化才是正确决策。
  tags: [principle, edge, cost-benefit]

- id: p2-213
  title: 示例性能数字必须经企业基线校准
  type: rule
  source_chapter: 24.9 能力状态、边界与企业决策
  source_quote: |
    "本章出现的所有性能数字均为示例场景假设或架构示例目标，正式实施前必须由企业基线和压测校准"
  summary: |
    书中边缘性能数字（TTFT 占比 40%、冷启动 <1ms、故障转移 <3s、缓存命中延迟 <50ms 等）均为示例场景假设或
    架构示例目标，正式实施前必须由企业基线和压测校准，不可直接当作产品现状指标。
  tags: [rule, edge, calibration]
```
