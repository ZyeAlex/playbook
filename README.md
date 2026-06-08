# Playbook

> **定位**：跨项目累积的**工具级经验库**。每个项目结束后把可复用做法**合并进对应原子文件**，供下一项目按需查阅。  
> **不是**：某一项目的目录说明、需求文档或排期记录。

## 新项目一键初始化

把 `playbook/` 放进业务仓库后，执行：

```text
阅读 playbook/，初始化项目
```

代理将按 [`patterns/project-init.md`](./patterns/project-init.md) 生成 **`docs/` + `memory/` + `AGENTS.md`**。  
（`docs/` 放 PRD/原型等设计文档；可附带文档路径与项目名；可加 **初始化后端结构** / **初始化前端结构** / **初始化前后端结构**；详见 project-init §4。）

## 目录分桶

```
playbook/
├── README.md
├── patterns/     # 跨工具的领域模式
├── frontend/     # 浏览器预览、高亮、SSE UI
├── docs/         # 文档解析与切片
├── rag/          # 向量、重排、漏斗、Judge
├── backend/      # 服务基建、存储、配置
├── ops/          # 部署与运维
└── testing/      # 验收与回归
```

## 使用方式（渐进式披露）

| 层级 | 何时读 | 路径 |
|------|--------|------|
| L0 | **新项目：初始化项目**（docs + memory + AGENTS.md；可选前后端骨架） | [`patterns/project-init.md`](./patterns/project-init.md) |
| L0b | 后端 / 前端目录分层（初始化结构时必读） | [`backend/project-structure.md`](./backend/project-structure.md)、[`frontend/project-structure.md`](./frontend/project-structure.md) |
| L1 | docx 预览 + 原文高亮（segments / Context Unit 合并） | `frontend/docx-preview.md` → `frontend/evidence-highlight-dom.md`；契约 `backend/evidence-locate-segments.md` |
| L2 | 文档审核 RAG 后端 | `docs/` → `rag/` 按序阅读 |
| L3 | Excel 规则提取 | `backend/excel-rule-extract.md` |
| L4 | 模型、热重载、长任务 | `ops/` + `backend/async-job-resume.md` |
| L5 | 验收 | `testing/acceptance.md` |

**原则**：一文件 = 一个工具或一个可复用方案；只写参数、踩坑、决策理由；不写仓库路径。

## 维护协议（每个项目结束时）

**完整步骤** → [`patterns/playbook-contribute.md`](./patterns/playbook-contribute.md)

1. **先找桶再找文件**：在对应分桶下追加/修订，不重复建同主题文件。
2. **写法**：`## 适用场景` → `## 推荐参数` → `## 踩坑与解法` → `## 反模式`。
3. **冲突合并**：保留更普适说法，或并列分场景；删过期条目。
4. **索引同步**：新建文件后更新本文索引表。
5. **提交**：在 playbook 独立仓库里 `commit` + `push`（与业务项目仓库分开）。

## 原子索引

### `patterns/`

| 文件 | 内容 |
|------|------|
| [project-init.md](./patterns/project-init.md) | **初始化项目**（memory + AGENTS.md + Rules/Skills；触发语见文内） |
| [memory-bootstrap.md](./patterns/memory-bootstrap.md) | 别名 → project-init §3（仅 memory 速查） |
| [playbook-contribute.md](./patterns/playbook-contribute.md) | **如何把项目经验提取进 playbook**（分桶、写法、提交） |
| [rag-document-review.md](./patterns/rag-document-review.md) | 规则审文档：架构模式与硬约束 |

### `frontend/`

| 文件 | 工具/方案 |
|------|-----------|
| [project-structure.md](./frontend/project-structure.md) | **Vite + React 目录分层与初始化骨架** |
| [vite-react-antd.md](./frontend/vite-react-antd.md) | Vite + React + Ant Design |
| [docx-preview.md](./frontend/docx-preview.md) | docx-preview 渲染与版面 |
| [evidence-highlight-dom.md](./frontend/evidence-highlight-dom.md) | 证据高亮与多锚点跳转 |
| [pdfjs-highlight.md](./frontend/pdfjs-highlight.md) | pdf.js + bbox 坐标 |
| [sse-long-task-ui.md](./frontend/sse-long-task-ui.md) | SSE 长任务 UI |

### `docs/`

| 文件 | 工具/方案 |
|------|-----------|
| [docx-parser.md](./docs/docx-parser.md) | python-docx 结构化解析 |
| [chunking-parent-child.md](./docs/chunking-parent-child.md) | Parent-Child 切片 |
| [pdf-pymupdf-ocr.md](./docs/pdf-pymupdf-ocr.md) | PyMuPDF + RapidOCR |

### `rag/`

| 文件 | 工具/方案 |
|------|-----------|
| [bge-m3.md](./rag/bge-m3.md) | BGE-M3 hybrid 向量 |
| [bge-reranker.md](./rag/bge-reranker.md) | 交叉编码器重排 |
| [rag-funnel-params.md](./rag/rag-funnel-params.md) | 检索漏斗与证据裁剪 |
| [llm-judge-evidence.md](./rag/llm-judge-evidence.md) | 判定 Agent 少引证据 |

### `backend/`

| 文件 | 工具/方案 |
|------|-----------|
| [project-structure.md](./backend/project-structure.md) | **FastAPI 目录分层与初始化骨架** |
| [evidence-locate-segments.md](./backend/evidence-locate-segments.md) | 审核证据 `locateSegments[]` 落库与块索引 API |
| [async-job-resume.md](./backend/async-job-resume.md) | 长任务与热重载续跑 |
| [sse-event-bus.md](./backend/sse-event-bus.md) | FastAPI SSE 总线 |
| [excel-rule-extract.md](./backend/excel-rule-extract.md) | Excel 流式规则提取 |
| [fastapi-sqlite-files.md](./backend/fastapi-sqlite-files.md) | SQLite + 哈希文件存储 |
| [llm-config-thresholds.md](./backend/llm-config-thresholds.md) | LLM/向量/阈值配置 |

### `ops/`

| 文件 | 内容 |
|------|------|
| [models-offline.md](./ops/models-offline.md) | 向量模型离线与热重载 |

### `testing/`

| 文件 | 内容 |
|------|------|
| [acceptance.md](./testing/acceptance.md) | 个人验收与回归分层 |
