# pdf.js 高亮与后端 bbox

## 适用场景

PDF 源文档预览，叠加检索/判定返回的 bbox 或文本命中。

## 版本

pdf.js **3.11** 曾稳定配合 Vite；升级 major 需单独回归坐标与 worker。

## 坐标系（必核对）

| 来源 | 原点 |
|------|------|
| PyMuPDF (fitz) bbox | **左上角** |
| pdf.js viewport 矩形 | **左下角**（PDF 标准） |

翻转示例（页高 `H`）：

```
pdf_y0 = H - fitz_y1
pdf_y1 = H - fitz_y0
```

## 高亮优先级

1. **有文本层**：文本 search / 包裹 span（更贴字面）
2. **扫描/OCR 页**：绝对定位 div 画框；按**渲染缩放比**换算 bbox

## 与 OCR 配合

页内可提取文本极少（如 &lt;20 字）时走后端 OCR → 带 bbox 块；前端勿按文本层硬搜。

## 踩坑

- 直接用 fitz bbox 不翻转 → 高亮垂直镜像错位
- OCR 框不缩放 → 放大缩小 PDF 后框漂移

## 项目来源

PDF 审核预览，2026 年验证。
