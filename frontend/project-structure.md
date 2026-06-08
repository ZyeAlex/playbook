# Vite + React 前端项目结构

## 适用场景

中后台 SPA：Vite + React + Ant Design + React Router，REST/SSE 调后端。  
新项目「初始化前端结构」时按本文脚手架；业务页与组件后续按需追加。

## 推荐技术栈

| 项 | 选择 |
|----|------|
| 构建 | Vite 6 |
| UI | React 19 + Ant Design 6 |
| 路由 | React Router 7 |
| 测试 | Vitest（`utils/**/*.test.js`） |
| API | 原生 `fetch`，按域拆分 `src/api/*.js` |

工具参数（代理超时、版本组合）→ [`vite-react-antd.md`](./vite-react-antd.md)。

## 目录树（起步骨架）

```txt
frontend/
├── index.html
├── package.json
├── vite.config.js          # /api 代理 → localhost:8000
├── src/
│   ├── main.jsx            # createRoot、antd ConfigProvider + zhCN
│   ├── App.jsx             # BrowserRouter + Routes
│   ├── index.css           # 全局样式、断点、布局 token
│   ├── polyfills.js        # 浏览器兼容（按需）
│   ├── api/
│   │   └── health.js       # GET /api/health 示例
│   ├── assets/
│   │   └── .gitkeep
│   ├── components/
│   │   ├── MainLayout.jsx  # 侧栏/顶栏壳 + Outlet
│   │   ├── PageBanner.jsx
│   │   ├── PanelSection.jsx
│   │   ├── Btn.jsx
│   │   ├── Modal.jsx
│   │   └── FormControls.jsx
│   ├── context/
│   │   └── AppStateContext.jsx   # 全局加载、SSE、跨页状态
│   ├── domain/
│   │   └── constants.js          # 枚举、断点、状态文案
│   ├── hooks/
│   │   └── .gitkeep
│   ├── pages/
│   │   ├── DashboardPage.jsx
│   │   └── SettingsPage.jsx      # 占位页，验证路由
│   └── utils/
│       ├── confirm.js            # 统一确认框
│       └── request.js            # fetch 封装（可选，或内联在 api/）
└── dist/                   # build 产物（gitignore）
```

**变大后再加**（勿起步就建）：

```txt
src/pages/{业务}Page.jsx
src/api/{域}.js              # 一文件一 REST 域
src/components/{Viewer}.jsx    # 文档预览、证据高亮等
src/domain/{域}Domain.js
src/utils/{feature}/           # 多文件工具子包
src/hooks/use{X}.js
```

## 分层职责

| 层 | 路径 | 职责 |
|----|------|------|
| 入口 | `main.jsx` | antd 主题/locale、全局 CSS |
| 路由 | `App.jsx` | 路径表；业务页懒加载可后加 |
| 布局 | `components/MainLayout.jsx` | 导航 + `<Outlet />` |
| 页面 | `pages/*Page.jsx` | 单路由视图；组合 components + hooks |
| API | `api/*.js` | `fetch('/api/...')`；错误解析、分页归一化 |
| 状态 | `context/` | 跨页共享（列表缓存、SSE 订阅） |
| 领域 | `domain/` | 常量、默认值、纯函数映射（无 DOM） |
| 工具 | `utils/` | 纯函数；可放 `*.test.js` |
| 组件 | `components/` | 可复用 UI；无直接路由依赖 |

**调用方向**：`pages` → `api` / `context` / `hooks` → `utils` / `domain`；`components` 尽量无业务 API 调用（容器组件例外）。

## 关键文件约定

### `vite.config.js`

```javascript
server: {
  host: '0.0.0.0',
  port: 5173,
  proxy: {
    '/api': {
      target: 'http://localhost:8000',
      changeOrigin: true,
      timeout: 600_000,
      proxyTimeout: 600_000,
    },
  },
},
```

### `App.jsx` 路由模式

- 外层 `BrowserRouter` + 全局 `Provider`
- 布局路由：`<Route element={<MainLayout />}>` 包裹业务子路由
- 未知路径 `<Navigate to="/" replace />`

### `api/*.js` 模式

- 统一 `readError(res)` 解析 FastAPI `detail`
- 列表接口兼容数组与 `{ items, total, page, pageSize }`
- 流式接口（NDJSON/SSE）单独函数，勿与普通 `request` 混用

### 样式约定

- 移动断点常用 **750px**（常量放 `domain/constants.js`）
- 页头 KPI、Panel 标题行复用同一 CSS 类，避免单页覆盖

## 初始化命令（代理执行）

```bash
cd frontend
npm install
npm run dev    # http://localhost:5173
npm run build
npm test       # vitest
```

## 反模式

- 在 `pages` 里散落裸 `fetch`（应经 `api/`）
- 全局 Context 塞满所有域状态（按域拆分 loader / 局部 state）
- 默认 30s 代理超时跑流式或长任务 API
- 起步就复制整套业务页（审核、规则等）到新项目
- `components` 与 `pages` 同名业务组件混放无边界

## 项目来源

QTP 文档审查前端，2026 年验证。
