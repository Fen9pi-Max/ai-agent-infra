# 状态分层（Call/Session/Task 三分 + Event/Snapshot/Checkpoint + Memory 写入六问 + 信息五分法）

## R（原文摘引）
> 将 Task ID 绑定为消息线程 ID，会限制后台执行、多人协作和跨渠道续接。（第 5 章 5.3）
> 三者不能互相替代。只有 Event Log，恢复成本会随任务长度增长；只有 Snapshot，无法解释状态如何形成。（第 5 章 5.3）
> "每次任务结束自动总结并写入 Memory"很容易造成污染。（第 5 章 5.4）
> 企业 Knowledge 是由组织维护、具有来源和时效的业务事实……Memory 是 Agent 从任务和用户交互中选择性积累的经验或个体信息。（第 5 章 5.4）
> 任何检索结果在进入模型前都必须完成租户和用户权限过滤，权限不能仅靠向量库中的自然语言标签推断。（第 5 章 5.4）

## I（解释）
1. **三生命周期边界**：Call（一次请求/恢复，秒到分钟：请求 ID/身份/凭证/游标）、Session（用户与 Agent 连续交互，分钟到数天：参与者/Channel/消息/偏好）、Task（围绕可验收目标的执行对象，可跨 Call/Session/进程/节点：目标/状态/计划/预算/Artifact/证据）。一个 Session 可发起多个 Task，长 Task 可在多个 Session 查看——分别保留并显式记录关联，不绑死。
2. **三表示互补 + 逻辑接口**：Event Log（发生过什么，因果/审计/重建）、Snapshot（某时刻聚合视图，快速读取）、Checkpoint（可安全恢复位置=Snapshot+Continuation+幂等+环境依赖）。面向逻辑状态接口编程（append_event/load_task_state/commit_task_patch/save_snapshot/put_artifact…）而非绑定本地内存或某数据库，本地与分布式实现才可移植。
3. **Workspace 与 Artifact**：Workspace 是外部工作记忆，分区约定 inputs/scratch/state/artifacts/evidence/manifest——服务当前任务的显式工作过程，用户可直接查看编辑，与 Memory（跨任务选择性经验）不同库不同策略。Artifact 有自己的状态机（Draft→Validating→Ready→Published/Rejected→Archived/Deleted）：写出文件不等于完成，进 Ready 须过格式/测试/业务验收，进 Published 还需审批。
4. **Memory 四分类 + 写入六问**：Working/Episodic/Semantic/Procedural；写入前过六问（未来价值？环境验证还是模型推测？属于谁/保留周期？敏感数据？新增/合并/冲突？谁可纠正派生如何清理？）。冲突不静默覆盖，遗忘分层（权重衰减 vs 彻底删除）。Knowledge 与 Memory 分库：来源/权威责任/更新方式/风险全不同——混存同时失去两类治理能力。
5. **信息去向五分法**：当前补丁/待审批→Task State；可复用约束发现→Memory 候选（带来源与验证时间）；企业制度→Knowledge（制度所有者维护，Agent 不改写）；已验证方法→Skill 候选；未验证的模型推测→不写入。实时经营数据查权威工具而非离线索引；权限按结构化模型在进模型前过滤。

## A1（原书案例 + 合成演练）
- 原书·实践案例：MiniMax 全系产品记忆底座部署 PolarDB，千亿级对话表读写性能提升超 3 倍、存储成本降 75%，支撑日均数亿消息实时写入与毫秒级检索——Memory 层独立存储与规模化量级参照（c-34）；B 站"大模型+小模型"协同、PolarDB for AI 数据不出库——Knowledge/数据边界与算力分离的架构参照（c-36）。
- 原书·调研数据：90% 企业有上下文与记忆管理明确需求，检索不准（54%）与遗忘机制缺失（48%）居前（c-04/c-10）。
- 合成演练（纸面推演，非原书案例）：客服 Agent 数据设计——一次来电=1 Session，可发起退款与补发两个 Task（Task 不绑消息线程 ID），三表+显式关联；Workspace 分区（inputs/客户来件与订单快照，scratch/临时比对，state/Plan-Todo-Continuation，artifacts/回复草稿与退款单，evidence/用户确认记录，manifest）；退款单流转 Draft→Validating→Ready 须过业务校验，Published 须审批；信息五分法路由——客户偏好"周五下午勿扰"过六问后入 Semantic Memory（带来源/验证时间），本单临时补偿方案只入 Task State，退款政策归 Knowledge 由制度所有者维护，重复查物流步骤为 Skill 候选，模型对客户情绪的猜测不写入。核验：每类信息有唯一去向且过六问。

## A2（情境应用）
场景：设计任务/会话/状态数据模型时；决定"这条信息写到哪"时；建记忆库/知识库时；多渠道（IM/Web/IDE）访问同一任务时；断点恢复与分布式运行设计时。
语言特征：「状态存哪」「session 和 task 什么关系」「记忆怎么写才不污染」「记忆库和知识库要不要分开」「断线/重启后怎么恢复」「state store / memory / knowledge base / checkpoint」。
区分：context-engineering 管窗口内视图与压缩管线（本卡是它"先落权威状态再移出"的落点）；skill-asset 管跨资产作用域与发布链（本卡管单类对象的治理细则：Memory 写入/冲突/遗忘、Knowledge 分责）；存储选型/物理承载/一致性实现归第 8 章存储单元，本卡止步逻辑接口。

## E（执行步骤）
输入契约：任务与交互形态描述（单轮/多轮/后台/多渠道）、信息类型清单（临时状态/经验/制度/方法/推测）、租户与权限结构、恢复时限要求。
1. 建 Call/Session/Task 模型：三个生命周期各自字段与 ID，Session-Task 多对多显式关联表。完成标准：任一业务事件可唯一归属某 Call/Session/Task；无"Task=消息线程"绑定。
2. 定三表示与逻辑接口：声明 Event Log/Snapshot/Checkpoint 各自承载什么、状态 Schema 与事件归并规则、安全点语义；只暴露逻辑接口。完成标准：三表示职责表 + 接口清单，无实现细节泄漏。
3. 分区 Workspace 与 Artifact 状态机：六分区落位并定义保留策略；Artifact 定流转规则与校验关口、对象元数据（ID/版本/来源/权限/状态/校验摘要）。完成标准：每类产物有起点态与验收关口。
4. Memory 设计：四分类归属 + 写入六问流水线 + 元数据模板（来源/证据/可信度/适用条件/作用域/验证时间/过期策略/版本）+ 冲突与遗忘规则；Knowledge 独立分库并声明内容所有者。完成标准：Memory 与 Knowledge 分库且各有治理理由；六问有执行载体。
5. 信息五分法路由：对典型信息逐条判去向（Task State/Memory 候选/Knowledge/Skill 候选/不写入），实时事实标注权威工具来源。完成标准：每条信息唯一去向 + 判定依据；权限过滤在进模型前按结构化模型完成。
输出契约：三生命周期数据模型图 + 接口清单 + Workspace 分区表 + Artifact 状态机 + Memory 六问检查单 + 信息路由表。判停：纯无状态单轮问答（无跨步骤事实）可简化，但只要任务可跨轮恢复，Task State 与状态外置不可省略。

## B（边界）
- Context 被当 State 用（ce-05）：关键状态只存在于窗口内，压缩/截断/误读改变任务事实，长任务与多 Agent 协作失去恢复基础——可恢复状态必须活在上下文之外（2.2.3）。
- Memory 与 Knowledge 同库混存（ce-06）：同时失去写入门槛/遗忘与权限过滤/时效两类治理能力；删除请求无法区分"衰减记忆"与"下线文档"。
- 编排层直接依赖 Runtime 内存结构（ce-08）：换 Runtime 要改编排、进程重启丢状态——接口必须是任务与状态，不是函数调用细节。
- 三类污染与两端防护（ce-23）：错误推测/失败轨迹/恶意输入被写入当经验（记忆污染）、外部文字被提升为行为规则（指令污染）、索引/缓存/摘要把一租户信息带入另一租户（跨租户污染）——写入端（来源识别/验证/作用域绑定）与读取端（身份过滤/信任标记/指令数据分离）缺一不可；撤回不能只降检索分数（ce-24，派生链见 skill-asset）。
- 一致性要求不同不可同库混存（2.3.4）：Event Log/Checkpoint 要强一致与顺序，Workspace 要可快照可回滚，Artifact 要可寻址可保留，Memory/Knowledge 要可检索可治理——一个向量库装全部等于放弃大部分要求。
- 权限不能靠自然语言标签推断（5.4）：检索结果进模型前按结构化权限模型过滤；被检索到≠可直接注入 Context，须标记"历史经验"而非当前事实。
- 反场景：无跨任务复用价值的一次性执行（单次脚本）不需要 Memory 层；强行加记忆只会引入污染面。
- 交界规则：压缩/卸载/Reset 的窗口侧规则在 context-engineering，"先持久化后移出"为两卡共用红线；Procedural Memory 升级为 Skill 的判断信号在本卡，Skill 打包/披露/发布链归 skill-asset。
