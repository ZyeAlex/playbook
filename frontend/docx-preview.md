# docx-preview（浏览器 Word 预览）

## 适用场景

在 Web 内预览 `.docx` 源文件，并在渲染后的 HTML 上做证据高亮（配合同目录 `evidence-highlight-dom.md`）。

## 库

npm **`docx-preview`**（当前 0.3.x），动态 `import()` 减小首包。

## 推荐 renderAsync 参数

```javascript
renderAsync(blob, bodyEl, styleEl, {
  className: 'docx',
  inWrapper: true,
  ignoreWidth: false,
  ignoreHeight: false,
  breakPages: true,        // 产出 section.docx 分页
  renderHeaders: true,
  renderFooters: true,
  renderFootnotes: true,
  renderEndnotes: true,
})
```

- **双容器**：`body` 放 DOM，`style` 放库生成的样式；不要混用一个 div。
- 切换文档/关闭弹窗：`AbortController` 中断 fetch + 清空 `innerHTML`，避免竞态。

## 推荐布局参数

| 常量 | 建议值 | 作用 |
|------|--------|------|
| 回退缩放 | 0.85 | 测不到页宽时 |
| 最小缩放 | 0.5 | |
| 最大缩放 | 1.5 | 宽栏可放大铺满 |
| CSS 变量 | `--docx-source-scale` | 挂在 wrapper，`zoom` 应用 |

流程：渲染完成 → **统一页宽** → **按容器算缩放** → `ResizeObserver` 监听容器变化重算。

## 自动编号段落（docx-num-*）

Word 自动编号在 docx-preview 中常渲染为：

```html
<p class="docx-num-20-0"><span>1</span>正文…</p>
```

库注入 `p.docx-num-*::before { content: counter(...); }` 与 span 内字面序号**重复显示**（如表 7-1 序号列 **11、22**）。

**当前解法**（作用域 `.docx-source-body`，2026-06-08）— **隐藏伪元素序号**：

```css
.docx-source-body p[class*='docx-num-']::before {
  content: none !important;
  display: none !important;
}
```

保留 span / 正文内已有数字。若仍双显，可追加隐藏首 span（按文档抽样）：

```css
.docx-source-body p[class*='docx-num-'] > span:first-child {
  display: none;
}
```

**勿**全局隐藏所有 `p > span:first-child`。

## 踩坑与解法

### 宽表右侧被裁切

**原因**：Word 多节不同 `pageSize` → 各页 HTML 宽度不一 → 窄页 `overflow:hidden` 裁宽表。  
**解法**：取文档内**最大页宽**，所有 `section.docx` 统一 `width` / `minWidth`。

### 预览区留白或横向溢出

**解法**：`可用宽度 / 页宽` 得缩放比，clamp 到 [min, max]；外层 `overflow-x: auto` 允许宽表横滚。

### 与后端块 ID 不对齐

docx-preview **不会**输出 `data-block-id`。高亮必须走文本 / segment 匹配，不能假设 DOM 与解析器块一一对应。

### 表内 `<p>` 与表体 `<table>`

表格单元格内也可能有 `docx-num-*` 段落（序号列）。高亮整表 `<table>` 时，序号双显问题仍靠上文 CSS 修正，与是否加 `docx-source-highlight` 无关。

## 反模式

- 在 iframe 里嵌 Office Online 做内网预览（依赖外网、难高亮）
- 单容器同时塞 body+style（样式污染、难清理）
- 全局隐藏所有 `p > span:first-child`（会误伤非编号段落）
- 用 JS 后处理改 docx-preview 注入的 style 标签（升级库易丢）

## 项目来源

文档合规审查类 Web 产品，2026 年验证；表 7-1 序号双显 2026-06-08。
