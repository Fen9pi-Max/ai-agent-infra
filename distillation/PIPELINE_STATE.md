# ai-agent-handbook 蒸馏流水线状态

- **书目**: AI Agent 手册（AI Agent HandBook）
- **来源**: https://github.com/aliyun/ai-agent-handbook （EPUB 为本地转换产物）
- **EPUB**: books/ai-agent-handbook/ai-agent-handbook.epub（14.7MB，构建脚本 build_epub.py；副本在 /Users/huifeng/Code/ebook/books/）
- **分章文本**: .cangjie/index/chapters/01-229.txt（54 万字符，索引 .cangjie/index/chapter-index.txt）
- **使用目的**: 企业级 Agent 架构/构建/运行/治理/调优方法论 → single（入口 ai-agent-infra）
- **首次试点**: 否
- **运行模式**: 自主模式（用户不在线，各"轻确认"门以书面决策记录替代）

## 阶段进度（全部完成 2026-10-02）

| 阶段 | 状态 | 结果 |
|---|---|---|
| 0 整书理解 | ✅ | BOOK_OVERVIEW.md（24 项关键任务清单） |
| 1 并行提取 | ✅ | 9 agents：framework×4 分区全量 + principle×2 重点区 + case×1 + counter×1 检索 + glossary×1 → 971 条候选 |
| 1.5 三重验证 | ✅ | 22 单元全部 verified（284+227+142+111 条候选归类，重复以 f 系列为 canonical） |
| 1.6 晋级门 | ✅ | single 模式：22/22 router，由唯一入口 ai-agent-infra 服务（destinations.json） |
| 2 RIA++ 能力卡 | ✅ | cards/ 22 张六段卡 + verified.yaml（assemble_bundle.py 组装） |
| 3 Zettelkasten | ✅ | 86 条 also_read（含 13 对跨分区链接）+ GLOSSARY 20 术语 + book/overview.md |
| 4 压力测试 | ✅ | 盲测触发 10/10（3 诱饵全拦截）+ 代表任务 2/2，报告 .cangjie/test-results-stage4.md |
| 5 编译交付 | ✅ | dist/ single 编译（run-20261002-235301-121646），validate 0 errors；安装 ~/.zcode/skills/ai-agent-infra/；发布 Fen9pi-Max/ai-agent-infra（私有） |

## 下次更新入口

- 新增材料/覆盖审计：从 PIPELINE_STATE 续跑，编译用 `python3 scripts/cangjie.py compile --bundle books/ai-agent-handbook/.cangjie/capabilities --out books/ai-agent-handbook/dist --output single --yes`（cangjie-skill 根目录执行）
- 非阻断改进项（压力测试观察）：capability-index 的 tuning-attribution 行可补"丢约束/归因"中文信号；harness-entry 的"云控制台"信号保持聚焦选型决策
- 发布仓库：/Users/huifeng/Code/Skill/ai-agent-infra/（含 distillation/ 审计轨迹）

## 分工决策（阶段 1，供复用）

- 54 万字符超单 agent 上下文：framework 按篇分 4 区全量扫描；principle 聚焦判据/清单/公式密集章（1-3、8-9、13-14、17.1、24）
- 实践篇（25-28 章企业案例）作为案例证据源，不直接产出能力
- 全书归因次序"先环境→再 Harness→后模型"是防最贵误判的核心，U19/U21 定 critical
