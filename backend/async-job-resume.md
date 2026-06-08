# 异步长任务与热重载续跑

## 适用场景

FastAPI 接收请求后，分钟级 CPU/GPU 任务在后台跑；开发环境 uvicorn `--reload`。

## 推荐架构

```
HTTP handler → asyncio.create_task
            → ThreadPoolExecutor(max_workers=1)  # 重模型流水线
            → 同步 executor.run()
```

Excel 解析、LLM HTTP 用**独立**线程池，勿与重模型 worker 共用。

## 续跑

| 触发 | 行为 |
|------|------|
| 进程启动 | 扫描 DB「进行中」任务，重新投递 |
| 打开详情/SSE | 若无活跃 worker 则续跑 |

续跑时**跳过已完成条目**（按稳定 sort_index / rule_id），避免重复 LLM。

## 热重载踩坑

| 现象 | 原因 | 解法 |
|------|------|------|
| 进度永停 2% | shutdown 后线程池未重建 | 惰性检测 `_shutdown` 并 new 池 |
| 进度停中间 | reload 取消 asyncio，DB 仍进行中 | 启动/访问时续跑 |
| reload 卡死 | SSE 长连接 | 缩短 graceful shutdown 秒数 |

**Bootstrap 僵尸**：进度极低且文案为「已保存/准备执行」类 → 取消无效 asyncio 任务并重新调度。

## 取消

用户删任务 → 协作式取消标志 + cancel asyncio；**无法**强杀已运行线程，靠检查点退出。

## 反模式

- `max_workers` 多开加载两份向量+重排模型
- 热重载后假设内存 Task 字典仍有效
- shutdown 线程池后同一进程继续 submit

## 项目来源

文档审核后台 worker，2026 年验证。
