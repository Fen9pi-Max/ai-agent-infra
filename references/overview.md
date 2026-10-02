# 《AI Agent 手册》整书概览（Bundle book/overview）

## 背景与主旨

阿里云开源技术白皮书（github.com/aliyun/ai-agent-handbook，2026-09），前身为 2025-09《AI 原生应用架构白皮书》。核心命题"智以致用"：智能已被造出来（模型），如何把它组织起来、约束起来，转化为可规模化交付的生产力——中间的"以"就是 Harness 工程。

全书沿生命周期五阶段展开：架构（1-2 章）→ 构建（3-6 章）→ 运行（7-12 章）→ 治理（13-16 章）→ 调优（17-24 章），辅以实践篇（25-29 章，16+ 企业案例与 9 个 GOAI 大赛作品）与展望（第 30 章 Agentic OS）。

## 五阶段阅读地图（运行篇两主线导航，f2-108 转参考）

- **架构篇**：对象界定（Agentic Application 定义/边界/成熟度）+ 结构落位（组件/平台/生命周期三视图，五能力责任域）
- **构建篇**：四类构建入口选择 + 任务/信息/行动三类工程契约（共用贯穿案例：生产服务漏洞修复与变更发布 Agent）
- **运行篇**：主线一"运行的地基"（第 7 执行环境、第 8 状态存储、第 9 流量网关——建议先读）；主线二"运行的秩序"（第 10 异步任务、第 11 多 Agent 协作、第 12 分布式通信）
- **治理篇**：第 13 可观测是其余三章的共同基础；第 14 安全、第 15 资产、第 16 仿真可按关注点选序
- **调优篇**：归因决定主线——模型调优（第 17）vs 智能体调优（第 18-23 数据飞轮）；第 24 边缘为独立专题

## 能力索引（22 个能力 → 五阶段落位）

| 阶段 | 能力（capability_id） |
|---|---|
| 架构 | arch-selection, harness-entry |
| 构建 | task-contract, context-engineering, state-layering, skill-asset, action-plane |
| 运行 | runtime-sandbox, state-storage, ai-gateway, async-completion, multi-agent-org, agent-comm |
| 治理 | observability, agent-security, asset-governance, agent-simulation |
| 调优 | tuning-attribution, trajectory-pipeline, golden-badcase, controlled-evolution, edge-optimization |

## 全书五项架构共识（第 30 章 30.1）

1. 架构对象是系统，不是一次模型调用
2. 任务是需要被长期管理的对象（状态外置、可暂停可恢复）
3. 能力是动态接入的，能力发现与执行授权必须分开
4. 自主性必须与权限、可撤销范围和可验证性匹配
5. 证据是变更能否发布的前提

## 参考内容（不编译为 active 能力）

- **Agentic OS 展望（第 30 章）**：六类跨应用工程约束（任务统一标识/环境一等资源/能力元数据三声明/上下文状态边界/授权派生收回/证据口径统一）指向应用之下的共享系统层。属前瞻推测，非已验证形态。
- **2026 调研报告**：1906 份问卷；关键数据——有评估体系的企业任务成功率约为无评估企业的两倍；轨迹自动评估仅占 6%；多 Agent 最大痛点是状态与上下文衰减（60%）。时点性数据，作背景引用。
- **实践篇案例**：16+ 企业案例与 GOAI 作品，已在各能力卡 A1 段作为证据引用（Kitta、PatchPilot、PolarDB-X Loop、畅捷通四阶段、MiniMax 记忆底座、塔斯汀三元组等）。

## 适用边界（自 BOOK_OVERVIEW 批判段）

- 阿里云产品视角强耦合：具体产品（百炼/AgentCore/PolarDB 等）在能力卡中仅作示例，机制以通用表述为准
- 面向中大型企业平台团队；小团队需按最低充分原则裁剪，避免体系化过度设计
- 书中定量数字均为原书示例假设，需以企业自身基线校准
- 协议层（MCP/A2A/AG-UI）快速演化，涉及时效性内容已在各卡 B 段标注
