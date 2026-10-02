# coverage-audit — 原书关键任务 → 交付去向（阶段 1.5/3 更新）

基准：BOOK_OVERVIEW.md 的 24 项关键任务清单。22 个能力单元（U6 为构建侧补充单元，服务 task-15/23 的前置能力）。

| task_id | 读者任务 | 候选依据 | 交付去向 | 判定 |
|---|---|---|---|---|
| task-01 | 形态与成熟度选型 | f1-01..24 + p1-01..29 | U1 arch-selection | ✅ 覆盖 |
| task-02 | Harness 构建入口选择 | f1-25..36 + p1-59..83 | U2 harness-entry | ✅ 覆盖 |
| task-03 | 任务状态机与完成验证 | f1-37..54 + p1-83..117 | U3 task-contract | ✅ 覆盖 |
| task-04 | Context 管线与压缩 | f1-55..65 + p1-118..134 | U4 context-engineering | ✅ 覆盖 |
| task-05 | Session/TaskState/Workspace/Memory/Knowledge | f1-66..75 + p1-135..153 | U5 state-layering | ✅ 覆盖 |
| task-06 | Action Plane 与权限/HITL | f1-85..114 + p1-44..58 | U7 action-plane | ✅ 覆盖 |
| task-07 | 沙箱与运行时 | f2-01..14 | U8 runtime-sandbox | ✅ 覆盖 |
| task-08 | 状态存储分层选型 | f2-15..42 + p2-01..56 | U9 state-storage | ✅ 覆盖 |
| task-09 | AI 网关三语义 | f2-43..58 + p2-57..120 | U10 ai-gateway | ✅ 覆盖 |
| task-10 | 异步/定时/工作流与完成语义 | f2-59..72 | U11 async-completion | ✅ 覆盖 |
| task-11 | 多 Agent 团队与编排 | f2-73..90 | U12 multi-agent-org | ✅ 覆盖 |
| task-12 | 通信四面选型 | f2-91..108 | U13 agent-comm | ✅ 覆盖 |
| task-13 | 可观测性与审计 | f3-01..20 + p2-121..138 | U15 observability | ✅ 覆盖 |
| task-14 | Agent 安全防护 | f3-21..30 + p2-139..173 | U16 agent-security | ✅ 覆盖 |
| task-15 | AI 资产注册、版本与发现 | f3-31..64 | U17 asset-governance（构建侧 U6 skill-asset 为前置） | ✅ 覆盖 |
| task-16 | 上线前 Agent Simulation | f3-65..89 | U18 agent-simulation | ✅ 覆盖 |
| task-17 | 调优归因 | f4-01..03 + p2-174..178 | U19 tuning-attribution | ✅ 覆盖 |
| task-18 | 模型调优路线（SFT/RL/蒸馏） | f4-04..28 + p2-179..188 | U19 tuning-attribution | ✅ 覆盖 |
| task-19 | 轨迹数据组织 | f4-29..38 | U20 trajectory-pipeline | ✅ 覆盖 |
| task-20 | 运行时数据 Pipeline | f4-39..43 | U20 trajectory-pipeline | ✅ 覆盖 |
| task-21 | 黄金数据集 | f4-44..49 | U21 golden-badcase | ✅ 覆盖 |
| task-22 | Badcase 闭环 | f4-50..55 | U21 golden-badcase | ✅ 覆盖 |
| task-23 | 受控自进化 | f4-56..62 | U22 controlled-evolution | ✅ 覆盖 |
| task-24 | 边缘/全球优化 | f4-63..71 + p2-189..213 | U23 edge-optimization | ✅ 覆盖 |

## 参考内容映射（不编译为 active 能力）

| 原书内容 | 去向 |
|---|---|
| 实践篇 16 企业案例 + GOAI 9 作品 + 调研报告 | candidates/cases.md → 各能力卡 A1 段证据 |
| 149 条失败模式 | candidates/counter-examples.md → 各能力卡 B 段证据 |
| f2-108 运行篇两主线导读 | Bundle book/overview.md 分区导航 |
| 阿里云产品操作细节（AgentSpace/Loopie/百炼等） | 能力卡 B 段"以通用机制表述，产品名为示例" |
| Agentic OS 展望（第 30 章） | Bundle book/overview.md 参考区 |
| 前言"智以致用"叙事 | Bundle book/overview.md 背景 |

## 结论

24/24 关键任务全部有候选支撑与明确交付去向，无未解释遗漏。薄弱点：task-12（通信）原书偏概念模型，可执行规则少于其他章，已在 U13 卡 B 段声明。
