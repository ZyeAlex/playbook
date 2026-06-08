# Parent-Child 文档切片

## 适用场景

长章节多段落：检索要细粒度，LLM 要上下文连贯。

## 两层

| 层 | 边界 | 用途 |
|----|------|------|
| Child | 单段或整表 | embedding、rerank |
| Parent | 同 path 连续段合并 | LLM 输入、引用 ID |

表：child = parent = 整表。

## Parent 合并规则

1. 同 `path` 相邻段落合并
2. 过短（如累计 &lt;150 字）→ 向上一级 path 合并，减少碎段
3. 合并后字符上限（如 2000）截断

## index_text 格式

```
{path}\n{正文}
```

## 检索→推理映射

```
child search → parent 去重 → rerank on child → map to parent
→ 可选 hybrid 救援 parent → merge（救援优先）→ LLM pool
```

**救援优先**：未进 rerank 的高分 parent 插到池前，避免被 `top_k` 截断。

## 缓存

索引产物按**文档内容哈希 + INDEX_CACHE_VERSION** 持久化，避免重复 embedding。

## 反模式

- LLM 只看 child 碎段判「报告完整性」
- parent 不设上限合并成长文超上下文

## 项目来源

Parent-Child RAG 流水线，2026 年验证。
