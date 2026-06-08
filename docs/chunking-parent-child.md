# Parent-Child 文档切片（Context Unit）

## 适用场景

长章节多段落 + 中间夹表：检索要细粒度，LLM / 落库要**含表体的连续叙事上下文**。

## 两层

| 层 | 边界 | 用途 |
|----|------|------|
| **Child** | 单段 `P-*` 或整表 `TBL-*` | embedding、rerank |
| **Context Parent** `PU-*` | 文档序连续块（段落 + 表题段 + 整表 + 后续段） | LLM 输入、引用 ID、`sourceBlockIds` |

**表不再自成 parent**：`TBL-*` child 映射到所属 `PU-*`；`table_row` 不参与向量索引。

## Context Unit 切分（`chunking/parent_child.py` + `context_text.py`）

1. 按 `document.blocks` **文档顺序**累加 leaf（paragraph / table）
2. **Section** 块 → 结束当前 unit
3. 字符上限：
   - 纯段落 unit：**2000**（软目标）
   - 含表 unit：**8000**；**不拆分 TBL**
   - 将溢出且下一块是 table → 先 flush 段落缓冲，再开含整表的新 unit
4. 稀疏段落（&lt;150 字）→ 与相邻同 section unit 合并，**不得跳过中间 TBL**

Parent 正文：`join_context_unit_text()` — 段落 `block.text`；表 `title + 表体`（caption 段与 title 去重）。

## index_text 格式

```
{path}\n{正文}
```

## 检索→推理映射

```
child search → parent 去重 → rerank on child → map to parent (PU-*)
→ 可选 hybrid 救援 parent → merge（救援优先）→ LLM pool
→ attach_referenced_table_blocks（仅当 PU 尚未含 TBL 时附带）
```

## 缓存

索引产物按**文档内容哈希 + INDEX_CACHE_VERSION** 持久化。

当前版本：`audit-index-v5-context-table` — **变更后须重跑审核**。

## 业界对照

LangChain ParentDocumentRetriever、Unstructured surrounding context、Multi-vector：**child 细检索 / parent 完整上下文**；表作为叙事单元的一部分，不从中间切开。

## 反模式

- LLM 只看 child 碎段判「报告完整性」
- 表单独 parent，导致 evidence 有表题无表体
- 用正文关键词过滤 parent 合并

## 项目来源

Parent-Child RAG + Context Unit 表格连续切块，2026-06 验证。
