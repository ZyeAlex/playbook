# 证据定位层（locateSegments）

## 适用场景

审核落库的 `evidences[]` 需在前端 docx-preview / pdf.js 上**精确定位**，但预览 HTML **无 blockId**。  
在**不改 LLM `evidenceBlockIds` 契约**的前提下，为每条证据附加 DOM 定位专用字段 `locateSegments[]`。

## 与 RAG 主字段的关系

| 字段 | 用途 | 谁写 |
|------|------|------|
| `text` / `sourceBlockIds` | LLM 引用语义、左栏全文展示 | Judge + 证据门控 |
| `locateSegments[]` | 前端逐段 DOM 匹配 | 审核 executor 落库时派生 |
| `pageFrom` / `pageTo`（证据顶层） | 分页容器选择 | leaf segment 的 min/max 覆盖 |

**禁止**：用检索 top-k 候选直接填 `locateSegments`；禁止改 Judge 输出块 ID 规则。

## 生成时机

审核 executor 在 `_evidence_ref_payload` 落库前，对每条 cited parent 块：

1. 用 parent-child registry 的 `source_block_ids_for_parent(parent_id)` 取 **leaf 列表**（文档顺序）
2. 从当次 `parsed.blocks` 查每个 leaf 的 `DocumentBlock`
3. 调用 `build_locate_segments()` 写入 `locateSegments`
4. 用 `page_range_from_segments()` 回写证据顶层 `pageFrom` / `pageTo`

旧任务无 `locateSegments` → 前端走 legacy 策略（`expandParagraphRun` 等），**无需 DB 迁移**。

## LocateSegment 字段

| 字段 | 说明 |
|------|------|
| `blockId` | leaf 块 ID（P-xxxx / TBL-xxx） |
| `blockType` | paragraph / table / table_row |
| `locateKind` | body / table_body（表块专用） |
| `orderIndex` | 解析器 blocks 下标 |
| `excerpt` | 前 **120 字**正文，供 DOM 模糊匹配 |
| `pageFrom` / `pageTo` | 分页预览容器 |
| `path` / `tableNo` / `rowFrom` / `rowTo` | 与块元数据一致 |

`excerpt` 生成：去 path 首行前缀后截断；**不是**落库 `text` 的替代品。

## 不做的事（已验证）

- **不**合成 `caption` 段：parent 文本含「表 15-1：…」但 leaf 全是 paragraph 时，表题留在段落正文里，由前端按 excerpt 匹配；避免 `findTableAfterCaption` 误亮整表/表头。
- **不**注入 docx-preview `data-block-id`（成本高，另项）。

## 可选：文档块索引 API

只读调试接口，**非**审核热路径：

`GET /audits/documents/{documentFileId}/blocks`

- 优先读审核索引 cache 的 `parsed`
- miss 时 parse 一次（不写完整向量 cache）
- 返回轻量列表：`blockId, blockType, pageFrom, path, excerpt, orderIndex, tableNo, rowFrom`

## 踩坑

| 现象 | 处理 |
|------|------|
| 前端仍走旧策略 | 须**重跑审核**才有 `locateSegments` |
| excerpt 太短匹配失败 | 左栏展示用完整 `evidence.text`；匹配仍用 excerpt |
| 解析/索引版本变更 | 须重跑审核；segments 与旧 parsed 不对齐 |

## 反模式

- 把 `locateSegments` 写进 LLM prompt 或当作证据引用来源
- 用 segment excerpt 替代 `prune_cited_evidence` 后的 `text` 落库
- 为 parent 块只写一个 segment 而不展开 leaf

## 项目来源

Word 审核 parent 块夹表、设备多行段落误匹配，2026-06 验证。
