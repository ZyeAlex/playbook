# 如何创建 Memory

> **已合并进「初始化项目」** → **[`project-init.md`](./project-init.md)**  
> 新项目请用触发语：`阅读 playbook/，初始化项目`（产出 memory + AGENTS.md + Cursor Rules/Skills）。  
> 本文档保留为**别名入口**；下文仅 memory 子集速查。

Memory 只记**当前项目**：需求摘录、项目结构、阶段进度、契约、联调结论。  
工具用法在 `playbook/`，**不要写进 memory**。

---

## 起步结构（README 入口 + 三文件夹）

```
memory/
├── README.md                 # L0：入口 + 渐进式披露 L1–L4 + source_of_truth
├── requirements/             # L1：需求与业务事实（原子 md + README 索引）
├── structure/                # L2：项目结构、API、存储（原子 md + README 索引）
└── progress/                 # L3：阶段快照 + 原子里程碑 + backlog
```

完整步骤、AGENTS.md 模板、Cursor 配置 → **`project-init.md` §3 Step 1**。

---

## 渐进式披露（L0–L4）

| 层级 | 何时读 | 路径 |
|------|--------|------|
| L0 | 新任务 | `memory/README.md` |
| L1 | 做什么、规则、验收 | `memory/requirements/README.md` |
| L2 | 改代码、找目录/API | `memory/structure/README.md` |
| L3 | 阶段、里程碑、待办 | `memory/progress/README.md` |
| L4 | 工具参数、踩坑 | **`playbook/README.md`（不进 memory）** |

---

## 核心原则（速查）

1. **README 入口 + 三文件夹** — 需求 / 结构 / 进度分桶，各文件夹内原子化
2. **一份快照** — 覆盖旧内容，不堆「第 N 轮」
3. **摘录不转载** — 摘表、字段、流程，不贴 PRD 全文
4. **查阅顺序写进 README** — L0–L4 表比 manifest 更重要
5. **链 playbook** — README 写明「写代码优先读 playbook/」

---

## 仅补建 memory 时（已有 AGENTS.md）

```text
请阅读 playbook/patterns/project-init.md §3（Step 1）。

根据文档来源补建或更新 memory/：
- {PRD、原型路径}
- 项目名：{名称}

只动 memory/：README.md + requirements/ + structure/ + progress/。
从文档摘录 requirements/ 原子文件，勿编造；progress/ 按里程碑原子化。
```

**旧版迁移**：若仍有 `sources.md` / `current.md`，拆入三文件夹后删除，并更新 AGENTS / Rules 链接。

---

## 维护

见 **`project-init.md` §5**。
