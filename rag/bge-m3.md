# BGE-M3 混合向量

## 适用场景

中英混合长文档 hybrid 检索（dense + sparse lexical）。

## 模型与库

- 模型：`BAAI/bge-m3`
- Python：`FlagEmbedding.BGEM3FlagModel`

## 推荐参数

| 参数 | 建议值 | 说明 |
|------|--------|------|
| hybrid dense 权重 | 0.4 | 可配置，需归一化后和为 1 |
| hybrid sparse 权重 | 0.6 | |
| passage batch | 8 | 小批次降峰值内存 |
| query max length | 512 | |
| index max length | 1024 | 建索引可略长 |
| 单条截断 | 2000 字符 | |
| hybrid 候选上限 | 128 | 打分阶段 |

## 离线部署

1. 模型快照放统一缓存目录
2. 有本地 snapshot → `HF_HUB_OFFLINE=1` 加载
3. 超时 → 镜像站环境变量 + 预下载脚本写进部署文档

## warm_up

长任务开始前显式加载模型，**早失败**（避免解析/建索引后才报错）。

## fp16

有 CUDA 时 `use_fp16=True`，CPU 自动 false。

## 踩坑

- 首次在线拉模型阻塞首单 → 部署流程必须含预下载
- batch 过大 OOM → 优先减 batch 而非盲目减 max_length

## 项目来源

文档审核混合检索，2026 年验证。
