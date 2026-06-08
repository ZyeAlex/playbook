# PyMuPDF + RapidOCR

## 适用场景

PDF 审核：优先文本层，扫描页 fallback OCR。

## 流程

1. `fitz` 逐页抽文本行 + bbox
2. 页内文本极少（如 &lt;20 字）→ `rapidocr-onnxruntime`
3. 输出与 docx 同构块模型（含 `pageFrom`、`bbox`）

## 依赖

- `pymupdf`
- `rapidocr-onnxruntime`（无 API Key，首次下模型）

## 进度

解析回调上报「OCR 第 k/n 页」供 SSE 展示。

## 与前端

bbox 坐标系见 `frontend/pdfjs-highlight.md`。

## 反模式

- 全页 OCR 不判断文本层（慢）
- OCR 框不做缩放换算

## 项目来源

PDF 源文档解析，2026 年验证。
