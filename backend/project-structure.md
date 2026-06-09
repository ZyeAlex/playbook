# FastAPI 后端项目结构

## 适用场景

企业后台 API：FastAPI + SQLite + 分层架构 + 可选 LLM Agent。  
新项目「初始化后端结构」时按本文脚手架；业务域文件（审核、规则等）后续按需追加。

## 推荐技术栈

| 项 | 选择 |
|----|------|
| 运行时 | Python ≥ 3.11 |
| 包管理 | uv（`pyproject.toml` + `uv.lock`） |
| Web | FastAPI + uvicorn |
| ORM | SQLAlchemy 2.x |
| 配置 | `python-dotenv` + `src/core/settings.py` |
| 测试 | pytest（`tests/`） |

## 目录树（起步骨架）

```txt
backend/
├── pyproject.toml          # 依赖、pytest、uvicorn 默认参数
├── .env.example            # 环境变量模板（勿提交 .env）
├── .gitignore              # Python 运行时忽略（模板见 project-init §3 Step 5.1）
├── dev.ps1 / dev.bat       # 一键启动（可选）
├── README.md               # 启动命令与分层说明
├── data/                   # 运行时数据（gitignore 大文件/缓存）
│   └── .gitkeep
├── scripts/                # 离线脚本、验收脚本（非 API 入口）
│   └── .gitkeep
├── tests/
│   └── test_health.py      # 冒烟测试
└── src/
    ├── main.py             # create_app()、lifespan、CORS
    ├── api/
    │   ├── router.py       # 聚合 endpoints
    │   ├── deps.py         # FastAPI Depends 工厂
    │   └── endpoints/
    │       ├── health.py   # GET /health
    │       └── __init__.py
    ├── core/
    │   ├── settings.py     # pydantic-settings 或 dataclass 配置
    │   ├── database.py     # engine、Session、init_db、get_db
    │   └── executors.py    # 线程池（解析/LLM/重模型，按需）
    ├── models/
    │   ├── base.py         # DeclarativeBase
    │   └── __init__.py
    ├── schemas/            # Pydantic 请求/响应（不放 ORM）
    │   └── __init__.py
    ├── repositories/       # 数据访问；接口 + SqlAlchemy 实现
    │   └── __init__.py
    ├── services/           # 业务编排；可按子域分子目录
    │   └── __init__.py
    └── agents/             # LLM Agent（无 Agent 可先空目录）
        ├── llm_factory.py
        └── prompts/
            └── __init__.py
```

**变大后再加**（勿起步就建）：

```txt
src/services/{子域}/     # 如 chunking/、parsing/
src/core/{domain}_thresholds.py
scripts/business_test_*.py
data/models/             # 向量模型离线缓存
data/{业务目录}/         # 上传原件、索引缓存
```

## 分层职责

| 层 | 路径 | 职责 |
|----|------|------|
| 入口 | `main.py` | 应用工厂、lifespan（init_db、续跑、shutdown） |
| 路由 | `api/endpoints/*.py` | HTTP 薄层：校验入参、调 service、返回 schema |
| 依赖 | `api/deps.py` | `Depends(get_db)` → repository → service 链 |
| 配置 | `core/settings.py` | `API_PREFIX`、`DATABASE_URL`、`ALLOWED_ORIGINS` |
| ORM | `models/` | 表定义；不含业务逻辑 |
| 契约 | `schemas/` | API 与 Agent 的 Pydantic 模型 |
| 仓储 | `repositories/` | CRUD、复杂查询；endpoint 不直接写 SQL |
| 服务 | `services/` | 业务流程、文件处理、编排 Agent |
| Agent | `agents/` | LangChain 调用；prompt 放 `prompts/` |

**调用方向**：`endpoints` → `services` → `repositories` → `models`；`agents` 由 `services` 调用，不反向依赖 endpoints。

## 关键文件约定

### `pyproject.toml`

- `[tool.setuptools.packages.find] where = ["src"]`
- `[tool.pytest.ini_options] pythonpath = ["."] testpaths = ["tests"]`
- 可选 `[tool.uvicorn]` 默认 `app = "src.main:app"`、`reload_dirs = ["src"]`

### `.env.example`

```env
APP_NAME={项目名} API
APP_ENV=dev
API_PREFIX=/api
DATABASE_URL=sqlite:///./data/{slug}.db
ALLOWED_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

### `src/main.py` 最小形态

- `lifespan`：`init_db()` + 资源清理（线程池、后台任务）
- `CORSMiddleware` 读 `settings.allowed_origins`
- `app.include_router(api_router, prefix=settings.api_prefix)`

### `api/endpoints/health.py`

- `GET /health` → `{"status": "ok"}`，供前后端联调与部署探活

## 初始化命令（代理执行）

```bash
cd backend
uv sync
uv run uvicorn src.main:app --reload --reload-dir src --host 0.0.0.0 --port 8000
uv run pytest
```

## `.gitignore`

初始化后端结构时**必须**创建 `backend/.gitignore`。  
完整模板 → [`patterns/project-init.md`](../patterns/project-init.md) §3 Step 5.1。

要点：

- 忽略 `.venv/`、`__pycache__/`、`.pytest_cache/`、`.env`
- 运行时数据按目录忽略：`data/uploads/`、`data/ocr/`、`data/*.db`
- **勿**整包忽略 `data/`，以便提交种子 JSON 等静态文件

## 反模式

- 在 `endpoints` 里写 SQL 或调 ORM（应经 repository/service）
- 用 `schemas` 当 ORM 模型混用
- 大二进制塞 SQLite BLOB（用 `data/` 哈希文件存储，见 `fastapi-sqlite-files.md`）
- 起步就建 20+ 空 service 文件
- `main.py` 与 `src/main.py` 双入口混乱（保留根 `main.py` 占位即可，**服务入口统一 `src.main:app`**）

## 项目来源

QTP 文档审查后端，2026 年验证。
