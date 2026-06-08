# Excel 规则流式提取

## 适用场景

检查单 xlsx → LLM 抽规则 → 人工勾选入库。

## 流程

```
上传 → 选场景/标签 → NDJSON 流式 extract → 预览勾选 → publish 入库
```

## 分块单位

按 Excel **逻辑行**送 Agent，不是 Word 块。

## 溯源契约（硬）

| 字段 | 规则 |
|------|------|
| sheet / row / cal | 全链路溯源三字段 |
| row | 仅 `N` 或 `N-M` |
| 展示用 location | **仅前端拼接**，不进 API |

标题行、章节行不入库；不在 service 层事后改 row。

## 去重

`源文件ID + sheet + row` 唯一。

## 源文件

内容哈希去重存储；提供 sheet-view API（merges + 高亮行）供溯源 UI。

## 反模式

- 后端拼 `location` 字符串入库
- 离散 row 列表（`1,3,5`）——不利于 Excel 切片

## 项目来源

检查单规则提取，2026 年验证。
