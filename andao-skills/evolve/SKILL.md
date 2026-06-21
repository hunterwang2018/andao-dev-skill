---
name: evolve
description: 批量同步已归档变化的 DESIGN.md §9（架构沉淀建议）到 CONTEXT.md 和 ARCHITECTURE.md。逐项人工审核。
---

# evolve — 架构同步

## Goal

1. 扫描自上次 evolve 以来的归档 change
2. 提取 DESIGN.md §9 中 `evolved: false` 条目
3. 五类聚合
4. 冲突检测
5. 逐项人工审核
6. 产出补丁 + EVOLVE.md

## Workflow

### 第一步 · 确定扫描范围

读 `STATE.md` 的 `last_evolve_at`。扫描 `docs/andao_specs/archive/` 中自该时间之后的 change。

### 第二步 · 读取输入

- 读 `docs/andao_specs/CONTEXT.md`
- 读 `docs/andao_specs/ARCHITECTURE.md`（如存在）
- 读 `docs/andao_adr/STATE.md`

### 第三步 · 提取 §9

逐一读取归档 change 的 `DESIGN.md` `## §9` 节。

跳过标记 `evolved: true` 的条目和无 §9 节或标记为"无建议"的 change。

### 第四步 · 五类聚合

| 类型 | 入 |
|------|---|
| abstraction | 可复用抽象 → CONTEXT.md "既有抽象索引" |
| decision | 技术决策 → CONTEXT.md "锁定决策" |
| contract | 跨模块契约 → ARCHITECTURE.md "跨模块契约" |
| dependency | 依赖变更 → CONTEXT.md "默认偏好" |
| forbidden | 禁止操作清单 → CONTEXT.md "禁止操作清单" |

### 第五步 · 冲突检测

每条新条目与 CONTEXT.md / ARCHITECTURE.md 现有内容对比：
- 无冲突 → 直接归类
- 有冲突（与现有条目矛盾或重叠） → 标记 ⚠️

### 第六步 · 逐项审核

**强制逐项展示给用户确认**：

```
待同步条目：
1. ✅ 抽象：db.py 数据库连接封装（无冲突）
2. ⚠️ 决策：改用 JWT 鉴权（与现有 ADR-001 冲突）
   - 现有：Session Cookie
   - 新建议：JWT
   接受新建议？[Y/n]
```

### 第七步 · 产出补丁

备份当前 CONTEXT.md / ARCHITECTURE.md，然后按段编辑（非整文件重写）。

### 第八步 · 产出 EVOLVE.md

路径：`docs/andao_specs/evolve/<date>-EVOLVE.md`

```markdown
# Evolve — <YYYY-MM-DD>
扫描范围：<last_evolve_at> → <今天>
合计条目：<N> 条（通过 <N> / 冲突 <N> / 跳过 <N>）

## 更新摘要
- CONTEXT.md：+<N> 条
- ARCHITECTURE.md：+<N> 条
```

### 第九步 · 更新 STATE.md

```yaml
last_evolve_at: <YYYY-MM-DD>
```

## 约束

- 不修改业务代码
- 逐项人工审核为强制，不可批量 promote
- 备份为必须
- 不删除已有内容
