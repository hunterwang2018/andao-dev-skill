---
name: health
description: 代码库健康巡检。扫描冗余/死代码/技术债，产出健康报告。不自动修改代码。
---

# health — 健康巡检

## Goal

1. 冗余巡检
2. 死代码检测
3. 技术债分级
4. 依赖检查
5. 产出健康报告
6. 🔴 Critical → 自动创建 health-fix CHANGE

## Workflow

### 第一步 · 选择模式

| 模式 | 说明 |
|------|------|
| 快速 | 只检查 TOP 3 问题维度 |
| 完整（推荐） | 全部 6+ 维度 |
| 单维 | 指定一个维度深入 |

### 第二步 · 加载输入

- 读 `docs/andao_specs/CONTEXT.md`（"禁止操作清单"节）
- 读 `docs/andao_specs/ARCHITECTURE.md`（如存在）

### 第三步 · 冗余巡检

| 检测项 | 方法 | 工具 |
|--------|------|------|
| 代码重复 | 检查 >5 行的相同代码块 | jscpd / grep |
| 未使用导出 | 定义了但未引用的函数/变量 | knip / ts-prune / vulture |
| 未使用依赖 | package.json 中的依赖但 import 从未使用 | depcheck / deptry |
| 死代码 | 无法到达的分支/函数 | 静态分析 + 人工确认 |

### 第四步 · 依赖检查

| 检测项 | 方法 |
|--------|------|
| 过期依赖 | `npm outdated` / `pip list --outdated` |
| 未使用依赖 | depcheck |
| 安全漏洞 | `npm audit` / `pip audit` |

### 第五步 · 技术债分级

| 级别 | 含义 | 行动 |
|------|------|------|
| 🔴 Critical | 阻塞维护或存在风险 | 自动创建 health-fix CHANGE |
| 🟡 Scheduled | 需要安排时间处理 | 记入 CONTEXT.md "技术债"节 |
| 🟢 Monitored | 观察即可 | 记入 LESSONS.md |

### 第六步 · 产出健康报告

路径：`docs/andao_specs/health/<YYYY-MM-DD>-HEALTH.md`

```markdown
# Health — <YYYY-MM-DD>

## 摘要
- Critical：<N> 项
- Scheduled：<N> 项
- Monitored：<N> 项

## 详情
### 🔴 <title>
- 路径：<file:line>
- 检测方法：<工具/方法>
- 建议：<修复建议>

### 🟡 <title>
...
```

### 第七步 · Critical → health-fix CHANGE

存在 🔴 Critical 条目时，自动提示：

```
检测到 <N> 项 Critical 问题。
是否创建 health-fix CHANGE 进入 pipeline 修复？
1. 是，创建 change 进 pipeline
2. 否，先记录，以后处理
```

## 约束

- 不直接修改代码
- Critical 创建 health-fix CHANGE 前让用户确认
