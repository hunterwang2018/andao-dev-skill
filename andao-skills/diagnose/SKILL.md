---
name: diagnose
description: 6 阶段 Bug 诊断。产出诊断报告 + fix plan + 自动创建 pending issue（type: bugfix），走 pipeline 快速通道。
---

# diagnose — Bug 诊断

## Goal

1. 构建复现循环（THE skill）
2. 复现 + 最小化
3. 假设排序（3–5 个可证伪的）
4. 插桩验证
5. 修复 + 回归测试计划
6. 事后总结
7. 产出 fix plan → pending issue → pipeline 快速通道

## Workflow

### 第一步 · 构建复现循环（最关键）

构建一个能运行的最小命令，让 bug 可靠复现：

```
最简复现命令：
<command>
```

**这是诊断中最重要的一步**。如果能一键复现，剩下全是机械操作。如果不能 → 先花时间让它可以。

### 第二步 · 复现 + 最小化

确认 bug 确实存在。然后逐步消除无关条件：

```
最小化前：需要 A → B → C → D 才能触发
最小化后：只要 A → D 就能触发
```

### 第三步 · 假设排序

提出 3–5 个假设，按可能性从高到低排列。**每个假设必须可证伪**：

```
假设 1（高）：<假设内容>
  → 验证方法：<怎么做>
假设 2（中）：<假设内容>
  → 验证方法：<怎么做>
```

### 第四步 · 插桩验证

逐条验证假设。每个假设验证后：
- ✅ 证实 → 根因已定位
- ❌ 排除 → 验证下一个

### 第五步 · 修复 + 回归测试计划

产出结构化 fix plan：

```markdown
## Fix Plan
- 根因：<一句话>
- 修复方案：<怎么做>
- 涉及文件：<列表>
- 回归测试：<如何保证不再出现>
```

### 第六步 · 事后总结

```
## Post-mortem
- 根因类型：<逻辑错误 / 边界条件 / 配置问题 / 外部依赖>
- 关注信号：<未来如何更早发现>
- LESSONS 提名：<是否值得入库>
```

### 第七步 · 产出 pending issue

自动创建 pending issue，标记 `type: bugfix`：

```markdown
---
status: ready-for-agent
source: diagnose
type: bugfix
fix-plan: <summary>
---
# [Bug] <标题>

## 根因
<一句话>

## Fix Plan
<引用 fix plan>

## 涉及文件
<列表>
```

路径：`docs/andao_specs/pending/fix-<short-id>.md`

输出：
```
✅ 诊断完成。Fix plan 已写入 pending issue。
是否进入 pipeline 快速通道修？
1. 是 → 进 pipeline（跳过 propose/model/design，从 dev 开始）
2. 否 → 先记录，以后修
```

用户选 1 → 路由到 pipeline 快速通道。

## 约束

- 每一步假设必须可证伪（不可证伪 = 没用）
- 不直接修代码
