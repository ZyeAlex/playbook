# 如何把项目经验提取进 Playbook

项目结束时（或解决了一个**可复用**的坑之后），把**工具级**做法合并进 playbook，不要写项目路径和任务编号。

---

## 什么该进 Playbook / 什么不该

| 进 Playbook | 不进（留在本项目 memory） |
|-------------|---------------------------|
| 库的参数、推荐值、踩坑、反模式 | 阶段进度、backlog、REQ/TASK 编号 |
| 可跨项目复用的方案（如 docx 高亮算法） | 本项目 API 字段、表结构、页面路由 |
| 「为什么这样调 top-k」类决策理由 | 某次验收的 KPI、具体 auditId |
| 通用 AI 指令模板 | PRD/原型摘录 |

**判断一句话**：换一个新项目、换一份需求，这条经验还成立吗？成立 → playbook；不成立 → memory。

---

## 提取步骤

### 1. 找桶

打开 `playbook/README.md` 索引表，按**工具或方案**选分桶：

- 浏览器预览 / 高亮 → `frontend/`
- 解析、切片、PDF → `docs/`
- 向量、重排、Judge → `rag/`
- 长任务、SSE、存储 → `backend/`
- 部署、模型离线 → `ops/`
- 验收方法 → `testing/`
- 跨工具模式 → `patterns/`

### 2. 找文件

该桶下**已有**同主题原子文件 → **追加/修订**，不要新建重复主题。  
没有合适文件 → 新建一个，文件名用**工具或方案名**（如 `docx-preview.md`），并在 README 索引表加一行。

### 3. 写内容（固定结构）

```markdown
## 适用场景
## 推荐参数（表格式）
## 踩坑与解法
## 反模式
## 项目来源（可选，一行：何时验证，无路径）
```

- 删去具体仓库路径、文件名、人名、内部单号  
- 参数写**推荐区间或默认值**，附一句为什么  
- 踩坑写**现象 → 原因 → 解法**

### 4. 冲突合并

新经验与旧文矛盾时：

- 更普适的覆盖旧的；或  
- 并列「场景 A / 场景 B」，标适用条件  
- 删掉已过时、已被推翻的说法（playbook 也是**当前快照**，不堆历史轮次）

### 5. 同步索引

新建文件 → 必须更新 `playbook/README.md` 对应分桶表格。

### 6. 提交 Playbook 仓库

playbook 是**独立 git 仓库**（与业务项目分开）：

```bash
cd playbook
git add .
git commit -m "playbook: {简短说明合并了什么}"
git push
```

业务项目里只保留对 playbook 的引用（如 memory README 链到 playbook），不要把 playbook 全文复制进业务仓库。

---

## 给 AI 的提取指令（项目结束时）

```text
请阅读 playbook/README.md 与 playbook/patterns/playbook-contribute.md。

根据本项目开发经历，把可复用的工具经验合并进 playbook/：
- 来源：本项目 memory 与实现过程（不要抄路径和任务编号）
- 只改 playbook 里已有原子文件，或按规范新建并更新 README 索引
- 每条经验按：适用场景 / 推荐参数 / 踩坑 / 反模式
- 不要写进 playbook：阶段进度、backlog、本项目专有契约

完成后列出改了哪些 playbook 文件。
```

---

## 反模式

- 把整份 memory 或实现说明贴进 playbook  
- 每个项目新建 `project-xxx-notes.md` 堆在 playbook 根目录  
- 只改业务仓库里的 playbook 副本却不 push 独立 playbook 仓库  
- 写「详见某某文件第几行」类项目内引用  
