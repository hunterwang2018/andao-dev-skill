---
name: triage
description: 外部流入的 bugs/requests/PRs 分类。产出 pending/ 下带 frontmatter status 的 issue。
---

# triage — 问题分类

## Goal

1. 接收外部 issue / bug report / feature request
2. 复现验证（bug）或需求评估（enhancement）
3. 按需 grill
4. 标记 status
5. 产出 agent brief
6. 记录 out-of-scope

## Workflow

### 第一步 · 确定输入源

| 来源 | 路径 |
|------|------|
| 手动输入 | 用户粘贴/描述 |
| GitHub issues | `gh issue list` |
| pending/ 已有 | 扫描 `needs-triage` 条目 |

### 第二步 · 分类

| 类别 | 判断标准 |
|------|---------|
| `bug` | 已有功能行为不符合预期 |
| `enhancement` | 新增功能或改进现有功能 |

### 第三步 · 复现验证（bug）

**Bug** → 尝试复现：
- 能复现 → 标记 `bug` + 补充复现步骤
- 不能复现 → 标记 `needs-info`，问用户要更多信息

### 第四步 · 需求评估（enhancement）

**Enhancement** → 检查：
- 是否超出当前项目范围 → 是 → `wontfix` + 记录 out-of-scope
- 是否已在 pending/ 中存在（重复）→ 是 → 标记为重复并关闭
- 是否清晰可执行 → 是 → 继续

按需调用 `grill-with-docs`（内部追问不打断用户）。

### 第五步 · 标记 status

| status | 含义 | 条件 |
|--------|------|------|
| `needs-triage` | 等待评估 | 初始状态 |
| `needs-info` | 缺少信息 | 无法复现或需求不清 |
| `ready-for-agent` | 可直接进入 pipeline | 已完全明确 |
| `wontfix` | 不会处理 | 超出范围或拒绝 |

### 第六步 · 产出 agent brief

对 `ready-for-agent` 的条目，产出 agent brief：

```yaml
---
status: ready-for-agent
category: bug | enhancement
---
# Agent Brief

## 描述
<一句话概述>

## 当前行为（bug） / 期望行为（enhancement）
<描述>

## 关键接口
- <模块 / 文件>

## 验收标准
- [ ] <AC 1>

## 超范围（本次不做）
- <列表>
```

### 第七步 · 记录 out-of-scope

被 `wontfix` 的 enhancement 记录到 `docs/andao_specs/pending/out-of-scope.md`（或每个 feature 一个文件），供后续去重。

### 第八步 · 确认完成

```
✅ 分类完成
- bug：<N> 个
- enhancement：<N> 个
- wontfix：<N> 个
- needs-info：<N> 个

ready-for-agent 的条目可随时进入 pipeline。
```

## 约束

- 不执行变更
- 不直接修代码
