# docx-preview（浏览器 Word 预览）

## 适用场景

在 Web 内预览 `.docx` 源文件，并在渲染后的 HTML 上做证据高亮（配合同目录 `evidence-highlight-dom.md`）。

## 库

npm **`docx-preview`**，动态 `import()` 减小首包。

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
| CSS 变量 | `--docx-preview-scale` | 挂在 wrapper，`zoom` 应用 |

流程：渲染完成 → **统一页宽** → **按容器算缩放** → `ResizeObserver` 监听容器变化重算。

## 踩坑与解法

### 宽表右侧被裁切

**原因**：Word 多节不同 `pageSize` → 各页 HTML 宽度不一 → 窄页 `overflow:hidden` 裁宽表。  
**解法**：取文档内**最大页宽**，所有 `section.docx` 统一 `width` / `minWidth`。

### 预览区留白或横向溢出

**解法**：`可用宽度 / 页宽` 得缩放比，clamp 到 [min, max]；外层 `overflow-x: auto` 允许宽表横滚。

### 与后端块 ID 不对齐

docx-preview **不会**输出 `data-block-id`。高亮必须走文本匹配策略，不能假设 DOM 与解析器块一一对应。

## 反模式

- 在 iframe 里嵌 Office Online 做内网预览（依赖外网、难高亮）
- 单容器同时塞 body+style（样式污染、难清理）

## 项目来源

文档合规审查类 Web 产品，2026 年验证。
