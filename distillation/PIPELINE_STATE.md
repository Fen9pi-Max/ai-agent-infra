# ai-agent-handbook 蒸馏流水线状态

- **书目**: AI Agent 手册（AI Agent HandBook）
- **来源**: https://github.com/aliyun/ai-agent-handbook （EPUB 为本地转换产物）
- **EPUB**: books/ai-agent-handbook/ai-agent-handbook.epub（14.7MB，构建脚本 build_epub.py）
- **分章文本**: .cangjie/index/chapters/01-229.txt（54 万字符，索引 .cangjie/index/chapter-index.txt）
- **使用目的**: 企业级 Agent 架构/构建/运行/治理/调优方法论 → single-first（接入日常工作流可后续升级 pack）
- **首次试点**: 否（用户已有 7 本书蒸馏经验）
- **运行模式**: 自主模式（用户不在线，各"轻确认"门以书面决策记录替代）

## 阶段进度

| 阶段 | 状态 | 备注 |
|---|---|---|
| 0 整书理解 | ✅ 2026-10-02 | BOOK_OVERVIEW.md |
| 1 并行提取 | 🔄 进行中 | 9 agents：framework×4 分区全量 + principle×2 重点区 + case×1 + counter×1 检索 + glossary×1 检索 |
| 1.5 三重验证 | ⬜ | |
| 1.6 晋级门 | ⬜ | |
| 2 RIA++ 能力卡 | ⬜ | |
| 3 Zettelkasten | ⬜ | |
| 4 压力测试 | ⬜ | |
| 5 编译交付 | ⬜ | |

## 分工决策（阶段 1）

- 全书 54 万字符，方法论核心区为第 3-24 章（约 38 万字符），超出单 agent 上下文，framework 按篇分 4 区全量扫描
- principle 聚焦判据/清单/公式密集章（1-3、8-9、13-14、17、24），其余由 framework 交叉提取，阶段 1.5 去重
- 实践篇（25-28 章企业案例）作为案例证据源，不直接产出能力
- 调优判据"先环境→再 Harness→后模型"是全书反复强调的归因次序
