# FastAPI SSE 事件总线

## 适用场景

同步 worker 线程向多个浏览器推送任务进度。

## 模式

1. lifespan 启动时 `bind_loop(asyncio loop)`
2. worker 线程 `publish(dict)` → 线程安全 schedule 到 loop
3. 路由 `StreamingResponse(media_type="text/event-stream")`

## 载荷

```text
data: {"type":"task","progress":42,...}\n\n
```

## 注意

- 单任务流与全局流可分开端点，减少无关流量
- 建立 SSE 前可触发「确保任务在跑」逻辑（与 `async-job-resume.md` 同目录配合）
- 开发热重载时 SSE 连接会拖 shutdown，需合理 timeout

## 反模式

- 在 worker 里直接操作 WebSocket 无 loop 绑定
- 只有轮询无 SSE（分钟任务体验差）

## 项目来源

长任务进度推送，2026 年验证。
