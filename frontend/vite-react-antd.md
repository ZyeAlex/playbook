# Vite + React + Ant Design

## 适用场景

中后台 SPA，REST + SSE，本地 dev 代理 API。

## 推荐版本组合（曾验证）

Vite 6、React 19、Ant Design 6、React Router；`ConfigProvider` + 中文 locale。

## 开发代理

```javascript
proxy: {
  '/api': {
    target: 'http://localhost:8000',  // 后端端口按项目配置
    changeOrigin: true,
    timeout: 600_000,
    proxyTimeout: 600_000,
  },
}
```

**必须拉长 proxy 超时**：大文件上传、NDJSON 流式提取、长审核否则会断连。

## 长任务详情页

- 列表接口常为轻量快照 → 详情页**单独请求全量**（含子结果数组）
- 全局 Context 封装 `loadXxx(id)`，避免路由切换仍用旧快照

## UI 约定

- 移动断点常用 750px
- 弹窗：antd Modal + Portal 壳，便于统一样式
- 页头 KPI：多页共用同一 CSS 类，避免单页覆盖导致高度不一致

## 反模式

- 默认 30s 代理超时跑流式 API
- 详情依赖列表项内嵌的 incomplete payload

## 项目来源

企业文档审查前端，2026 年验证。
