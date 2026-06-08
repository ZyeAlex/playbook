# LLM 与向量可配置项

## 适用场景

同一套后端服务多环境/多模型，阈值运营可调。

## 建议 JSON 字段

| 字段 | 用途 |
|------|------|
| llm_base_url / llm_model / llm_timeout | 判定与提取 Agent |
| embedding_model | 如 bge-m3 |
| embedding_hybrid_dense/sparse_weight | hybrid 权重 |
| reranker_model | 如 bge-reranker-base |
| review_confidence_threshold | 综合分转 Review |
| evidence_semantic_min_score | 语义门控 |
| evidence_semantic_gate_enabled | 开关 |

## API Key

设置界面编辑；列表脱敏显示。

## 场景字典

与 LLM 配置分离：业务场景启停 + 说明文案，可 PATCH 即时生效。

## 注意

改 embedding/reranker 模型名须配合模型缓存部署；改阈值影响历史任务判定的**新跑**结果，不重算旧单。

## 项目来源

可配置 AI 中台，2026 年验证。
