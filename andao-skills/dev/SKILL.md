---
name: dev
description: 单任务 TDD 开发执行。读 TASK.md 中的单个任务，grep 现有抽象 + 扫描 LESSONS.md + TDD 循环 → 产出代码 + SUMMARY.md。
---

# dev — 单任务开发（阶段 4）

## Goal

1. Preflight Gate（检查 TASK.md 存在）
2. 读当前任务 + grep 现有抽象
3. 扫描 LESSONS.md
4. TDD（RED→GREEN→REFACTOR）
5. 运行 verify
6. 6 维提交自检 + 原子提交
7. 产出 SUMMARY.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/TASK.md` 存在。
- 不存在 → "缺少 TASK.md，先跑 slice"
- 存在但无当前任务 → 检查所有任务是否已完成 → 是则路由到 `test`

### 第二步 · 加载输入

- 读 TASK.md 中当前任务块（根据 `ask-andao` 传入的任务 ID）
- 读 `docs/andao_specs/<change-id>/DESIGN.md` `## 0` 段
- UI 任务额外读 `UI-DESIGN.md` + `CONTEXT.md`
- 读 `docs/andao_specs/LESSONS.md`

### 第三步 · grep 现有抽象

执行 `grep` / `rg` 搜索当前任务中需要用到的抽象是否存在：

```
搜索 "<关键字>" 在 src/ 下...
结果：<文件:行号>
```

必须输出 grep 命令 + 结果。如果找到已有抽象 → 优先复用，不重新发明。

### 第四步 · 扫描 LESSONS.md

检查 LESSONS.md 中是否有与本任务相关的失败教训。
- 有 → "LESSONS.md 中有相关教训：<条目>，建议参考"
- 无 → 跳过

### 第五步 · TDD 循环

```
1. RED：写一个会失败的测试
2. GREEN：写最少代码让测试通过
3. REFACTOR：重构，保持测试通过
```

每步都在当前任务范围内，不耦合其他任务。

### 第六步 · Schema 变更（如需）

变更数据库 Schema 时，必须提供可逆迁移（up/down）：
- 迁移文件命名：`<timestamp>-<description>.{sql|py|ts}`
- 不能破坏已有数据

### 第七步 · 运行 verify

执行 TASK.md 中当前任务的 `verify` 命令。
- 通过 → 继续
- 失败 → 回到第五步，修复

### 第八步 · 6 维自检（提交流前）

| 维度 | 检查项 |
|------|--------|
| R1 认知过载 | 一个函数超过 50 行？ |
| R2 重复 | 有代码和已有抽象重复？ |
| R3 耦合 | 引入了新的循环依赖？ |
| R4 魔法值 | 有硬编码的字符串/数字？ |
| R5 测试覆盖 | 测试覆盖了 AC 的所有路径？ |
| R6 越界 | 改动了 TASK 指定的 write_files 之外的文件？ |

异常 → 修复后再提交。

### 第九步 · 原子提交

只提交当前任务的文件，不混合多个任务的改动。

提交信息格式：`<change-id> T<NN>: <任务名>`

### 第十步 · 产出 SUMMARY.md

路径：`docs/andao_specs/<change-id>/T<NN>-SUMMARY.md`

```markdown
# T<NN> Summary — <任务名>

## 实际完成
<做了什么>

## Verify 输出
<命令 + 结果>

## 6 维自检
✅ R1 认知过载：通过
✅ R2 重复：通过
...

## Schema 变更
<有/无，如有附迁移文件路径>

## 越界检查
<TASK write_files 范围内>
```

### 第十一步 · 确认完成

```
✅ T<NN> 已完成（SUMMARY: docs/andao_specs/<change-id>/T<NN>-SUMMARY.md）
下一个任务：T<NN+1> / 全部完成

继续执行下一个任务？还是做其他操作？
```

用户选"继续" → 执行下一个任务。
全部任务完成 → 路由到 `test`。

## 约束

- verify 未通过禁止标记完成
- 禁止修改 MODEL.md / DESIGN.md（发现问题 → 回 propose 走新 change）
- 每次提交仅限单一任务（R7.2）
