# 证据定位层（locateSegments）

## 适用场景

审核落库 `evidences[]` 需在前端 docx-preview / pdf.js 上**精确定位**，但预览 HTML **无 blockId**。  
在**不改 LLM `evidenceBlockIds` 契约**前提下，为每条证据附加 DOM 定位字段 `locateSegments[]`。

前端匹配实现：[`../frontend/evidence-highlight-dom.md`](../frontend/evidence-highlight-dom.md)。

## 与 RAG 主字段的关系

| 字段 | 用途 | 谁写 |
|------|------|------|
| `text` / `sourceBlockIds` | LLM 引用语义、左栏全文（含表体） | Judge + 证据门控 |
| `locateSegments[]` | 前端逐段 DOM 匹配 | executor 落库时派生 |
| `pageFrom` / `pageTo`（证据顶层） | 分页容器 | leaf segment min/max |

**禁止**：检索 top-k 直填 `locateSegments`；禁止改 Judge `evidenceBlockIds` 契约。

## 与 Context Parent 对齐

索引 `audit-index-v5-context-table` 起，Context Parent `PU-*` 的 `source_block_ids` **有序含 TBL-***，parent.text 含表体。

| 后端 | 前端 |
|------|------|
| `sourceBlockIds` 含 `TBL-*` | 左栏整段 `evidence.text`；定位 **1 组** |
| `build_locate_segments` 展开全部 leaf | 段+表+段 `mergeAdjacentLocateGroups` 合并 |
| 表题 parser 段落 `P-*` + 表 `TBL-*` | 表题段可 `used` 后仍用 caption 锚整表 |

## 生成时机

executor `_evidence_ref_payload` 落库前，对每条 cited parent：

1. `source_block_ids_for_parent(parent_id)` → 有序 leaf（**含 TBL-***）
2. 从 `parsed.blocks` 查各 leaf
3. `build_locate_segments()` → `locateSegments[]`
4. `page_range_from_segments()` → 证据 `pageFrom` / `pageTo`

旧任务无 segments → 前端 legacy（`expandParagraphRun` 等），**无需 DB 迁移**。

## LocateSegment 字段

| 字段 | 说明 |
|------|------|
| `blockId` | leaf ID（`P-*` / `TBL-*`） |
| `blockType` | paragraph / table / table_row |
| `locateKind` | body / table_body |
| `orderIndex` | 解析器 blocks 下标 |
| `excerpt` | 前 **120 字**，供 DOM 模糊匹配（**非**落库 text 替代） |
| `pageFrom` / `pageTo` | 分页 |
| `path` / `tableNo` / `rowFrom` / `rowTo` | 块元数据 |

## 不做的事

- **不**合成独立 `caption` segment（表题在 parser 已是 `P-*` body）
- **不**注入 docx-preview `data-block-id`
- **不**用 excerpt 替代 `prune_cited_evidence` 后的 `text`

## 可选：块索引 API

`GET /audits/documents/{documentFileId}/blocks` — 调试只读，非热路径。

## 踩坑

| 现象 | 处理 |
|------|------|
| 无 segments | 须**重跑审核**（索引 v5 变更后亦须重跑） |
| excerpt 短、DOM 未命中 | 左栏仍用完整 `text`；Context Unit 定位合并为 1 组 |
| parent 仅引 PU、表体在 parent.text | 正常；`sourceBlockIds` 应含 TBL |

## 反模式

- locateSegments 写进 LLM prompt
- parent 只写一个 segment 不展开 leaf
- 表 child 仍当独立 parent（v4 行为，已废弃）

## 项目来源

Context Unit v5 + locateSegments，2026-06 验证。
