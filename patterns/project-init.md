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
├── AGENTS.md                          # 薄层：命令、导航、边界（跨工具可读）
├── memory/
│   ├── README.md                      # 入口 + source_of_truth + 查阅顺序
│   ├── sources.md                     # 从设计文档摘录的事实
│   └── current.md                     # 阶段、待办、本项目硬约束
├── .cursor/
│   ├── rules/
│   │   └── {slug}-agent-nav.mdc       # alwaysApply：查阅顺序 + 维护边界
│   └── skills/
│       └── project-init/
│           └── SKILL.md               # 再次初始化 / 补全时可触发
└── playbook/                          # 已有，勿复制内容进 memory
```

**变大后再拆**（按需，勿起步就建）：

```
memory/
├── backlog.md
├── contracts.md
├── 00-index/quick-start.md
└── manifest.json                      # 可选
```

**可选**：若项目属于某领域模式（如规则审文档 RAG），额外生成领域 Skill + 领域 Rule — 见 §6。

**可选**：若用户要求初始化代码结构，额外生成 `backend/` 与/或 `frontend/` 骨架 — 见 §3 Step 5。

---

## 2. 设计原则

| 原则 | 说明 |
|------|------|
| **先小后拆** | 起步 memory 三文件 + AGENTS.md + 1 条 alwaysApply Rule |
| **一份快照** | 每文件只保留**当前**说法，覆盖旧内容，不堆「第 N 轮」 |
| **摘录不转载** | 从 PRD/原型**摘**表、字段、流程，不贴全文 |
| **memory vs playbook** | memory = 本项目事实与进度；工具参数/踩坑 = playbook |
| **薄层 AGENTS.md** | 50～120 行：命令 + 导航表 + 边界；细节链到 memory/playbook |
| **Rules 薄、Skills 链 playbook** | Rule 写硬约束与导航；Skill 写触发场景 + 指向 playbook 原子文件 |
| **查阅顺序 > manifest** | README 写清「先读哪」；manifest.json 项目变大再加 |

---

## 3. 代理执行步骤

### Step 0 — 读 playbook 与设计来源

1. 读 `playbook/README.md` 了解分桶。
2. 读用户提供的 PRD、原型、README、现有代码结构（若有）。
3. 确认项目名 `{name}`、slug `{slug}`（小写短横线，如 `qtp`、`invoice-bot`）。

### Step 1 — 创建 `memory/`

#### `memory/README.md`

```markdown
# {项目名} Memory

最后更新：{YYYY-MM-DD}

跨工具入口 → 根目录 **[`AGENTS.md`](../AGENTS.md)**。

## 用途

沉淀 PRD/原型/实现事实，供研发与联调**单一事实基线**。  
**迭代覆盖**当前快照，勿在同文件堆叠多轮历史。

**跨项目技术配方** → **[`playbook/`](../playbook/README.md)**：写代码时**优先读 playbook**，再回 memory 看本项目差异。

## 文档来源（source_of_truth）

- {PRD 路径}
- {原型路径}
- …

## 查阅顺序

1. 本文
2. `sources.md` — 需求与交互事实
3. `current.md` — 阶段、待办、硬约束
4. 写代码 / 工具踩坑 → **`playbook/README.md`（优先）**

## 文件说明

| 文件 | 内容 |
|------|------|
| sources.md | 从文档来源摘录，不编造 |
| current.md | 当前阶段快照，随进度覆盖更新 |
```

#### `memory/sources.md`

从设计文档整理（只写稳定事实；猜想标「待确认」）：

- 产品目标（一两句）
- 核心流程 / 页面
- 关键字段与状态（表格式）
- 验收口径（可测的说法）
- 原型 vs PRD 冲突时**以谁为准**

#### `memory/current.md`

```markdown
# 当前阶段

最后更新：{YYYY-MM-DD}

## 阶段

- **名称**：{如 MVP / 规则闭环 / …}
- **目标**：{一句话}
- **开始**：{YYYY-MM-DD}

## 已完成

- [ ] …

## 进行中 / 待办

- [ ] TASK-01 …

## 硬约束（仅本项目）

- …

## 已知缺口

- …
```

**禁止**写入：框架教程、向量 top-k、组件 API — 那些进 playbook。

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
| 1 | 新任务 | `memory/README.md` → `sources.md` / `current.md` |
| 2 | **写代码 / 工具参数** | **`playbook/README.md`（优先）** |
| 3 | {本项目最常改的子域} | `memory/…` |

**memory vs playbook**：memory 只记本项目；工具经验写 playbook，勿堆进 memory。

## 当前阶段（{日期}）

| 模块 | 状态 |
|------|------|
| … | … |

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

1. 新任务 → `memory/README.md` → `sources.md` / `current.md`
2. **写代码 / 改实现** → **`playbook/README.md` 优先**，再读 memory 本项目现状
3. {按项目补充第 3、4 条，链到 memory 子文件}

完整命令与边界见根目录 **`AGENTS.md`**。

## memory vs playbook

- **memory**：本项目需求、阶段、契约；更新时**覆盖**旧说法。
- **playbook**：跨项目工具配方；项目结束按 `playbook/patterns/playbook-contribute.md` 合并。

## 维护边界

- 未明确要求 → 不要 git commit / push / 开 PR。
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

1. 读设计来源 → 建 memory 三文件（README / sources / current）
2. 建根目录 AGENTS.md（薄层：命令 + 导航 + 边界）
3. 建 `.cursor/rules/{slug}-agent-nav.mdc`（alwaysApply）
4. 若有明确领域（如 RAG 审文档）→ 按 project-init §6 加领域 Skill/Rule
5. 若用户要求 → 按 project-init §3 Step 5 + `backend/project-structure.md` / `frontend/project-structure.md` 搭代码骨架
6. 勿把工具参数写进 memory；勿建空壳目录

## 维护

- 阶段变化 → 改 `memory/current.md`
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
   - `pyproject.toml`、`.env.example`、`.gitignore`、`README.md`
   - `src/main.py`、`src/api/router.py`、`src/api/deps.py`、`src/api/endpoints/health.py`
   - `src/core/settings.py`、`src/core/database.py`
   - `src/models/base.py`、空 `schemas/` / `repositories/` / `services/` / `agents/`
   - `tests/test_health.py`、`data/.gitkeep`、`scripts/.gitkeep`
3. 占位符替换：`{项目名}`、`{slug}`（数据库文件名等）。
4. `AGENTS.md` 的「常用命令」节补充后端启动与 `pytest`（若 Step 2 已写则更新）。
5. 执行 `uv sync` + `uv run pytest`（或说明用户需本地执行）。

#### 5.2 前端骨架

1. 若已有 `frontend/` 且含 `src/App.jsx`，**不覆盖**业务文件；仅补缺失的骨架文件。
2. 按 project-structure 创建：
   - `package.json`、`vite.config.js`、`index.html`
   - `src/main.jsx`、`src/App.jsx`、`src/index.css`、`src/polyfills.js`（空或最小）
   - `src/components/MainLayout.jsx` 及通用壳组件占位
   - `src/context/AppStateContext.jsx`（最小 Provider）
   - `src/pages/DashboardPage.jsx`、`src/pages/SettingsPage.jsx`
   - `src/api/health.js`、`src/domain/constants.js`、`src/utils/confirm.js`
3. `AGENTS.md` 补充 `npm install` / `npm run dev`。
4. 执行 `npm install` + `npm run build`（或说明用户需本地执行）。

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
1. memory/README.md、sources.md、current.md
2. 根目录 AGENTS.md
3. .cursor/rules/{slug}-agent-nav.mdc
4. .cursor/skills/project-init/SKILL.md

要求：
- 从文档摘录 sources，勿编造
- current 只写阶段、待办、本项目硬约束
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
```

---

## 5. 维护（项目进行中）

| 变化 | 改哪里 |
|------|--------|
| 阶段 / 待办 | `memory/current.md`（覆盖） |
| 需求变更 | `memory/sources.md` + README 日期 |
| 命令 / 边界调整 | `AGENTS.md` + `{slug}-agent-nav.mdc` |
| 工具踩坑 | playbook 对应原子文件（**不进 memory**） |
| memory 膨胀 | 拆 `backlog.md`、`contracts.md`；更新 README 文件表 |
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

权威说明：playbook/patterns/{pattern}.md；本项目现状：memory/current.md 硬约束节。
```

**不要**在初始化时批量生成所有 playbook 领域 Skill — 只加**与本项目相关**的。

---

## 7. 反模式

- 起步就建 `00-index/`～`40-execution/` 编号体系或 10 个空 md
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
- **memory/** = AGENTS.md 的**深层链接**（渐进式披露）
- **playbook/** = 跨项目的**工具配方**，不进单一项目 AGENTS.md 正文
- **.cursor/rules/** = Cursor 特有：alwaysApply 导航 + 按 glob 的硬约束
- **.cursor/skills/** = Cursor 特有：按场景触发，正文链 playbook

参考：[agents.md](https://agents.md/) 开放格式；本项目在 AGENTS.md 之上增加了 memory 分层与 playbook 链。
