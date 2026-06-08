# BGE Reranker（交叉编码器重排）

## 适用场景

hybrid 粗召后精排候选块（段落/表）。

## 模型

默认 `BAAI/bge-reranker-base`；v2-m3 等需单独部署验证后再换。

## 分数归一化

| 来源 | 归一化 |
|------|--------|
| reranker logit | sigmoid → [0,1] |
| hybrid 原始分 | clamp [0,1]，**勿**对 hybrid 套 sigmoid |

量纲混用会导致门控阈值漂移。

## 推荐参数

| 参数 | 建议值 |
|------|--------|
| rerank top-k | 8–12 |
| 归一化下限 | ~0.22（过滤后送 LLM） |

## 经验

- 盲目加大 rerank top-k 挤占 LLM 池与表附带槽
- rerank 偏「语义相近重复段」时，稀有段（如 QPP 编号段）难进池 → 需救援槽或多样性策略

## 资源

与 embedding 同属重模型；避免多进程同时加载。

## 项目来源

RAG 精排环节，2026 年验证。
