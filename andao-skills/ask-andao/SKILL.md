---
name: ask-andao
description: Andao 统一入口。每次用户回来都从这里开始。检测项目状态、自动路由到正确技能、展示全景图。
---

# ask-andao — 统一入口

## Goal

1. 检测项目是否初始化（STATE.md 存在？）→ 不存在则路由 `onboard`
2. 读取 STATE.md + git 状态对比，自动修正偏差
3. 根据用户输入路由到正确技能
4. Preflight Gate — 路由到 pipeline 阶段前检查上游产物完整性
5. 所有路由后"停下确认"才进入下一阶段

## Workflow

### 第一步 · 初始化检查

检查 `docs/andao_adr/STATE.md` 是否存在：
- 不存在 → 输出"项目未初始化，先跑 onboard" → 路由到 `onboard`
- 存在 → 继续

### 第二步 · 读取状态 + git 对齐

1. 读 STATE.md 获取：
   - `active_change_id`
   - `current_phase`
   - `last_task`
   - `last_intel_scan`
   - `last_evolve_at`

2. Git 快照对比：
   - 运行 `git status --porcelain` 检查是否有未提交变更
   - 运行 `git log --oneline -1` 获取最近一次提交
   - 对比：
     - STATE 说"dev 中 task T03"但工作区干净 + 最近提交晚于 STATE.last_updated → 推测已完成，STATE 未更新
     - STATE 说"dev 中 task T03"但最近提交早于 STATE.last_updated + 工作区有变更 → 正常开发中
     - 无法访问 git → fallback 信任 STATE.md

3. 发现偏差时自动修正 STATE.md，不询问用户

### 第三步 · 解析用户意图

按优先级匹配：

| 优先级 | 用户输入特征 | 路由到 | 说明 |
|--------|------------|--------|------|
| 1 | `继续` / `resume` / `接着上次` | 展示全景图 + 询问继续 | 见第四步 |
| 2 | 模糊想法（"我想做个XX" / "我想实现XX" / 不确定措辞） | `grill-with-docs` 或 `grill-me` | 有代码库 → grill-with-docs；无代码库 → grill-me |
| 3 | 具体需求（"实现用户登录" / "加一个XX功能" / 清晰的具体描述） | `propose` | 直接进 pipeline |
| 4 | `不做了` / `换一个` / `放弃` | 废弃当前 change | 见"废弃路径" |
| 5 | `我该做什么` / 空 / `帮助` / `help` | 展示全景路由图 | 只展示，不路由 |
| 6 | `诊断` / `debug` / `这个 bug` | `diagnose` | horizontal |
| 7 | `分类` / `triage` | `triage` | horizontal |
| 8 | `扫描代码` / `intel` / `scan` | `scan` | horizontal |
| 9 | `架构梳理` / `架构` / `architect` | `architect` | horizontal |
| 10 | `同步架构` / `evolve` | `evolve` | horizontal |
| 11 | `健康检查` / `health` / `技术债` | `health` | horizontal |
| 12 | `审查代码` / `review` | `review` | pipeline stage 6 |
| 13 | `测试` / `test` | `test` | pipeline stage 5 |
| 14 | `拆任务` / `分解` | `slice` | pipeline stage 3 |
| 15 | `设计` + 已有需求 | `design` | pipeline stage 2 |
| 16 | `UI` / `视觉` / `美学` | `ui-design` | pipeline stage 2a |
| 17 | `上线` / `归档` / `ship` | `ship` | pipeline stage 7 |
| 18 | `执行 T<NN>` / `do T<NN>` | `dev` | pipeline stage 4 |
| 19 | `换风格` / `restyle` / `改视觉` | `ui-design`（restyle 模式） | 视觉风格切换，独立入口 |
| 20 | `设置` / `pre-commit` / `git guardrails` | `setup` | standalone |
| 21 | 不清 / 无法匹配 | 反问用户 | "你是想做新功能、继续上次、还是别的？" |

优先级规则：取最先命中匹配，不尝试多重匹配。

### 第四步 · 全景图展示

当用户说"我该做什么" / 空 / "继续"时，展示：

```
╔══════════════════════════════════════════╗
║           Andao — 项目状态               ║
╠══════════════════════════════════════════╣
║ 活跃 Change：<id>（或无）                ║
║ 当前阶段：<phase>（或无）                 ║
║ 中断任务：<task-id>（或无）               ║
║ 上次扫描：<date>（或从未）                ║
║ 待处理 pending：<count> 条                ║
╠══════════════════════════════════════════╣
║ 你可以：                                  ║
║  1. 继续当前工作                          ║
║  2. 提出新想法（我来帮你打磨）             ║
║  3. 运行诊断 / 扫描 / 健康检查             ║
║  4. 分类待处理的 issue                     ║
║  5. 查看所有可用技能                       ║
╚══════════════════════════════════════════╝
```

用户说"继续" → 输出"进入 <phase> / <task>" + 停下确认"继续吗？"

### 第五步 · Preflight Gate（路由到 pipeline 阶段时触发）

路由到 pipeline 阶段前，检查上游产物完整性：

| 目标阶段 | 必须已有的上游产物 | 缺失时动作 |
|---------|-------------------|-----------|
| `propose` | 无 | 直接进入 |
| `model` | `docs/andao_specs/<id>/PROPOSAL.md` | 回 propose |
| `design` | PROPOSAL.md + MODEL.md | 缺哪个回哪个 |
| `ui-design` | PROPOSAL.md + MODEL.md + DESIGN.md | 回缺失阶段 |
| `slice` | MODEL.md + DESIGN.md；前端还须有 UI-DESIGN.md | 回缺失阶段 |
| `dev` | 正式 TASK.md 或用户显式临时 TASK | 反问：回 slice 还是用户提供临时 TASK |
| `test` | MODEL.md + DESIGN.md + TASK.md + 各 SUMMARY.md | 回缺失阶段 |
| `review` | MODEL.md + TASK.md + TEST.md + 本次 diff | 回缺失阶段 |
| `ship` | `<change-id>/` 下全部应有产物 | 回缺失阶段 |

bug fix 快速通道例外：跳过 propose/model/design。

Preflight 失败时输出：
```
规则 R2.7 触发：目标阶段缺少 <产物>。本次先回到 <阶段> 补齐，不能直接继续。
用户确认后进入补齐阶段。
```

### 第六步 · 废弃路径

用户说"不做了" / "换一个"时：

```
当前 change "<id>"（阶段: <phase> / <task>）未完成。
要废弃它吗？
1. 废弃 + 归档到 archive/abandoned/（保留数据，不再显示）
2. 直接丢弃（删除 .specs/<id>/，不保留）
3. 不废弃，先做新的（STATE 会被覆盖，旧目录残留待 health 清理）
```

用户确认 → 执行 → 路由到"新想法"入口（等待用户新输入）。

## 约束

- 所有路由后必须"停下确认"才进入下一阶段
- 不执行任何具体的开发/设计/测试操作
- Preflight Gate 缺失时必须回退，不可跳过（快速通道除外）
