# 向量模型离线部署与开发热重载

## 适用场景

内网/低配机跑 BGE + reranker；本地 uvicorn `--reload`。

## 离线

1. 统一模型缓存目录（环境变量或 hub cache）
2. 部署前预下载 embedding + reranker
3. 失败时：镜像站 endpoint + 明确错误文案指引预下载

## 开发热重载

```bash
uvicorn app:app --reload --reload-dir src --timeout-graceful-shutdown 2
```

- 仅 watch 源码目录，**不** watch 模型/数据目录
- 缩短 graceful shutdown，避免 SSE 拖死 reload

## 与长任务

见 `backend/async-job-resume.md`：shutdown 须 cancel 任务 + 重建线程池。

## 备份

数据库文件 + 上传原件目录 + LLM 配置 JSON，一并备份。

## 项目来源

本地 AI 文档服务运维，2026 年验证。
