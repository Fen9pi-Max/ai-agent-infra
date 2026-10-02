# ai-agent-infra

基于阿里云开源白皮书《**AI Agent 手册**（AI Agent HandBook）》（[aliyun/ai-agent-handbook](https://github.com/aliyun/ai-agent-handbook)，2026-09）蒸馏的**企业级 Agent 工程技能**——把架构、构建、运行、治理、调优五阶段的方法论，转译为 22 个可被 agent 直接调用的原子能力。

由 cangjie-skill（RIA-TV++ v2.5 流水线）蒸馏产出：9 个并行提取器扫描全书 30 章（54 万字）→ 971 条候选 → 三重验证 → 22 个能力 → 触发盲测 10/10 + 代表任务核验 2/2 通过。

## 它能做什么（22 个能力）

| 阶段 | 能力 | 一句话规则 | 原书依据 |
|---|---|---|---|
| 架构 | arch-selection | 四维判形态、L1-L4 定成熟度，永远选最低充分架构 | 第 1-2 章 |
| 架构 | harness-entry | 先固定任务契约，四判据选四类构建入口（可组合非阶梯） | 第 3 章 |
| 构建 | task-contract | Loop 五阶段+十任务态+预算边界；模型只能申请完成、Harness 凭环境证据提交 | 第 4 章 |
| 构建 | context-engineering | Context 八步确定性管线（权限过滤先于排序），压缩保八类、先落权威状态再移出 | 第 5 章 |
| 构建 | state-layering | Call/Session/Task 三分+Memory 写入六问，信息五分法定去向 | 第 5 章 |
| 构建 | skill-asset | 沉淀四步清洗+渐进披露三层+发布链反退化，Skill 不自带权限 | 第 5 章 |
| 构建 | action-plane | 行动链七步+看见/注册/授权三分+ALLOW/DENY/ASK+PACV 幂等验证 | 第 6 章 |
| 运行 | runtime-sandbox | 按负载交付环境契约，按可信度选隔离后端，接管用执行代次围栏 | 第 7 章 |
| 运行 | state-storage | 先定每类状态对象的正确性边界再选承载；恢复=最新快照+增量回放+租约 | 第 8 章 |
| 运行 | ai-gateway | 网关只做准入转发记录；错误分类先于重试、预算跨层原子扣减、MCP 准入四步 | 第 9 章 |
| 运行 | async-completion | 完成语义五层，恢复与幂等是执行端责任；带环流转用状态机不塞 DAG | 第 10 章 |
| 运行 | multi-agent-org | 收益成本判据防过度组队；根 Task 前定团队级停止条件；权限取交集 | 第 11 章 |
| 运行 | agent-comm | 四面×四档逐段定位交互语义；流结束≠任务结束 | 第 12 章 |
| 治理 | observability | "看什么×看哪里"规划接入，排障走聚合指标→下钻，审计四要素证据链 | 第 13 章 |
| 治理 | agent-security | 盾（纵深防护）+缰绳（身份/逐次校验/二次授权）；出站默认拒绝 | 第 14 章 |
| 治理 | asset-governance | 五类资产统一"逻辑资源+不可变版本+结构化引用"；发现与资格分离 | 第 15 章 |
| 治理 | agent-simulation | 授权与后果两判据内做用户/环境模拟；零违规按 3/n 置信上界表述 | 第 16 章 |
| 调优 | tuning-attribution | 先环境→再 Harness→后模型；四组对照分离收益来源 | 第 17 章 |
| 调优 | trajectory-pipeline | Trace/Session/Trajectory 三对象区分，结论落相邻事实链 | 第 19-20 章 |
| 调优 | golden-badcase | 黄金集=业务确认题目+可判定依据；Badcase 四类分流，单变量实验评新生成 | 第 21-22 章 |
| 调优 | controlled-evolution | 按现象五路选向，经验回 Trace 核验，采用四确认+恢复预案 | 第 23 章 |
| 调优 | edge-optimization | 就地三条件分类请求；缓存键六维防串租户；"不优化"亦是正确选项 | 第 24 章 |

## 安装

```bash
git clone https://github.com/Fen9pi-Max/ai-agent-infra.git
# ZCode / Claude Code 用户级安装
cp -R ai-agent-infra ~/.zcode/skills/ai-agent-infra   # 或 ~/.claude/skills/
```

技能为单入口模式：宿主 agent 读 `SKILL.md` 的路由表，按用户意图加载 1 张能力卡执行。每张能力卡含 R（原文依据）/ I（解释）/ A1（案例，区分原书与合成演练）/ A2（触发情境与近邻区分）/ E（执行步骤与输入输出契约）/ B（边界与原书警告）六段。

## 仓库结构

```
SKILL.md                    # 技能入口（路由表+核心原则+边界）
references/capabilities/    # 22 张能力卡
references/{cheatsheet,glossary,overview,capability-index}.md
test-prompts.json           # 12 条盲测用例（10 触发 + 2 代表任务）
test-results.md             # 压力测试报告（12/12 通过）
distillation/               # 蒸馏审计轨迹
├── BOOK_OVERVIEW.md        # 阶段 0 整书理解（含 24 项关键任务清单）
├── verified.md             # 阶段 1.5 三重验证结果（22 单元）
├── coverage-audit.md       # 任务→能力覆盖审计（24/24）
├── GLOSSARY.md             # 20 个共享术语
├── DIGEST.md               # 面向读者的精华长文
├── references.md           # 参考内容分流映射
├── PIPELINE_STATE.md       # 流水线状态
└── candidates/             # 971 条原始候选（frameworks×4/principles×2/cases/counter-examples/glossary）
```

## 上游与许可

- 源书：[aliyun/ai-agent-handbook](https://github.com/aliyun/ai-agent-handbook)（Apache-2.0）
- 本仓库同样以 Apache-2.0 发布；能力卡中的原文摘引（每条 ≤150 字）与案例证据均标注原书章节出处。
- 书中定量数字（成本比例、性能倍数等）均为原书示例，使用前需以企业自身基线校准。
