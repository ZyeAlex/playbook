# 证据高亮（docx-preview DOM 文本匹配）

## 适用场景

后端返回 `evidences[]`（含 `text`、`blockType`、`sourceBlockIds`），在前端已渲染的 docx HTML 上定位并高亮，支持多处跳转。

## 策略分派

| blockType | 策略 |
|-----------|------|
| paragraph | 匹配 `<p>` 文本 |
| table | 先「表 N-M」表题，再 `<table>` / `<tr>` |
| table_row | 匹配 `<tr>` 或 `<td>` 所在行 |

## 段落

1. 从 `evidence.text` 生成候选片段（去噪、分行）
2. **`findSmallestMatch`**：在容器内找**最短可匹配**片段，降低误匹配整章概率
3. **`used` Set**：同规则多条证据不抢同一 DOM 节点

### Parent 多段（`sourceBlockIds.length > 1`）

- 按 **sourceBlockIds 文档顺序** 找锚点段落
- 从锚点 **`expandParagraphRun`** 向下扩展连续 `<p>`，实现整段 parent 高亮
- **不要**用列表符号（如 `b)`）向前扩展——易错位到上一列表项

### 同块多角色列表（a/b/c）

多行且语义角色不同时：按行分别匹配，禁用「最长片段优先」。

## 表格

1. 优先：`表13-1` 类 caption 文本 → 其后第一个 `<table>`
2. 回退：表体单元格文本匹配 `<tr>`
3. 过滤纯表头候选，避免只亮表头行

## 多处跳转 UI

- 每组命中存 `{ elements[], evidence }`
- 普通高亮 class + `active` class 区分当前处
- `scrollIntoView({ block: 'center', behavior: 'smooth' })`

## 踩坑

| 现象 | 处理 |
|------|------|
| 0 命中 | 空白/标点归一化；`quote` 作补充片段；确认 render 完成后再 highlight |
| 只亮一行 | 检查是否下发 `sourceBlockIds`、是否走 parent 扩展 |
| 高亮偏移 | 检查是否错误扩展列表项 |

## 反模式

- 假设 docx-preview 节点带 blockId 属性
- 全局 `innerHTML` 搜索最长子串（误匹配率高）

## 项目来源

多证据溯源预览，2026 年验证。
