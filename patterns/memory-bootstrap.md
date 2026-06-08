# 如何创建 Memory

Memory 只记**当前项目**：需求摘录、阶段进度、契约、联调结论。  
工具用法在 `playbook/`，**不要写进 memory**。

---

## 这一路下来的优化结论

1. **先小后拆**：起步 2～3 个文件够用，别一上来建多层编号目录和一堆空壳。
2. **一份快照**：每个文件只保留**当前**说法，覆盖旧内容，不要在同一文件里堆「第 N 轮」。
3. **摘录不转载**：从 PRD/原型**摘**表、字段、流程，不贴全文。
4. **查阅顺序写进 README**：代理先读哪、再读哪，比复杂 manifest 更重要。
5. **manifest 可选**：项目变大后再加 `manifest.json` 列路径与 `source_of_truth`；小项目 README 里几行即可。
6. **链一次 playbook**：README 写明「写代码优先读 `playbook/`」，不必在 memory 里重复工具参数。
7. **按需增文件**：backlog 独立、契约独立、联调快照独立——**等到单文件难维护再拆**。

---

## 推荐起步结构

```
memory/
├── README.md       # 必建：入口 + 文档来源 + 查阅顺序 + 链 playbook
├── sources.md      # 必建：从设计文档摘录的事实
└── current.md      # 必建：现在在做什么、做到哪、待办、硬约束
```

变大后再拆（示例，按项目自取）：

```
memory/
├── README.md
├── nav.md              # README 太长时：「找什么 → 读哪」表
├── sources/
│   ├── prd.md
│   └── proto.md
├── current.md          # 或改名为 phase.md
├── backlog.md
├── contracts.md        # API / 数据契约稳定后
└── manifest.json       # 可选：机器可读索引
```

**不必**照搬 `00-index` / `10-sources` / `40-execution` 编号体系；编号适合超大项目，小项目反而难维护。

---

## 创建步骤

### 1. 读文档来源

用户提供的 PRD、原型、设计说明等——先读，再动笔。  
在 README 列出 **`source_of_truth`**（路径或文件名列表）。

### 2. 写 `memory/README.md`

```markdown
# {项目名} Memory

## 文档来源
- {PRD}
- {原型}
- …

## 查阅顺序
1. 本文
2. sources.md — 需求与交互事实
3. current.md — 阶段、待办、硬约束
4. 写代码 / 工具踩坑 → playbook/README.md（优先）

## 文件说明
| 文件 | 内容 |
|------|------|
| sources.md | 从文档来源摘录，不编造 |
| current.md | 当前阶段快照，随进度覆盖更新 |
```

有 `playbook/` 时加一句：**涉及实现，优先读 playbook，再回 current 看本项目差异。**

### 3. 写 `memory/sources.md`

从文档来源整理，建议分节：

- 产品目标（一两句）
- 核心流程 / 页面
- 关键字段与状态（表格式）
- 验收口径（可测的说法）
- 与原型/PRD 不一致时的**以谁为准**

只写**稳定事实**；猜想标「待确认」。

### 4. 写 `memory/current.md`

建议分节：

- **当前阶段**（名称 + 一句话目标 + 开始日期）
- **已完成**（模块级勾选，勿冗长）
- **进行中 / 待办**（可编号 TASK-01，不必一开始就用 T-xxx）
- **硬约束**（仅本项目业务规则，如「运行时读快照不读 live 配置」）
- **已知缺口**（可选）

不写工具参数、不写通用踩坑。

### 5. 可选：后续拆分

| 何时拆 | 拆出什么 |
|--------|----------|
| `current.md` 超过 ~150 行 | `backlog.md`、`phase.md` |
| 契约稳定且常改 | `contracts.md` |
| 前后端联调频繁 | `sync.md`（仅本项目 UI/API 对齐记录） |
| 代理常找错文件 | `nav.md` 或 `manifest.json` |

新建文件后**只改 README 的「文件说明」表**，不要留死链。

---

## 给 AI 的指令（新开项目）

```text
请阅读 playbook/patterns/memory-bootstrap.md。

根据文档来源为本项目创建 memory/：
- {列出 PRD、原型等路径}
- 项目名：{名称}

只创建 memory，按「推荐起步结构」：
README.md、sources.md、current.md。
从文档摘录 sources，勿编造；current 只写阶段与待办。
写代码时优先 playbook，不要把工具经验写进 memory。
```

---

## 维护（项目进行中）

- **阶段变化** → 改 `current.md`（覆盖旧说法）
- **需求变更** → 改 `sources.md` + README 的 `updated` 日期（可选）
- **工具踩坑** → 合并进 `playbook/`，不要写进 memory
- **拆文件** → 更新 README 文件表，删掉已迁移的旧节

## 反模式

- 先建空目录、空 manifest、10 个占位 md
- memory 里写框架教程、向量 top-k、组件 API
- sources 粘贴 PRD 全文
- README 没有「查阅顺序」，代理每次从零搜仓库
- 同一文件里保留多轮历史修订记录
