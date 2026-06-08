# 如何创建 Memory

> **已合并进「初始化项目」** → **[`project-init.md`](./project-init.md)**  
> 新项目请用触发语：`阅读 playbook/，初始化项目`（产出 memory + AGENTS.md + Cursor Rules/Skills）。  
> 本文档保留为**别名入口**；下文仅 memory 子集速查。

Memory 只记**当前项目**：需求摘录、阶段进度、契约、联调结论。  
工具用法在 `playbook/`，**不要写进 memory**。

---

## 起步结构（三文件）

```
memory/
├── README.md       # 入口 + source_of_truth + 查阅顺序 + 链 playbook
├── sources.md      # 从设计文档摘录的事实
└── current.md      # 阶段、待办、本项目硬约束
```

完整步骤、AGENTS.md 模板、Cursor 配置 → **`project-init.md` §3**。

---

## 核心原则（速查）

1. **先小后拆** — 三文件起步，别建空壳编号目录
2. **一份快照** — 覆盖旧内容，不堆「第 N 轮」
3. **摘录不转载** — 摘表、字段、流程，不贴 PRD 全文
4. **查阅顺序写进 README** — 比 manifest 更重要
5. **链 playbook** — README 写明「写代码优先读 playbook/」

---

## 仅补建 memory 时（已有 AGENTS.md）

```text
请阅读 playbook/patterns/project-init.md §3（Step 1）。

根据文档来源补建或更新 memory/：
- {PRD、原型路径}
- 项目名：{名称}

只动 memory/：README.md、sources.md、current.md。
从文档摘录 sources，勿编造；current 只写阶段与待办。
```

---

## 维护

见 **`project-init.md` §5**。
