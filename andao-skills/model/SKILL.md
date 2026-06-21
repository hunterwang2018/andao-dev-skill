---
name: model
description: 将 PROPOSAL.md 细化为可执行的需求模型。产出 MODEL.md + 消费 GRILL-NOTES.md 验证术语后写入 CONTEXT.md。
---

# model — 需求建模（阶段 1）

## Goal

1. 将 PROPOSAL.md 细化为带 AC 的用户故事
2. 消费 GRILL-NOTES.md，验证术语后写入 CONTEXT.md
3. 产出 MODEL.md
4. 更新 STATE.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/PROPOSAL.md` 存在。
- 不存在 → 输出"缺少 PROPOSAL.md，先跑 propose"

### 第二步 · 加载输入

- 读 `docs/andao_specs/<change-id>/PROPOSAL.md`
- 读 `docs/andao_specs/CONTEXT.md`
- 读 `GRILL-NOTES.md`（如存在）

### 第三步 · 消费 GRILL-NOTES.md

从 GRILL-NOTES.md 中提取术语（每条带验证问题），逐条验证：

| GRILL-NOTES.md 中的条目 | 验证方式 | 结果 |
|------------------------|---------|------|
| 术语 | 用用户故事描述验证是否准确 | 通过 → 写入 CONTEXT.md；不通过 → 追问用户 |
| 冲突假设 | 展示给用户确认选择 | 选定 → 写入 CONTEXT.md |
| 待验证 | 逐项验证 | 每项标记 ✅/❌ |

验证通过的术语写入 `docs/andao_specs/CONTEXT.md` 的"领域语言"章节。

验证完成后，删除 `GRILL-NOTES.md`（或标记已消费）。

### 第四步 · 用户故事 + AC

为用户故事填充角色/动作/价值：
```
作为 <角色>
我想 <动作>
以便 <价值>
```

为每个用户故事生成 AC（Given/When/Then）：

```
AC-1：<标题>
  Given <前提条件>
  When <操作>
  Then <预期结果>
```

### 第五步 · 范围切分

| 分类 | 含义 |
|------|------|
| v1 | 本次迭代必须完成 |
| v2 | 后续迭代完成 |
| out | 超出本 change 范围 |

### 第六步 · 非功能性需求

- 性能要求（响应时间、并发量）
- 安全要求（鉴权、数据加密）
- 兼容性要求（浏览器、设备）
- 可观测性要求（日志、监控）

### 第七步 · 产出 MODEL.md

路径：`docs/andao_specs/<change-id>/MODEL.md`

```markdown
# Model — <change-id>

## 用户故事
- <story 1>
- <story 2>

## 验收标准
- AC-1：<title>
  - Given... / When... / Then...

## 范围
- v1：<列表>
- v2：<列表>
- out：<列表>

## 非功能性需求
- <需求 1>
```

### 第八步 · 更新 STATE.md

```yaml
current_phase: model
```

### 第九步 · 确认完成

```
✅ MODEL.md 已生成（docs/andao_specs/<change-id>/MODEL.md）
✅ CONTEXT.md 已更新（X 条新术语）
准备进入下一阶段：design（技术设计）
继续吗？
```

用户确认 → 路由到 `design`。

## 约束

- 只有 `model` 有权将术语写入 CONTEXT.md
- 测试用例从 AC 派生，不从实现派生
