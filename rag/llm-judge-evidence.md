# LLM 判定 Agent（少引证据）

## 适用场景

给定规则 + 检索证据块列表，输出 Pass/Partial/Fail/N/A/Review 与引用块。

## 输出契约

| 字段 | 要求 |
|------|------|
| resultType | Pass / Partial / Fail / N/A / Review |
| confidence | 0–1，整条规则 |
| evidenceBlockIds | **默认 1，最多 3** |
| evidenceScores | 与 ids 一一对应 `{blockId, confidence}`，块 **≥0.7** 才可引用落库 |
| quote | 来自 evidence.text，建议 ≤300 字 |
| reason | 说明每块如何支撑规则，≤200 字 |

兼容旧版时可 fallback `evidenceRelevance` 字段名。

## 提示词要点

- 只能依据给定 evidence，禁止编造
- 勿滥引：章节总则清单、同主题重复段、跨检查点凑数
- 占位符（XXXX/TBD）→ 优先 Review
- 未检索到 ≠ N/A（需证据表明不适用）

## 置信度使用

| 层级 | 字段 | 阈值（默认） | 用途 |
|------|------|-------------|------|
| 规则综合 | 门控公式 + `reviewConfidenceThreshold` | **0.75** | Pass/Partial/N/A → Review |
| 证据块 | Judge `evidenceScores` + `evidenceBlockMinConfidence` | **0.7** | 低于阈值 **不落库/不展示**；Judge system prompt **运行时读取该配置** |
| 语义兜底 | BGE `evidenceSemanticMinScore` + 规则 LLM 置信 | **0.35** + **<0.75** | Pass/Partial → Review |

## 反模式

- 不限制 evidenceBlockIds 数量（落库 6+ 段不可复核）
- 用检索分数代替 Judge 引用

## 项目来源

文档合规 Judge Agent，2026 年验证。
