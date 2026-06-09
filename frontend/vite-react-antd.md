# Vite + React + Ant Design

## 适用场景

中后台 SPA，REST + SSE，本地 dev 代理 API；有 HTML 设计稿时需与 antd 组件共存。

## 推荐版本组合（曾验证）

Vite 5/6、React 18/19、Ant Design 5/6、React Router；`ConfigProvider` + 中文 locale + 独立 `theme/*.js` token 文件。

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

## 与设计稿共存的推荐分层

| 层 | 职责 |
|----|------|
| `styles/tokens.css` | OKLCH / CSS 变量，设计稿唯一色源 |
| `styles/layout.css`、`components.css` | 设计稿原生类（侧栏、页头、图表等） |
| `theme/antd.js` | `ConfigProvider` token：品牌色、圆角、字体 |
| `styles/antd.css` | antd 选择器覆盖（表格、分页、渐变按钮等） |
| `components/ui/*` | 薄封装：统一 props，内部用 antd |

**原则**：有精确 HTML 原型的区块（侧栏、图表、KPI）优先用设计稿 CSS 类；列表/表单/弹窗等重复劳动用 antd + 覆盖。

## 侧栏导航

**不要用 antd `Menu` 硬改图标容器**（32×32 圆角底、15px 图标、渐变选中态）——DOM 结构与伪类难 1:1 还原。

推荐：保留 antd `Layout.Sider` 壳，导航用设计稿结构：

```html
<button class="nav-item active">
  <div class="nav-icon"><!-- 15px svg --></div>
  <span class="nav-text">…</span>
  <span class="nav-badge">3</span>
</button>
```

路由高亮用 `useLocation` + `navKeyFromPath`，不用 Menu `selectedKeys`。

## 全高可滚动表格

列表页常见需求：表格占满剩余视口、表体滚动、分页贴底。

- 页面根：`page-stack-fill`（flex 列、`min-height: 0`）
- 封装：`ListTablePanel` = flex 列 + antd `Table`（`scroll.y` 动态计算）+ 底部 `Pagination size="small"`
- 全局：`.page-body:has(.page-stack-fill)` 禁止外层滚动

## 图表区高度

双栏 Dashboard 应对齐结构，避免一侧 `height: auto`、一侧固定高：

```text
chart-body
├── chart-plot（固定 116px + border-bottom）← SVG 或柱图
└── chart-axis-row（margin-top: 6px）       ← 轴标签
```

**踩坑**：子元素 `.bar-chart { height: 100% }` 写在 `.chart-plot { height: 116px }` 之后会覆盖固定高度，柱图塌成一条线。固定高度只写在 `.chart-plot` 上；柱段用 `(value / max) * 116` 像素高度。

## 长任务详情页

- 列表接口常为轻量快照 → 详情页**单独请求全量**（含子结果数组）
- 全局 Context 封装 `loadXxx(id)`，避免路由切换仍用旧快照

## UI 约定

- 移动断点常用 750px
- 弹窗：antd Modal 或项目 Modal 壳 + Portal
- 页头 KPI / 图表：多页共用同一 CSS 类，避免单页 inline 覆盖导致高度不一致

## 踩坑与解法

| 现象 | 解法 |
|------|------|
| Menu 图标无外框 / 尺寸不对 | 改用原生 `nav-item` + `nav-icon` |
| antd 表格与 design 间距不符 | `antd.css` 按 `.ace-table` 等作用域覆盖 |
| 侧栏角标样式被 Badge 默认盖掉 | 设计稿角标用 `nav-badge` 类，不必 antd Badge |
| 页面双滚动条 | `page-body` + `page-stack-fill` + `min-height: 0` 链 |
| ConfigProvider token 不够细 | 组件级样式放 `antd.css`，token 只管色/圆角/字号 |

## 反模式

- 默认 30s 代理超时跑流式 API
- 详情依赖列表项内嵌的 incomplete payload
- 全站 raw antd 默认主题直接上线，无 token / 无覆盖层
- 设计稿已有精确 CSS 仍强行用 Menu / Tabs 默认皮肤

## 项目来源

企业文档审查前端（2026）；机载元器件智析 UI 对齐（2026-06）验证侧栏、表格、图表分层。
