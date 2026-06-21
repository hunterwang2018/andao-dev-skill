---
name: onboard
description: 首次接入项目时，初始化目录结构、探测项目类型、检测已有 AI 文档、配置 triage 标签。仅在 STATE.md 不存在时自动触发。
---

# onboard — 项目入职向导

## Goal

首次接入项目（`docs/andao_adr/STATE.md` 不存在）时，完成：
1. 探测项目类型（greenfield / brownfield）
2. 检测已有 AI 上下文文档
3. 检测旧 skill / issue 结构
4. 展示迁移明细让用户确认
5. 初始化目标目录结构
6. 配置 triage 标签
7. 提示是否跑 `scan`

完成后自动路由到 `ask-andao`。

## Workflow

### 第一步 · 检测初始化状态

1. 检查 `docs/andao_adr/STATE.md` 是否存在
   - 存在 → 项目已初始化，直接路由到 `ask-andao`
   - 不存在 → 继续

### 第二步 · 探测项目类型

检查根目录下是否存在以下文件（任一存在即为 brownfield）：
- `package.json` / `pyproject.toml` / `Cargo.toml` / `go.mod` / `Gemfile` / `build.gradle`
- `.git/`
- `src/` / `lib/` / `app/` 等常见源码目录

**输出**：`项目类型：<greenfield | brownfield>`

### 第三步 · 检测已有 AI 文档

按优先级扫描：

| 文件名 | 来源工具 |
|--------|---------|
| `CONTEXT.md`（根目录 / `docs/`） | 通用 |
| `AGENTS.md` | OpenAI Codex |
| `CLAUDE.md`（根目录 / `.claude/`） | Anthropic Claude |
| `.cursor/rules/*.md` | Cursor |
| `.windsurf/rules/*.md` | Windsurf |
| `.github/copilot-instructions.md` | GitHub Copilot |
| `.clinerules` | Cline |

**输出**：`已有 AI 文档列表：<文件名>`

**问用户**：
```
检测到以下 AI 上下文文档：
  - <文件1>
  - <文件2>
如何处理？
1. 综合到 CONTEXT.md（推荐）
2. 以现有文档为准，不进 CONTEXT
3. 忽略现有文档，重新扫描
```

### 第四步 · 检测旧 skill / issue 结构

检查旧目录：

| 检测目标 | 路径 | 说明 |
|---------|------|------|
| 旧 skill | `.opencode/skills/` | flow-x + matt pocock 遗留 |
| 旧 spec | `.specs/` | flow-x 遗留产物目录 |
| 旧 issue | `.scratch/` | matt pocock 遗留 issue tracker |

**对每个检测到的旧结构**，展示明细并询问：
```
检测到遗留 <结构类型>：
  - 路径：<path>
  - 内容：<条目数 / 大小>
  如何处理？
  1. 迁移到目标目录（<旧内容> → <目标路径>）
  2. 跳过，保留原位置不动
  3. 暂时不处理
```

### 第五步 · 初始化目标目录

创建以下目录（如不存在）：
- `docs/andao_specs/` + `pending/` + `archive/abandoned/` + `health/` + `evolve/`
- `docs/andao_adr/adr/`
- `andao-skills/`（已存在则跳过）

初始化 `STATE.md`：

```markdown
# Andao State
last_updated: <YYYY-MM-DD>
active_change_id: null
current_phase: null
last_task: null
last_intel_scan: null
last_evolve_at: null
```

初始化 `CONTEXT.md`（空模板）：

```markdown
# CONTEXT
## 领域语言
（暂无）
## 锁定决策
（暂无）
## 默认偏好
（暂无）
## 既有抽象索引
（暂无）
```

### 第六步 · 配置 triage 标签

问用户：
```
请选择 triage 标签方案：
1. 标准（推荐）：needs-triage / needs-info / ready-for-agent / ready-for-human / wontfix + bug / enhancement
2. 极简：待办 / 进行中 / 已完成
3. 自定义：稍后配置
```

### 第七步 · 提示是否扫描

Brownfield 项目：
```
检测到已有项目。是否运行 scan 生成项目 CONTEXT.md？
1. 是，现在扫描
2. 稍后手动运行
3. 跳过（不推荐）
```

### 第八步 · 完成

输出初始化摘要：
```
✅ Andao 初始化完成
  项目类型：<greenfield | brownfield>
  目录结构：docs/andao_specs/ + docs/andao_adr/
  已有 AI 文档：<已处理方案>
  旧结构迁移：<已处理方案>
  下一步：输入"我想做..."或"我该做什么"开始
```

路由到 `ask-andao`。

## 约束

- 所有迁移操作必须展示明细，用户确认每一步后才执行
- 不删除任何用户文件（旧结构原地保留，或用户确认迁移后复制到目标目录）
- 不可跳过第七步（brownfield 必须提示扫描）
