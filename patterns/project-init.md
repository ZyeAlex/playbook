# 初始化项目

> **触发语**：`阅读 playbook/，初始化项目`（可加：**初始化后端结构** / **初始化前端结构** / **初始化前后端结构**）  
> **产出**：`memory/` + 根目录 `AGENTS.md` + `.cursor/rules/` + `.cursor/skills/`  
> **可选产出**：`backend/` 与/或 `frontend/` 代码骨架 — 见 §3 Step 5  
> **前提**：业务仓库已放入 `playbook/`（独立 git 子目录或 submodule）；已有 PRD/原型/设计说明等文档来源。

把 playbook 拷进新项目后，用上面一句话即可让代理按本文生成**整套 AI 协作基线**。  
原「只建 memory」流程已合并进本文 §3；`memory-bootstrap.md` 保留为别名入口。

---

## 1. 产出物一览

```
{项目根}/
├── .gitignore                         # 排除 memory/playbook/.cursor/docs
├── AGENTS.md                          # 薄层：命令、导航、边界（跨工具可读）
├── memory/
│   ├── README.md                      # L0 入口 + 渐进式披露 + source_of_truth
│   ├── requirements/                  # L1 需求与业务事实（原子文件）
│   │   ├── README.md
│   │   ├── product.md、workflow.md …
│   ├── structure/                     # L2 项目结构（前后端、存储、API）
│   │   ├── README.md
│   │   ├── repo-root.md、backend.md、frontend.md …
│   └── progress/                      # L3 开发进度（阶段 + 原子里程碑）
│       ├── README.md
│       ├── phase-*-snapshot.md、m0-*.md … backlog-*.md
├── .cursor/
│   ├── rules/
│   │   └── {slug}-agent-nav.mdc       # alwaysApply：查阅顺序 + 维护边界
│   └── skills/
│       └── project-init/
│           └── SKILL.md               # 再次初始化 / 补全时可触发
└── playbook/                          # 已有，勿复制内容进 memory
```

**按需追加原子文件**（在对应文件夹内，勿起步建空壳）：

| 文件夹 | 常见原子文件 |
|--------|--------------|
| `requirements/` | `product.md`、`workflow.md`、`json-output.md`、`ui-pages.md`、`business-rules.md`、`acceptance.md`、`gaps.md` |
| `structure/` | `repo-root.md`、`backend.md`、`frontend.md`、`data-storage.md` |
| `progress/` | `phase-*-snapshot.md`、`m0-*.md` … `mN-*.md`、`backlog-*.md` |

**旧版迁移**：已有 `sources.md` / `current.md` 的项目，拆入上述三文件夹后删除旧文件，并更新 AGENTS / Rules 链接。

**可选**：若项目属于某领域模式（如规则审文档 RAG），额外生成领域 Skill + 领域 Rule — 见 §6。

**可选**：若用户要求初始化代码结构，额外生成 `backend/` 与/或 `frontend/` 骨架 — 见 §3 Step 5。

---

## 2. 设计原则

| 原则 | 说明 |
|------|------|
| **README 入口 + 三文件夹** | 起步 `README` + `requirements/` / `structure/` / `progress/`，每文件夹内 README 索引 + 原子 md |
| **一份快照** | 每文件只保留**当前**说法，覆盖旧内容，不堆「第 N 轮」 |
| **摘录不转载** | 从 PRD/原型**摘**表、字段、流程，不贴全文 |
| **memory vs playbook** | memory = 本项目事实与进度；工具参数/踩坑 = playbook |
| **薄层 AGENTS.md** | 50～120 行：命令 + 导航表 + 边界；细节链到 memory/playbook |
| **Rules 薄、Skills 链 playbook** | Rule 写硬约束与导航；Skill 写触发场景 + 指向 playbook 原子文件 |
| **查阅顺序 > manifest** | README 写清「先读哪」；manifest.json 项目变大再加 |
| **Git 分层忽略** | 根 `.gitignore` 排除 AI/docs；`backend/`、`frontend/` 各自 `.gitignore` |

---

## 3. 代理执行步骤

### Step 0 — 读 playbook 与设计来源

1. 读 `playbook/README.md` 了解分桶。
2. 读用户提供的 PRD、原型、README、现有代码结构（若有）。
3. 确认项目名 `{name}`、slug `{slug}`（小写短横线，如 `qtp`、`invoice-bot`）。

### Step 1 — 创建 `memory/`

#### `memory/README.md`（L0 入口）

```markdown
# {项目名} Memory

最后更新：{YYYY-MM-DD}

跨工具入口 → 根目录 **[`AGENTS.md`](../AGENTS.md)**。  
工具配方 → **[`playbook/`](../playbook/README.md)**（写代码优先读 playbook，再回 memory 看本项目差异）。

## 用途

本项目**单一事实基线**：需求、结构、进度。每文件只保留**当前快照**，覆盖更新，不堆历史轮次。

## 渐进式披露（查阅顺序）

| 层级 | 何时读 | 路径 |
|------|--------|------|
| L0 | 新任务 / 不知道读哪 | 本文 |
| L1 | 做什么、规则、验收 | [`requirements/README.md`](requirements/README.md) |
| L2 | 改代码、找目录/API | [`structure/README.md`](structure/README.md) |
| L3 | 阶段、里程碑、待办 | [`progress/README.md`](progress/README.md) |
| L4 | 工具参数、踩坑 | **`playbook/README.md`（不进 memory）** |

## 目录索引

\`\`\`
memory/
├── README.md
├── requirements/
├── structure/
└── progress/
\`\`\`

## 外部文档来源（source_of_truth）

- {PRD 路径}
- {原型路径}
- …

## 维护约定

| 变化 | 改哪里 |
|------|--------|
| 需求 / 业务规则 | `requirements/` 对应原子文件 |
| 目录 / API / 模块 | `structure/` 对应原子文件 |
| 阶段 / 里程碑 / 待办 | `progress/` 对应原子文件 |
| 工具踩坑 | playbook（**不进 memory**） |
```

#### `memory/requirements/`（L1）

`README.md` 为索引表；从设计文档拆成**原子文件**（只写稳定事实；猜想标「待确认」）：

| 文件 | 典型内容 |
|------|----------|
| `product.md` | 产品目标、阶段划分 |
| `workflow.md` | 核心业务流程、数据契约 |
| `json-output.md` / `contracts.md` | 输出格式、API 契约 |
| `ui-pages.md` | 页面与导航 |
| `business-rules.md` | 业务硬约束、判定规则 |
| `acceptance.md` | 验收口径（可测的说法） |
| `gaps.md` | 已知文档缺口、原型 vs PRD 冲突以谁为准 |

**禁止**写入：框架教程、向量 top-k、组件 API — 那些进 playbook。

#### `memory/structure/`（L2）

`README.md` 为索引表；记录**当前代码库布局**（随实现覆盖更新）：

| 文件 | 典型内容 |
|------|----------|
| `repo-root.md` | 仓库顶层目录 |
| `backend.md` | 后端分层、启动命令、HTTP API 表 |
| `frontend.md` | 前端目录、API 层、代理 |
| `data-storage.md` | 数据库、文件、缓存路径 |

后端/前端细节**优先放 structure/**，不在业务仓库 `backend/README.md` 重复维护（可选保留极简启动说明）。

#### `memory/progress/`（L3）

`README.md` 为索引表 + 模块状态一览；进度**按里程碑原子化**：

| 文件 | 典型内容 |
|------|----------|
| `phase-*-snapshot.md` | 当前阶段总览 |
| `m0-*.md` … `mN-*.md` | 单个里程碑：目标、交付、验收、缺口 |
| `backlog-*.md` | 下一阶段待办 |

单里程碑模板：

```markdown
# M{N} — {名称}

最后更新：{YYYY-MM-DD}
**状态**：{未开始 / 进行中 / 已完成}

## 目标
…

## 交付
- [ ] …

## 验收 / 缺口
…
```

### Step 1b — 根目录 `.gitignore`

与 Step 1 同步创建（若已存在则**补全缺失项**，勿删用户自定义规则）。

业务 git **只跟踪代码与 AGENTS.md**；AI 协作文档、设计源、playbook 副本不进仓：

```gitignore
# AI 协作文档与配方（本地或独立仓库维护，不进业务 git）
memory/
playbook/
.cursor/
docs/

# OS
.DS_Store
```

> `backend/`、`frontend/` 的运行时忽略（`node_modules/`、`.venv/`、`data/uploads/` 等）写在各自子目录 `.gitignore`，见 Step 5。

### Step 2 — 创建根目录 `AGENTS.md`

按项目实际情况填占位符；保持 **50～120 行**。

```markdown
# {项目名} — Agent 指南

> 薄层导航：操作事实与边界在此；项目事实见 [`memory/`](memory/README.md)；跨项目配方见 [`playbook/`](playbook/README.md)。

最后更新：{YYYY-MM-DD}

## 项目概览

{一两段：做什么、技术栈、设计源路径}

## 常用命令

### {后端/主服务}

\`\`\`bash
{可复制的启动、测试、lint 命令}
\`\`\`

### {前端若有}

\`\`\`bash
…
\`\`\`

## 文档查阅顺序

| 优先级 | 何时读 | 路径 |
|--------|--------|------|
| 1 | 新任务 | [`memory/README.md`](memory/README.md)（L0 入口） |
| 2 | 需求 / 业务规则 | [`memory/requirements/`](memory/requirements/README.md) |
| 3 | 改代码 / 找目录 API | [`memory/structure/`](memory/structure/README.md) |
| 4 | 阶段 / 里程碑 | [`memory/progress/`](memory/progress/README.md) |
| 5 | **写代码 / 工具参数** | **`playbook/README.md`（优先）** |
| 6 | {本项目设计源} | `{docs/…}` |

**memory vs playbook**：memory 只记本项目；工具经验写 playbook，勿堆进 memory。

## 当前阶段（{日期}）

{一句话 + 链到 `memory/progress/phase-*-snapshot.md`}

## 硬约束

{仅本项目业务规则，3～7 条}

## 边界

### Always

- 改实现前：先读 playbook 对应原子文件，再查 memory 现状。
- 更新 memory：**覆盖**快照，不堆历史轮次。

### Ask first

- git commit / push / 开 PR（除非用户明确要求）。

### Never

- 在 memory 里重复 playbook 工具参数。
- {项目特有禁止项}

## Cursor 扩展

| 资产 | 路径 |
|------|------|
| 始终生效 | `.cursor/rules/{slug}-agent-nav.mdc` |
| Skills | `.cursor/skills/project-init/` |
```

### Step 3 — 创建 `.cursor/rules/{slug}-agent-nav.mdc`

```markdown
---
description: {项目名} 代理导航 — memory/playbook 查阅顺序与维护边界
alwaysApply: true
---

# {项目名} Agent 导航

## 查阅顺序

1. 新任务 → `memory/README.md`（L0 入口）
2. 需求 / 业务规则 → `memory/requirements/README.md`
3. 改代码 / 找目录 API → `memory/structure/README.md`
4. 阶段 / 里程碑 → `memory/progress/README.md`
5. **写代码 / 改实现** → **`playbook/README.md` 优先**，再读 memory 本项目现状
6. {按项目补充，链到 memory 子文件或 docs}

完整命令与边界见根目录 **`AGENTS.md`**。

## memory vs playbook

- **memory**：本项目需求、阶段、契约；更新时**覆盖**旧说法。
- **playbook**：跨项目工具配方；项目结束按 `playbook/patterns/playbook-contribute.md` 合并。

## 维护边界

- 未明确要求 → 不要 git commit / push / 开 PR。
- 阶段 / 里程碑 → 改 `memory/progress/` 对应原子文件。
- 需求 / 业务规则 → 改 `memory/requirements/` 对应原子文件。
- 目录 / API → 改 `memory/structure/` 对应原子文件。
```

### Step 4 — 创建 `.cursor/skills/project-init/SKILL.md`

```markdown
---
name: project-init
description: >-
  Initialize or repair project AI baseline (memory/, AGENTS.md, Cursor rules/skills).
  Use when user says 初始化项目, bootstrap memory, or setup AGENTS.md for a new repo
  with playbook/.
---

# Project Init

完整流程见 **`playbook/patterns/project-init.md`**。

## 何时触发

- 用户说「阅读 playbook/，初始化项目」
- memory / AGENTS.md 缺失或需按 playbook 规范重建
- 新项目已放入 playbook/，尚未建立 AI 协作基线

## 执行要点

1. 读设计来源 → 建 memory 分文件夹结构（README + requirements/structure/progress）
2. 建根目录 `.gitignore`（排除 memory/playbook/.cursor/docs）— 见 project-init §3 Step 1b
3. 建根目录 AGENTS.md（薄层：命令 + 导航 + 边界）
4. 建 `.cursor/rules/{slug}-agent-nav.mdc`（alwaysApply）
5. 若有明确领域（如 RAG 审文档）→ 按 project-init §6 加领域 Skill/Rule
6. 若用户要求 → 按 project-init §3 Step 5 + `backend/project-structure.md` / `frontend/project-structure.md` 搭代码骨架（**含各自 `.gitignore`**）
7. 勿把工具参数写进 memory；勿建空壳目录

## 维护

- 阶段 / 里程碑 → 改 `memory/progress/` 对应原子文件
- 需求变更 → 改 `memory/requirements/` 对应原子文件
- 结构 / API 变化 → 改 `memory/structure/` 对应原子文件
- 工具踩坑 → `playbook/patterns/playbook-contribute.md`
```

### Step 5 — 可选：初始化代码结构

用户指令含以下**任一**表述时执行（可组合）：

| 用户表述 | 执行 |
|----------|------|
| 初始化后端结构 | 仅 Step 5.1 |
| 初始化前端结构 | 仅 Step 5.2 |
| 初始化前后端结构 | Step 5.1 + Step 5.2 |

**权威模板**（勿凭记忆编造目录）：

- 后端 → [`playbook/backend/project-structure.md`](../backend/project-structure.md)
- 前端 → [`playbook/frontend/project-structure.md`](../frontend/project-structure.md)

#### 5.1 后端骨架

1. 若已有 `backend/` 且含 `src/main.py`，**不覆盖**业务文件；仅补缺失的骨架文件并汇报缺口。
2. 按 project-structure 创建目录树与最小可运行文件：
   - `pyproject.toml`、`.env.example`、**`backend/.gitignore`**（见下方模板）
   - 后端结构说明 → `memory/structure/backend.md`（可选保留极简 `README.md` 仅写启动命令）
   - `src/main.py`、`src/api/router.py`、`src/api/deps.py`、`src/api/endpoints/health.py`
   - `src/core/settings.py`、`src/core/database.py`
   - `src/models/base.py`、空 `schemas/` / `repositories/` / `services/` / `agents/`
   - `tests/test_health.py`、`data/.gitkeep`、`scripts/.gitkeep`
3. 占位符替换：`{项目名}`、`{slug}`（数据库文件名等）。
4. `AGENTS.md` 的「常用命令」节补充后端启动与 `pytest`（若 Step 2 已写则更新）。
5. 执行 `uv sync` + `uv run pytest`（或说明用户需本地执行）。

**`backend/.gitignore` 模板**：

```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
*.egg
*.egg-info/
.eggs/
dist/
build/
pip-wheel-metadata/

# Virtual env
.venv/
venv/
ENV/

# Env & secrets
.env
.env.*
!.env.example

# Test & lint
.pytest_cache/
.mypy_cache/
.ruff_cache/
.coverage
htmlcov/
.tox/
.nox/

# Runtime data（保留 data/**/categories.json 等种子文件）
data/*.db
data/*.db-journal
data/uploads/
data/ocr/
data/models/

# uv（若使用）
.uv/

# Editor & OS
.DS_Store
*.swp
*.swo
.idea/
.vscode/*
!.vscode/extensions.json
```

#### 5.2 前端骨架

1. 若已有 `frontend/` 且含 `src/App.jsx`，**不覆盖**业务文件；仅补缺失的骨架文件。
2. 按 project-structure 创建：
   - `package.json`、`vite.config.js`、`index.html`、**`frontend/.gitignore`**（见下方模板）
   - `src/main.jsx`、`src/App.jsx`、`src/index.css`、`src/polyfills.js`（空或最小）
   - `src/components/MainLayout.jsx` 及通用壳组件占位
   - `src/context/AppStateContext.jsx`（最小 Provider）
   - `src/pages/DashboardPage.jsx`、`src/pages/SettingsPage.jsx`
   - `src/api/health.js`、`src/domain/constants.js`、`src/utils/confirm.js`
3. `AGENTS.md` 补充 `npm install` / `npm run dev`。
4. 执行 `npm install` + `npm run build`（或说明用户需本地执行）。

**`frontend/.gitignore` 模板**：

```gitignore
# Dependencies
node_modules/

# Build
dist/
dist-ssr/
*.local

# Vite
.vite/

# Env
.env
.env.*
!.env.example

# Logs
logs/
*.log
npm-debug.log*
pnpm-debug.log*
yarn-debug.log*
yarn-error.log*

# Test / coverage
coverage/

# Editor & OS
.DS_Store
*.swp
*.swo
.idea/
.vscode/*
!.vscode/extensions.json

# TypeScript (if added later)
*.tsbuildinfo
```

#### 5.3 结构与 AI 基线关系

- **只初始化项目**（默认）：仅 Step 1～4，**不**创建 `backend/` / `frontend/`。
- **初始化项目 + 前后端结构**：先完成 Step 1～4，再 Step 5；memory / AGENTS 中技术栈与命令须与骨架一致。
- 业务域文件（审核、规则提取等）**不在**结构初始化时批量生成 — 按 PRD 后续迭代。

### Step 6 — 向用户汇报

列出已创建文件路径，并说明：

- 日常开发：代理先读 `AGENTS.md` → memory → playbook
- 写后端/前端：先读 `playbook/backend/project-structure.md` 或 `playbook/frontend/project-structure.md`
- 项目结束：按 `playbook-contribute.md` 把工具经验合并回 playbook 仓库

---

## 4. 给用户的一键指令

复制到 Cursor（替换占位符）：

```text
请阅读 playbook/，按 playbook/patterns/project-init.md 初始化项目。

文档来源：
- {PRD 路径}
- {原型路径}
- …

项目名：{名称}
slug：{小写短横线，如 my-app}

请生成：
1. memory/README.md + requirements/ + structure/ + progress/（各含 README 与必要原子文件）
2. 根目录 `.gitignore`（排除 memory/、playbook/、.cursor/、docs/）
3. 根目录 AGENTS.md
4. .cursor/rules/{slug}-agent-nav.mdc
5. .cursor/skills/project-init/SKILL.md

要求：
- 从文档摘录 requirements/ 原子文件，勿编造
- progress/ 按里程碑原子化，只写当前阶段快照
- structure/ 反映实际或计划中的代码布局
- AGENTS.md 保持薄层（命令 + 导航 + 边界）
- 工具经验不写进 memory
- 完成后列出创建的文件清单
```

**带代码结构**（三选一或组合，追加到指令末尾）：

```text
并初始化后端结构
```

```text
并初始化前端结构
```

```text
并初始化前后端结构

结构须严格遵循：
- playbook/backend/project-structure.md
- playbook/frontend/project-structure.md

并创建 backend/.gitignore 与 frontend/.gitignore（模板见 project-init §3 Step 5）。
```

---

## 5. 维护（项目进行中）

| 变化 | 改哪里 |
|------|--------|
| 阶段 / 里程碑 / 待办 | `memory/progress/` 对应原子文件（覆盖） |
| 需求 / 业务规则 | `memory/requirements/` 对应原子文件 + README 日期 |
| 目录 / API / 模块 | `memory/structure/` 对应原子文件 |
| 命令 / 边界调整 | `AGENTS.md` + `{slug}-agent-nav.mdc` |
| Git 忽略规则 | 根 `.gitignore`（AI/docs）；`backend/`、`frontend/` 各自 `.gitignore` |
| 工具踩坑 | playbook 对应原子文件（**不进 memory**） |
| 某文件夹膨胀 | 在该文件夹内继续拆原子文件；更新文件夹 README 索引表 |
| 项目结束 | `playbook/patterns/playbook-contribute.md` |

---

## 6. 可选：领域 Skill / Rule

项目若匹配 playbook 某 **patterns/** 领域，**额外**生成（薄层，链 playbook）：

| 领域 | playbook 模式 | 生成 Skill | 生成 Rule（globs 按实际路径） |
|------|---------------|------------|-------------------------------|
| 规则审文档 RAG | `rag-document-review.md` | `.cursor/skills/rag-document-review/` | `{slug}-domain-constraints.mdc` |
| 仅 Excel 规则提取 | `backend/excel-rule-extract.md` | 可选 | 可选 |

**领域 Rule 模板**（示例：RAG 审文档）：

```markdown
---
description: {项目名} {领域}硬约束
globs: {backend 审核/RAG 相关 glob}
alwaysApply: false
---

# {领域}硬约束

{从 playbook/patterns/xxx.md 摘 3～7 条 MUST，链到 playbook 全文}

权威说明：playbook/patterns/{pattern}.md；本项目现状：`memory/requirements/business-rules.md` + `AGENTS.md` 硬约束节。
```

**不要**在初始化时批量生成所有 playbook 领域 Skill — 只加**与本项目相关**的。

---

## 7. 反模式

- 起步就建 `00-index/`～`40-execution/` 编号体系或大量空 md
- 在 requirements/structure/progress 外再平行维护 `sources.md` / `current.md`（应迁入三文件夹）
- 初始化结构时覆盖已有业务代码（应只补骨架、汇报已有文件）
- 把 QTP 的 AGENTS.md / memory 结构原样复制到新项目
- 不读 project-structure.md 凭印象搭目录（层名、调用方向须与 playbook 一致）
- memory 里写向量 top-k、组件 API、框架教程
- AGENTS.md 超过 150 行（应拆链到 memory）
- 只建 memory 不建 AGENTS.md / Rules（代理缺少跨工具入口）
- 在业务仓库里改 playbook 却不 push 独立 playbook 仓库

---

## 8. 与 AGENTS.md 开放标准的关系

- **AGENTS.md**（根目录）= 跨 Cursor / Copilot / Claude Code 等工具的**项目入口**
- **memory/** = AGENTS.md 的**深层链接**（L0 README → L1/L2/L3 渐进式披露）
- **playbook/** = 跨项目的**工具配方**，不进单一项目 AGENTS.md 正文
- **.cursor/rules/** = Cursor 特有：alwaysApply 导航 + 按 glob 的硬约束
- **.cursor/skills/** = Cursor 特有：按场景触发，正文链 playbook

参考：[agents.md](https://agents.md/) 开放格式；本项目在 AGENTS.md 之上增加了 memory 分层与 playbook 链。
