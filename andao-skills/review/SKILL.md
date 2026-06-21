---
name: review
description: 三轮审查——Spec 合规 + 代码质量（6 维） + UI 视觉（仅前端）。只出报告，不修改代码。
---

# review — 三轮审查（阶段 6）

## Goal

1. 第 1 轮 Spec 合规审查
2. 第 2 轮 6 维代码质量衰退扫描
3. 第 3 轮 UI 视觉审查（仅前端）
4. 严重度分级 🔴/🟡/🟢
5. 追加 fix 任务到 TASK.md
6. 产出 REVIEW.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/MODEL.md` + `TASK.md` + `TEST.md` 存在 + 本次 git diff。

### 第二步 · 加载输入

- 读 `docs/andao_specs/<change-id>/MODEL.md`
- 读 `docs/andao_specs/<change-id>/DESIGN.md`
- 读 `docs/andao_specs/<change-id>/TASK.md`
- 读 `docs/andao_specs/<change-id>/TEST.md`
- 运行 `git diff` 获取本次变更

### 第三步 · 第 1 轮 Spec 合规

| 维度 | 检查项 |
|------|--------|
| AC 覆盖 | 所有 AC 都有对应的测试或实现？ |
| 范围蔓延 | 改动了超出 PROPOSAL/MODEL 范围的文件？ |
| 边界条件 | 错误/空/异常路径有处理？ |
| MODEL 一致性 | 实现和 MODEL.md 的用户故事一致？ |

### 第四步 · 第 2 轮 6 维代码质量

| 维度 | 症状 | 检查方法 |
|------|------|---------|
| R1 认知过载 | 函数/文件过长 | 检查 >50 行函数、>200 行文件 |
| R2 重复 | 相似逻辑多次出现 | grep 相似片段 |
| R3 耦合 | 循环依赖、模块间强耦合 | 检查 import 图 |
| R4 魔法值 | 硬编码字符串/数字 | grep 无命名的字面量 |
| R5 测试缺失 | 核心路径无测试 | 对照 AC 检查测试覆盖 |
| R6 领域模型扭曲 | 实现反映射了 MODELmd 的领域语言 | 对比代码术语和领域术语 |

每个问题记录：
```
- R2 重复（🟡 Major）
  症状：src/utils/format.ts 和 src/helpers/format.ts 有相似的数字格式化逻辑
  来源：T03 引入
  建议：合并为 utils/format.ts 一个版本
```

### 第五步 · 第 3 轮 UI 视觉（仅前端）

| 检查项 | 说明 |
|--------|------|
| Design Tokens 一致性 | 使用了 UI-DESIGN.md 定义的 tokens？ |
| 反 AI-slop | 有 Inter/Roboto 默认字体？有蓝色默认色？ |
| 无障碍 | 颜色对比度、键盘导航、aria 标签 |

### 第六步 · 严重度分级

| 级别 | 含义 | 行动 |
|------|------|------|
| 🔴 Critical | 阻塞性问题 | 必须修复或明确标记"已知接受" |
| 🟡 Major | 质量问题 | 建议修复，安排进 TASK.md |
| 🟢 Minor | 小问题 | 记录但不阻塞 |

### 第七步 · 追加 fix 任务

所有 Critical + 已安排修复的 Major → 追加到 TASK.md 的"修复区域"：
```
## 修复区域
<T-FIX-01> <描述>（🔴 Critical）
<T-FIX-02> <描述>（🟡 Major）
```

### 第八步 · 产出 REVIEW.md

路径：`docs/andao_specs/<change-id>/REVIEW.md`

### 第九步 · 确认完成

```
✅ REVIEW.md 已生成（docs/andao_specs/<change-id>/REVIEW.md）
  Critical：<N> 项
  Major：<N> 项
  Minor：<N> 项
  追加 fix 任务：<N> 项

有 fix 任务 → 需要回到 dev 执行
无 fix 任务 → 准备进入 ship（集成验收）
```

修复任务 → 回到 `dev`。
无修复任务 → 路由到 `ship`。

## 约束

- 禁止直接修改代码（R3.3）
- 所有 Critical 必须修复或经用户确认"已知接受"
- 每个问题必须含症状、来源、建议
