---
name: scan
description: 代码扫描。探测项目元数据、框架、约定、抽象层，生成或更新 CONTEXT.md。
---

# scan — 代码扫描

## Goal

1. 探测项目元数据
2. 框架/库检测
3. 关键约定提取
4. 既有抽象层记录
5. 数据库 Schema 检测
6. 基础设施检测
7. 产出 / 更新 CONTEXT.md

## Workflow

### 第一步 · 文档发现

扫描已有 AI 上下文文档：
- `AGENTS.md` / `CLAUDE.md` / `.cursor/rules/*.md` / `.github/copilot-instructions.md` / `.clinerules`

问用户：如何处理已有文档？

### 第二步 · 项目元数据

| 检测项 | 检查位置 |
|--------|---------|
| 语言 | package.json / pyproject.toml / Cargo.toml / go.mod |
| 包管理器 | package-lock.json / yarn.lock / pnpm-lock.yaml / poetry.lock |
| 脚本 | package.json scripts / Makefile / Justfile / Taskfile |
| CI | .github/workflows/ / .gitlab-ci.yml / Jenkinsfile |

### 第三步 · 框架检测

| 框架 | 检测线索 |
|------|---------|
| React | react + react-dom |
| Vue | vue |
| Express | express |
| FastAPI | fastapi |
| Next.js | next |
| Prisma | prisma/schema.prisma |

### 第四步 · 关键约定

| 约定 | 检测方式 |
|------|---------|
| 命名风格 | camelCase / snake_case / PascalCase 比例 |
| 导入风格 | 相对导入 / alias 导入 |
| 测试框架 | vitest / jest / pytest / mocha |
| 格式化 | .prettierrc / .eslintrc / ruff.toml |

### 第五步 · 既有抽象层

| 抽象 | 搜索线索 |
|------|---------|
| HTTP 客户端 | `fetch` / `axios` / `httpx` 封装 |
| 数据库访问 | ORM 封装 / Repository 模式 |
| 状态管理 | Redux store / Zustand / Pinia |
| 工具函数 | utils/ / helpers/ 目录下的通用函数 |
| Hooks（React） | use* 模式 |

### 第六步 · 数据库 Schema

检测 `schema.prisma` / `migrations/` / `models/` 目录。

### 第七步 · 基础设施检测

检测 `Dockerfile` / `docker-compose.yml` / `k8s/` 等。

### 第八步 · 产出 CONTEXT.md

写入 `docs/andao_specs/CONTEXT.md`：

```markdown
# CONTEXT

## 项目元数据
- 语言：<language>
- 框架：<frameworks>
- 包管理器：<manager>

## 关键约定
- 命名：<camelCase>
- 测试：<vitest>
- 导入：<relative>

## 既有抽象索引
- HTTP 客户端：src/lib/http.ts（fetch 封装）
- 数据库：src/db/prisma.ts（Prisma 单例）

## 基础设施
- Docker：有
- CI：GitHub Actions
```

### 第九步 · 更新 STATE.md

```yaml
last_intel_scan: <YYYY-MM-DD>
```

## 约束

- 不修改业务代码
- 每个字段必须有具体证据（文件路径:行号）
