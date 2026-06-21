---
name: ship
description: 集成验证与归档。运行全量测试 + 引导 UAT + LESSONS 提名 + evolve 检查 + 归档。用户确认后执行。
---

# ship — 集成归档（阶段 7）

## Goal

1. 运行全量自动化测试
2. 引导人工 UAT（逐 AC 记录）
3. 失败诊断（≤3 轮自动修复）
4. LESSONS 提名
5. 检查 DESIGN.md §9 evovled 状态（提醒不阻塞）
6. 归档到 archive/
7. 追加 CHANGELOG.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/` 下全部应有产物存在。

### 第二步 · 运行全量自动化测试

- 单元测试
- 集成测试
- 类型检查
- 构建

全部通过 → 继续。
有失败 → 输出"测试失败：<细节>"，确认是否可以继续。

### 第三步 · 人工 UAT

逐条 AC 引导用户确认：

```
AC-1：<描述>
实际结果：<描述>
通过？[Y/n]
```

**逐条记录结果到 UAT.md**：

```markdown
# UAT — <change-id>

## AC 验证结果
- [x] AC-1：<结果> ✅
- [ ] AC-2：<结果> ❌
```

### 第四步 · 失败诊断

有 AC 未通过时：
1. 定位根因
2. 生成 fix-plan
3. 追加到 TASK.md "修复区域"（前缀 T-FIX-XX）
4. 回到 `dev`

**最多 3 轮修复重试**。超过 3 轮仍失败 → "UAT 失败超过 3 轮，需要人工介入。"

### 第五步 · LESSONS 提名

扫描各 `SUMMARY.md`，标记符合以下条件的条目：

| 条件 | 说明 |
|------|------|
| 耗时 >30 分钟的调试 | 值得记录避免重复 |
| 跨任务可能遇到问题的技术细节 | 对后续任务有参考价值 |
| 特殊配置/依赖的坑 | 其他人可能踩到 |

提名确认后追加到 `docs/andao_specs/LESSONS.md`：

```markdown
## L-<NNN>：<标题>
- 场景：<什么情况下发生>
- 根因：<为什么>
- 解决：<怎么修的>
- 建议：<以后怎么避免>
```

### 第六步 · evolve 检查

检查 `docs/andao_specs/<change-id>/DESIGN.md` 中 §9 是否有 `evolved: false` 条目：

```
⚠️ 你有 <N> 条架构沉淀建议未同步到 CONTEXT/ARCHITECTURE。
建议尽快运行 evolve。是否现在跑？
1. 现在跑 evolve
2. 先归档，以后再说（提醒保持）
```

用户选 2 不阻塞归档。

### 第七步 · 归档

1. 移动 `docs/andao_specs/<change-id>/` → `docs/andao_specs/archive/<YYYY-MM-DD>-<change-id>/`
2. 追加 `docs/andao_specs/CHANGELOG.md`：

```markdown
## <YYYY-MM-DD>：<change-id>
- <一句话描述>
- 任务数：<N>
- AC 通过率：<N/N>
- LESSONS 提名：<N> 条
```

3. 清空 STATE.md 的活跃 change 指针

### 第八步 · 确认完成

```
✅ change "<change-id>" 已归档
  → archive/<YYYY-MM-DD>-<change-id>/
  → CHANGELOG 已更新
  → LESSONS 提名 <N> 条
  → Evolve 检查：<N> 条待同步

准备回到 ask-andao。
```

路由到 `ask-andao`。

## 约束

- UAT 重试不超过 3 轮
- 归档操作必须用户确认
- evolve 检查提醒不阻塞归档
