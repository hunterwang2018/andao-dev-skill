---
name: propose
description: 将经过 grill 打磨或用户清晰描述的 需求转为结构化变更提案，产出 PROPOSAL.md，输入到 model 阶段。
---

# propose — 变更提案（阶段 0）

## Goal

1. 自动生成 change-id
2. 结构化反问澄清（≤3 轮）
3. 确认无架构级变更
4. 产出 PROPOSAL.md
5. 更新 STATE.md

## Workflow

### 第一步 · Preflight Gate（由 ask-andao 执行）

`ask-andao` 已确认上游完整（无前置依赖），进入本阶段。

### 第二步 · 读取已有输入

- 读 `docs/andao_specs/CONTEXT.md`
- 如 `GRILL-NOTES.md` 存在，读它
- 问用户一句需求的简短描述

### 第三步 · 自动生成 change-id

规则：kebab-case，2–4 个英文词，反映变更本质。

示例：
- `implement-user-auth`
- `add-dark-mode`
- `fix-payment-timeout`

检查 `docs/andao_specs/` 下是否存在同名目录。有冲突 → 自动加序号（`implement-user-auth-2`）。

### 第四步 · 结构化反问（最多 3 轮）

每轮最多 3 个问题，按以下维度依次深入：

| 维度 | 问题示例 |
|------|---------|
| Why | "为什么要做这个？解决了什么问题？" |
| What | "具体包括什么？" |
| Scope | "第一期必须有什么？什么可以后续再做？" |
| Visual（前端） | "视觉风格有什么偏好？有参考吗？" |
| Edge | "用户不登录可以用吗？数据为空时怎么显示？" |

**架构级变更检测**：询问用户"这次变更是否涉及架构层面的改动？比如新增模块、修改数据流、变更技术栈？"
- 是 → 提示"建议先跑 architect" → 路由到 `architect`
- 否 → 继续

### 第五步 · 产出 PROPOSAL.md

路径：`docs/andao_specs/<change-id>/PROPOSAL.md`

```markdown
# Change Proposal — <change-id>

## Why
<为什么做>

## What
<做什么>

## 影响面
- 涉及模块：<列表>
- 涉及文件：<预估>
- 数据库变更：<是 / 否>

## 范围排除（本次不做）
- <至少 1 条>

## 验收标准
- [ ] <AC 1>
- [ ] <AC 2>

## 视觉风格（仅前端）
- <风格描述或"不涉及">
```

### 第六步 · 更新 STATE.md

```yaml
active_change_id: <change-id>
current_phase: propose
```

### 第七步 · 确认完成

```
✅ PROPOSAL.md 已生成（docs/andao_specs/<change-id>/PROPOSAL.md）
准备进入下一阶段：model（需求建模）
继续吗？
```

用户确认 → 路由到 `model`。

## 约束

- 反问最多 3 轮
- 架构级变更检测不可跳过
- 范围排除至少 1 条
