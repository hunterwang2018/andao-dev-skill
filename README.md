# andao-dev-skill
一个统一 AI 编程工作流框架，融合 Flow-X 的阶段式流水线与 Matt Pocock 的工程追问体系，通过 21 个技能覆盖从模糊想法到交付归档的完整开发旅程 |  Unified AI skill workflow: from idea to ship, with built-in design, development, testing, review, and release pipelines

## 1. 概述

### 1.1 背景

当前项目同时存在两套技能体系：

- **Flow-X**（15 个 `flow-*` skills）：面向 AI 编程智能体的阶段性工作流框架，强流程、强产物、强门控
- **Matt Pocock**（约 25 个 skills）：工程技能工具箱，以 `ask-matt` 为路由器，强调设计思维、追问打磨、问题分类

两套体系有大量重叠（设计、任务拆解、开发执行、代码扫描），但各有不可替代的精髓（Flow-X 的 UI 设计 + 5 轮测试 + 3 轮审查，MP 的 grill 追问 + bug 诊断 + triage 分类）。合并为统一体系 **Andao**。

### 1.2 设计目标

- 统一入口：用户只面对 `ask-andao`，不需要知道技能名
- 继承双方精髓：不丢掉任何一方的独特能力
- 消除重复：重叠部分只保留一个
- 清晰边界：每个技能有明确的输入、输出、职责
- 可迁移：旧项目通过 `onboard` 技能逐步迁移

### 1.3 设计原则

- **人工在环**：关键决策（技术选型、范围切分、废弃变更、grill → pipeline）必须经用户确认
- **单一角色**：每技能只做一件事（出报告的不写代码，写代码的不改设计）
- **工件驱动**：阶段切换通过上游工件完整性门控（Preflight Gate）
- **自动恢复**：用户中断后回来，`ask-andao` 自动检测并给出继续路径
- **懒惰优先**：小功能走 single-change 不拆多个 issue，hotfix 走快速通道跳过非必要阶段

---

## 2. 目录结构

```
<repo-root>/
  andao-skills/                     # 合并后的 skill 文件（新建，不碰 .opencode/skills/）
    ask-andao/
    onboard/
    grill-with-docs/
    grill-me/
    prototype/
    handoff/
    propose/
    model/
    design/
    ui-design/
    slice/
    dev/
    test/
    review/
    ship/
    scan/
    architect/
    evolve/
    health/
    diagnose/
    triage/
    setup/

  docs/
    andao-design.md                 # 本文档
    andao-plan.md                   # 实施计划
    andao_specs/                    # 变更产物 + 项目级文档
      CONTEXT.md                    # 领域语言、锁定决策、抽象索引
      LESSONS.md                    # 跨变更失败教训
      CHANGELOG.md                  # 变更历史
      STATE.md                      # 当前活跃 change、阶段、中断任务
      pending/                      # inbox backlog（frontmatter status 区分）
        <feature-slug>.md
      <change-id>/                  # 活跃变更产物
        PROPOSAL.md
        MODEL.md
        DESIGN.md
        UI-DESIGN.md
        TASK.md
        T01-SUMMARY.md
        TEST.md
        REVIEW.md
        UAT.md
      archive/                      # 已归档 + 已废弃变更
        <YYYY-MM-DD>-<id>/
        abandoned/
      health/
        <date>-HEALTH.md
      evolve/
        <date>-EVOLVE.md
    andao_adr/
      adr/
        <NNN>-<title>.md
```

---

## 3. 整体业务流程

### 3.1 完整用户旅程

```
首次接入项目
    │
    ▼
┌──────────────────────────────────────────────────────────────────┐
│ onboard (一次性)                                                  │
│ 检测项目类型、已有 AI 文档、旧 skill/issue 结构，让用户确认迁移路径   │
│ 初始化 docs/andao_specs/ 目录 + STATE.md                          │
│ 配置 triage 标签                                                   │
│ 提示是否跑 scan                                                    │
└────────────────┬─────────────────────────────────────────────────┘
                 │
                 ▼
┌──────────────────────────────────────────────────────────────────┐
│ ask-andao (统一入口，每次用户回来都从这里开始)                        │
│                                                                  │
│ 1. 检测 git 状态，与 STATE.md 对比，自动修正偏差                    │
│ 2. 根据用户输入路由：                                              │
│    ├── "继续" / "resume" → 检测 STATE → 展示全景图 + "继续吗？"    │
│    ├── 模糊想法（"我想做个XX"）→ grill-with-docs / grill-me        │
│    ├── 具体需求（"实现用户登录"）→ propose                          │
│    ├── "我该做什么" / 空 → 展示全景路由图                           │
│    ├── "不做了换一个" → 废弃当前 change → 路由到新入口              │
│    ├── "换风格" / "restyle" → ui-design（restyle 模式）            │
│    └── 横向命令关键词 → 对应 horizontal skill                      │
└────────────────┬─────────────────────────────────────────────────┘
                 │
     ┌───────────┴───────────┐
     ▼                       ▼
   Grill Path             Pipeline Path
     │                       │
     ▼                       ▼
┌──────────┐          ┌────────────┐
│ grill-   │          │ propose    │
│ with-docs│          │ (0)        │
│ /        │          └─────┬──────┘
│ grill-me │                │
│          │                ▼
│ ──→ 产出 │          ┌────────────┐
│ GRILL-   │          │ model      │
│ NOTES.md │          │ (1)        │
│ ──→ 停   │          └─────┬──────┘
│ 下确认   │                │
│ ──→ 用   │                ▼
│ 户同意   │          ┌────────────┐
│ 则进     │          │ design     │
│ propose  │          │ (2)        │
└──────────┘          └─────┬──────┘
                             │   ┌──────────────────────┐
                             ├──→│ ui-design            │ (2a, 仅前端)
                             │   │ ├─ 标准模式            │
                             │   │ └─ restyle 模式（"换风格"触发）│
                             │   └──────────┬───────────┘
                            ▼         ▼
                      ┌──────────────────┐
                      │ slice (3)        │
                      │ ├── multi-issue  │ → pending/ → 逐个进 pipeline
                      │ └── single-change│ → 直接产 TASK.md
                      └────────┬─────────┘
                               │
                               ▼
                      ┌──────────────────┐
                      │ dev (4)          │
                      │ TDD + grep 抽象  │
                      │ + LESSONS 扫描   │
                      └────────┬─────────┘
                               │
                               ▼
                      ┌──────────────────┐
                      │ test (5)         │
                      │ 5 轮测试金字塔    │
                      └────────┬─────────┘
                               │
                               ▼
                      ┌──────────────────┐
                      │ review (6)       │
                      │ 3 轮审查         │
                      └────────┬─────────┘
                               │
                               ▼
                      ┌──────────────────┐
                      │ ship (7)         │
                      │ UAT + LESSONS    │
                      │ + evolve 检查    │
                      │ + 归档 + CHANGELOG│
                      └──────────────────┘

     ─── Horizontal 命令（从 ask-andao 直接进入） ───

     scan       ── CONTEXT.md 生成/更新
     architect  ── 架构梳理 + 架构深度报告
     evolve     ── 批量同步 DESIGN §9 → CONTEXT/ARCHITECTURE
     health     ── 冗余/死代码/技术债巡检
     diagnose   ── bug 诊断 → pending issue → 快速通道
     triage     ── 外部 bugs/requests → pending/

     ─── Standalone（从 ask-andao 直接进入） ───

     setup      ── pre-commit hooks + git guardrails
     prototype  ── 需要跑起来验证（handoff 桥接）
     handoff    ── 跨会话桥接
```

### 3.2 bug fix 快速通道

```
diagnose 诊断
    │
    ▼
产出结构化 fix plan
    │
    ▼
自动创建 pending issue（frontmatter type: bugfix）
    │
    ▼
ask-andao 提示：已确认的 bug fix，要走 pipeline 修吗？
    │
    ▼
用户同意 → 进入 pipeline，跳过 propose/model/design
            从 dev 开始（读 fix plan + 修 + 回归测试）
            → test → review → ship

         → 用户拒绝 → fix plan 留在 pending/，待后续处理
```

### 3.3 用户中途放弃路径

```
ask-andao：用户说"不做了，我想做别的"
    │
    ▼
检测当前 STATE 有活跃 change
    │
    ▼
展示选项：
  1. 废弃 + 归档到 archive/abandoned/（保留数据，不再显示）
  2. 直接丢弃（删除 .specs/<id>/，不保留）
  3. 不废弃先做新的（STATE 会被覆盖，旧的残留等 health 清理）
    │
    ▼
用户确认 → 执行 → 路由到新入口
```

### 3.4 pipeline 内部快速通道

| 场景 | 跳过阶段 | 条件 |
|------|---------|------|
| bug fix | propose → model → design | diagnose 产 fix plan，直接从 dev 开始 |
| 纯 CSS/视觉微调 | propose → model → design → test → review | 直接 dev → ship |
| 文档更新 | 整个 pipeline | 直接在文档上修改，不经过 pipeline |
| 3 行以下 bugfix | 整个 pipeline | 不走 pipeline，`ask-andao` 直接反问"小改动，直接修？" |

---

## 4. 技能职责描述

### 4.1 入口技能

| 技能 | `ask-andao` |
|------|-------------|
| **角色** | 统一入口路由 |
| **触发** | 每次用户回来 |
| **输入** | 用户一句话 + `STATE.md` + git 状态 |
| **输出** | 路由到正确技能 |
| **核心职责** | 检测 STATE.md 与 git 状态一致性并自动修正；检测用户意图（继续/新需求/模糊想法/横向命令/放弃）；展示全景路由图；所有路由返回后"停下确认"才进入下一阶段 |
| **不做什么** | 不执行任何具体的开发/设计/测试操作 |

### 4.2 Pre-pipeline 技能

| 技能 | `onboard` |
|------|-----------|
| **角色** | 入职向导 |
| **触发** | 首次接入项目（`STATE.md` 不存在时自动触发） |
| **输入** | 项目根目录 |
| **输出** | 初始化的目标目录结构 + STATE.md + 迁移计划 |
| **核心职责** | 探测项目类型（greenfield/brownfield）、检测已有 AI 文档（AGENTS.md/CLAUDE.md/.cursor 等）、检测旧 skill/issue 结构、展示迁移明细让用户确认每一步、创建 `docs/andao_specs/` + `STATE.md`、配置 triage 标签、提示是否跑 scan |
| **不做什么** | 不删任何用户文件（只创建新目录，旧结构保留） |

| 技能 | `grill-with-docs` |
|------|-------------------|
| **角色** | 想法打磨师 |
| **触发** | 用户说模糊想法 + 有代码库 |
| **输入** | 用户模糊描述 + 代码库 |
| **输出** | `GRILL-NOTES.md`（临时，包含术语、假设、待验证问题） |
| **核心职责** | 多轮追问把隐藏假设炸出来、识别模糊术语、识别冲突假设、探索代码库验证假设 |
| **不做什么** | 不直接写 `CONTEXT.md`（只产 GRILL-NOTES.md）、不产正式提案文件 |
| **完成后** | 停下让用户确认，同意才进 `propose` |

| 技能 | `grill-me` |
|------|-----------|
| **角色** | 想法打磨师 |
| **触发** | 用户说模糊想法 + 无代码库 |
| **输入** | 用户模糊描述 |
| **输出** | 清晰的思路（无本地文件产出） |
| **核心职责** | 同 `grill-with-docs`，但无状态——不保存任何文件 |
| **完成后** | 停下让用户确认，同意才进 `propose` |

| 技能 | `prototype` |
|------|------------|
| **角色** | 方案验证师 |
| **触发** | grill 过程中需要跑起来验证某个设计问题 |
| **输入** | 问题描述 |
| **输出** | 可丢弃的原型代码 + 结论 |
| **核心职责** | 快速编码回答问题、不追求质量、验证完成即丢弃、捕获结论 |
| **不做什么** | 不进入版本控制、不纳入正式代码库 |

| 技能 | `handoff` |
|------|----------|
| **角色** | 上下文桥梁 |
| **触发** | 跨会话需要转移上下文（如进 prototype 前的桥接） |
| **输入** | 当前对话 |
| **输出** | handoff.md 文件 |
| **核心职责** | 压缩对话为结构化摘要、保留关键决策和未解决问题、引用已有产物路径 |
| **不做什么** | 不修改任何项目文件 |

### 4.3 Pipeline 技能

| 技能 | `propose`（阶段 0） |
|------|-------------------|
| **角色** | 合作伙伴 |
| **触发** | grill 完成确认后 + 用户说具体需求 |
| **输入** | 用户需求描述 +（可选）GRILL-NOTES.md |
| **输出** | `PROPOSAL.md` |
| **核心职责** | 自动生成 change-id、结构化反问（最多 3 轮）、Why/What/视觉风格/影响面/范围排除/验收标准 |
| **不做什么** | 不涉及技术设计、不拆任务 |

| 技能 | `model`（阶段 1） |
|------|------------------|
| **角色** | 合作伙伴 |
| **触发** | PROPOSAL.md 已确认 |
| **输入** | PROPOSAL.md + GRILL-NOTES.md（如存在） |
| **输出** | `MODEL.md` + `CONTEXT.md` 更新 |
| **核心职责** | 用户故事 + Given/When/Then AC、v1/v2/out-of-scope 切分、消费 GRILL-NOTES.md 精炼领域术语、将已验证的术语正式写入 CONTEXT.md、非功能性需求 |
| **不做什么** | 不涉及技术方案、不拆任务 |

| 技能 | `design`（阶段 2） |
|------|-------------------|
| **角色** | 架构师 |
| **触发** | MODEL.md 已确认 |
| **输入** | MODEL.md + CONTEXT.md + ARCHITECTURE.md（如存在） |
| **输出** | `DESIGN.md` + ADR（如需） |
| **核心职责** | 技术栈选型、架构级变更检测、既有抽象对齐、技术决策（理由 + 取舍）、数据流/架构图（ASCII/Mermaid）、ADR 记录、风险管理（≥3）、§9 架构沉淀建议 |
| **不做什么** | 不写实现代码（R3.1） |

| 技能 | `ui-design`（阶段 2a） |
|------|----------------------|
| **角色** | 架构师 |
| **触发** | DESIGN.md 已确认 + 前端项目；或用户说"换风格/restyle"（restyle 模式） |
| **输入** | DESIGN.md + 项目现有 UI 代码 |
| **输出** | `UI-DESIGN.md` |
| **核心职责** | Greenfield/Brownfield 判断、ASCII 线框图（低精度布局验证）、五维决策 + Design Tokens（OKLCH 颜色、CSS 变量）、反 AI-slop 自检、关键组件规范、视觉回归策略、占位符策略；Restyle 模式：识别当前风格、产 v2 对比、三波次拆解 |
| **不做什么** | 不写完整组件代码、不改业务逻辑 |

| 技能 | `slice`（阶段 3） |
|------|------------------|
| **角色** | 规划师 |
| **触发** | DESIGN.md 已确认 |
| **输入** | DESIGN.md +（如前端）UI-DESIGN.md |
| **输出** | `TASK.md`（single-change 模式）或 `pending/` 下多个 issue（multi-issue 模式） |
| **核心职责** | 自动判断范围大小 → 推荐 multi-issue / single-change 让用户确认；按文件冲突垂直切分（非按层）；波次依赖图；每任务可执行的 verify 命令 |
| **不做什么** | 不写代码、不做设计决策 |

| 技能 | `dev`（阶段 4） |
|------|----------------|
| **角色** | 操作者 |
| **触发** | 用户说"执行 T<NN>"或"继续" |
| **输入** | TASK.md 中当前任务 + DESIGN.md §0 + LESSONS.md |
| **输出** | 代码更改 + `T<NN>-SUMMARY.md` |
| **核心职责** | Preflight Gate（上游工件完整性）、grep 现有抽象（输出 grep 命令 + 结果）、扫描 LESSONS.md 防重复失败、TDD（RED→GREEN→REFACTOR）、运行 verify、6 维提交自检、原子提交 |
| **不做什么** | 不改 MODEL.md/DESIGN.md（发现问题则回 propose 走新 change） |

| 技能 | `test`（阶段 5） |
|------|-----------------|
| **角色** | 验证者 |
| **触发** | 所有 DEV 任务完成 |
| **输入** | MODEL.md（AC）+ DESIGN.md §0 + TASK.md + 各 SUMMARY.md |
| **输出** | `TEST.md` |
| **核心职责** | 5 轮测试金字塔声明（功能/性能/安全/兼容+视觉回归/可观测）、每轮 ✅/⚠️/❌ 并说明理由、AC → 测试矩阵、覆盖率检查、回归测试登记 |
| **约束** | 测试用例从 AC 派生，不从实现派生；禁止跳过轮次无明确理由 |

| 技能 | `review`（阶段 6） |
|------|-------------------|
| **角色** | 侦察兵 |
| **触发** | TEST.md 已确认 |
| **输入** | MODEL.md + DESIGN.md + TASK.md + TEST.md + git diff |
| **输出** | `REVIEW.md` + 可能的 fix 任务追加到 TASK.md |
| **核心职责** | 3 轮审查：第 1 轮 Spec 合规、第 2 轮 6 维代码质量衰退扫描、第 3 轮 UI 视觉（仅前端）；严重度分级 🔴 Critical / 🟡 Major / 🟢 Minor |
| **不做什么** | 不直接修改代码（R3.3） |

| 技能 | `ship`（阶段 7） |
|------|------------------|
| **角色** | 操作者 |
| **触发** | REVIEW.md 通过 |
| **输入** | 全部上游产物 |
| **输出** | `UAT.md` + 归档产物 + CHANGELOG 条目 + LESSONS 条目 |
| **核心职责** | 运行全量测试、引导人工 UAT（逐 AC 记录）、失败诊断（≤3 轮自动修复）、LESSONS 提名（耗时>30min 错误）、检查 DESIGN.md §9 未同步建议（提醒不阻塞）、归档到 archive/、追加 CHANGELOG |
| **不做什么** | 不修改代码、不跳过检查直接归档 |

### 4.4 Horizontal 技能

| 技能 | `scan` |
|------|--------|
| **角色** | 导航者 |
| **触发** | 用户说"扫描代码"、"intel"、"更新 CONTEXT" |
| **输入** | 代码库 |
| **输出** | `CONTEXT.md`（创建或更新） |
| **核心职责** | 文档发现（已有 AI 文档）、项目元数据探测、框架检测、关键约定、既有抽象层、数据库 Schema、基础设施检测 |
| **不做什么** | 不修改业务代码 |

| 技能 | `architect` |
|------|------------|
| **角色** | 架构师 |
| **触发** | 用户说"架构梳理"、"architect"、"画架构图" |
| **输入** | 代码库 + CONTEXT.md |
| **输出** | `ARCHITECTURE.md` + 可选 HTML 深度报告 |
| **核心职责** | 模块图（Mermaid）、NFR 基线、ADR 索引、跨模块契约、扩展点 + 容量边界、深度挖掘机会报告 |
| **不做什么** | 不修改业务代码 |

| 技能 | `evolve` |
|------|---------|
| **角色** | 哲学家 |
| **触发** | 用户说"同步架构"、"evolve"，或每 60 天提示 |
| **输入** | STATE.md（读取归档列表）+ 已归档 DESIGN.md |
| **输出** | `evolve/<date>-EVOLVE.md` + CONTEXT.md/ARCHITECTURE.md 补丁 |
| **核心职责** | 提取归档 DESIGN.md §9 建议、五类聚合（抽象/决策/契约/依赖/禁止操作）、冲突检测、逐项人工审核、备份后写入 |
| **不做什么** | 不删除已有内容、不批量自动 promote |

| 技能 | `health` |
|------|---------|
| **角色** | 侦察兵 |
| **触发** | 用户说"健康检查"、"health"、"技术债扫描" |
| **输入** | 代码库 |
| **输出** | `health/<date>-HEALTH.md` + 可选 health-fix CHANGE |
| **核心职责** | 冗余巡检（jscpd/knip/vulture/depcheck）、死代码检测、依赖过期、技术债分级；🔴 Critical → 自动创建 health-fix CHANGE 进 pipeline |
| **不做什么** | 不直接修改代码 |

| 技能 | `diagnose` |
|------|-----------|
| **角色** | 侦察兵 |
| **触发** | 用户说"诊断"、"debug"、"这个 bug" |
| **输入** | bug 描述 + 代码库 |
| **输出** | 诊断报告 + fix plan + 自动创建 pending issue（type: bugfix） |
| **核心职责** | 6 阶段诊断：构建复现循环 → 复现+最小化 → 假设排序（3-5 个可证伪的）→ 插桩验证 → 修复+回归测试计划 → 事后总结 |
| **不做什么** | 不直接修代码（由 pipeline 快速通道执行） |

| 技能 | `triage` |
|------|---------|
| **角色** | 分类员 |
| **触发** | 用户说"triage"、"分类"、"来处理这些 issue" |
| **输入** | 外部 bug report / feature request / PR |
| **输出** | `pending/` 下带 frontmatter 的 issue 文件（status: needs-triage / needs-info / ready-for-agent / wontfix） |
| **核心职责** | 复现验证（bug）或需求评估（enhancement）、按需 grill、编写 agent brief、记录 out-of-scope、标记 ready-for-agent |
| **不做什么** | 不执行变更、不直接修代码 |

### 4.5 Standalone 技能

| 技能 | `setup` |
|------|---------|
| **角色** | 配置员 |
| **触发** | 用户说"设置 pre-commit"、"加 git guardrails" |
| **输入** | 项目 |
| **输出** | pre-commit hooks + git guardrails 配置 |
| **核心职责** | 检测包管理器、安装 husky/lint-staged、配置 pre-commit hook（格式化+类型检查+测试）、安装 git guardrails（防 push/reset --hard 等） |

---

## 5. 产物规范

### 5.1 产物完整性要求

每个 change 的 pipeline 中，各阶段到达时 `docs/andao_specs/<change-id>/` 下必须已有：

| 阶段 | 必须已存在的产物 |
|------|----------------|
| `propose` | 无 |
| `model` | PROPOSAL.md |
| `design` | PROPOSAL.md + MODEL.md |
| `ui-design` | PROPOSAL.md + MODEL.md + DESIGN.md |
| `slice` | MODEL.md + DESIGN.md（前端还需 UI-DESIGN.md） |
| `dev` | 正式 TASK.md 或用户显式提供临时 TASK |
| `test` | MODEL.md + DESIGN.md + TASK.md + 各 SUMMARY.md |
| `review` | MODEL.md + TASK.md + TEST.md + 本次 diff |
| `ship` | `<change-id>/` 下全部应有产物 |

### 5.2 Preflight Gate（阶段门控）

进入任何阶段前，`ask-andao` 执行 Preflight Gate：

- 检查上游产物是否完整
- 缺失 → 输出"规则 R2.7 触发：目标阶段缺少 `<产物>`。本次先回到 `<阶段>` 补齐"
- 不缺失 → 允许进入
- bug fix 快速通道例外：跳过 propose/model/design，从 dev 开始

### 5.3 角色红线

| 规则 | 内容 |
|------|------|
| R1 | 架构师（design/ui-design/architect）不写实现代码 |
| R2 | 开发（dev）不改设计文档（发现问题走新 change） |
| R3 | 审查者（review/health/diagnose/triage）不修改代码 |
| R4 | 一次性提交原子 commit（不混合多个任务的改动） |

---

## 6. 应用场景速查

| 用户说 | 触发技能 | 说明 |
|--------|---------|------|
| "我有个想法" | `grill-with-docs` / `grill-me` | 模糊想法优先打磨 |
| "实现用户登录" | `propose` | 具体需求直接进 pipeline |
| "继续" / "resume" | `ask-andao` | 恢复上次中断 |
| "审查代码" | `review` | 直接进审查阶段 |
| "测试" | `test` | 直接进测试阶段 |
| "拆任务" | `slice` | 从上游产物拆解 |
| "诊断这个 bug" | `diagnose` | 产出 fix plan 进 pending |
| "健康检查" | `health` | 代码库巡检 |
| "架构梳理" | `architect` | 产 ARCHITECTURE.md |
| "扫描代码" | `scan` | 产/更新 CONTEXT.md |
| "同步架构" | `evolve` | 批量同步 DESIGN §9 |
| "分类这些 issue" | `triage` | 外部请求分类 |
| "换风格" / "restyle" | `ask-andao` → `ui-design`（restyle 模式） | 视觉风格切换，独立触发入口 |
| "设置 pre-commit" | `setup` | 工具配置 |
| "我该做什么" / 空 | `ask-andao` | 展示全景图 |
| "不做了换一个" | `ask-andao` | 废弃当前 change |

---

## 7. 与原始两套体系的对比

| 维度 | Flow-X | Matt Pocock | Andao（合并后） |
|------|--------|-------------|----------------|
| 技能数 | 15 | ~25 | 21 |
| 入口 | flow-go | ask-matt | ask-andao（融合两者） |
| 流程强度 | 高（刚性流水线） | 低（柔性路由） | 中（流水线骨架 + 柔性 grill/快速通道） |
| 产物 | `.specs/` | `.scratch/` | `docs/andao_specs/` + `docs/andao_adr/` |
| 测试 | 5 轮金字塔 | 无（仅 tdd） | 保留 5 轮金字塔 |
| 审查 | 3 轮 | 无独立 skill | 保留 3 轮 |
| 设计词汇 | 无 | deep module/seam/depth | 吸收进 design + architect |
| Bug 诊断 | 无 | diagnosing-bugs | 保留为 diagnose |
| 问题分类 | 无 | triage | 保留为 triage |
| UI 设计 | flow-ui-design + flow-restyle | 无 | 保留 |
| 追问打磨 | 无（3 个问题模板化） | grill-with-docs / grill-me | 保留为独立 grill 阶段 |
| 上手引导 | flow-go brownfield 检测 | setup-matt-pocock-skills | onboard（融合两者） |
