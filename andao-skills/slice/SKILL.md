---
name: slice
description: 将设计拆解为可执行的任务。大功能走 multi-issue 模式拆为垂直切片 issue；小功能走 single-change 模式产 TASK.md。
---

# slice — 任务拆解（阶段 3）

## Goal

1. 自动判断范围大小 → 推荐拆解模式
2. 让用户确认
3. 按文件冲突垂直切片（非按层）
4. 每任务有读写文件 + 可执行 verify
5. 波次依赖图
6. 产出 TASK.md（single-change）或 pending/ 下多个 issue（multi-issue）

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/MODEL.md` + `DESIGN.md` 存在。
前端项目还需检查 `UI-DESIGN.md` 存在。

### 第二步 · 加载输入

- 读 `docs/andao_specs/<change-id>/DESIGN.md`
- 读 `docs/andao_specs/<change-id>/MODEL.md`
- 前端项目还读 `UI-DESIGN.md`

### 第三步 · 自动判断范围

| 条件 | 推荐模式 |
|------|---------|
| 波及 >3 个模块 | multi-issue |
| 包含 >2 个可独立交付的垂直切片 | multi-issue |
| 涉及多人/多会话协作 | multi-issue |
| 总改动 <5 个文件 | single-change |
| 纯 UI 变更（无后端逻辑） | single-change |
| 不同切片严格依赖顺序执行 | single-change |

**展示给用户**：
```
根据分析，本 change 建议走 <推荐模式>。
理由：<具体原因>
是否同意？
1. 同意（推荐）
2. 改为 multi-issue
3. 改为 single-change
```

### 第四步 · Multi-issue 模式

产出 N 个独立 issue 写入 `docs/andao_specs/pending/`：

```
docs/andao_specs/pending/
  <change-id>-01-auth.md
  <change-id>-02-profile.md
  <change-id>-03-settings.md
```

每个 issue 格式：

```yaml
---
status: ready-for-agent
source: slice
parent-change: <change-id>
slice-of: <原 PROPOSAL.md 路径>
---
# <垂直切片标题>

## 范围
<做什么，不做什么>

## 涉及文件
- 读：<文件列表>
- 写：<文件列表>

## 验收标准
- [ ] <AC>
```

更新 STATE.md：
```yaml
current_phase: slice
mode: multi-issue
```

输出：
```
✅ 已拆解为 <N> 个独立 issue → docs/andao_specs/pending/
每个 issue 可以单独进入 pipeline 执行。
```

### 第五步 · Single-change 模式

按文件冲突垂直切分为原子任务（每个 2–10 分钟）。

任务格式（XML）：

```xml
<Task id="T01" name="创建用户模型" wave="1">
  <read_files>
    <file>src/schema.prisma</file>
    <file>src/types/user.ts</file>
  </read_files>
  <write_files>
    <file>src/models/user.ts</file>
  </write_files>
  <action>创建 User 模型，包含 id/email/name/createdAt</action>
  <verify>npm run typecheck</verify>
</Task>
```

**波次规则**：
- 按文件冲突分组（同文件的任务必须在不同 wave，或标明可合并）
- `[P]` 标记可并行执行的任务

附加到 TASK.md 末尾：
```
## 阻塞日志
（记录跨波次依赖中的阻塞问题）

## 修复区域
（review 或 diagnose 追加的 fix 任务，前缀 T-FIX-）
```

### 第六步 · 产出 TASK.md

路径：`docs/andao_specs/<change-id>/TASK.md`

### 第七步 · 确认完成

```
✅ TASK.md 已生成（docs/andao_specs/<change-id>/TASK.md）
任务数：<N> 个，波次：<N> 个

准备进入下一阶段：dev（开发）
继续吗？
```

用户确认 → 路由到 `dev`。

## 约束

- 每任务必须有可执行的 verify 命令
- read_files 和 write_files 严格分离
- 任务粒度控制在 2–10 分钟
