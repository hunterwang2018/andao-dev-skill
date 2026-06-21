---
name: test
description: 五轮测试金字塔。所有 DEV 任务完成后进入，产出 TEST.md。
---

# test — 五轮测试（阶段 5）

## Goal

1. 声明每轮 ✅/⚠️/❌ 并说明理由
2. 第 1 轮 功能测试（AC → 测试矩阵 + 覆盖率）
3. 第 2 轮 性能测试
4. 第 3 轮 安全测试
5. 第 4 轮 兼容性测试
6. 第 5 轮 可观测性测试
7. 产出 TEST.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/MODEL.md` + `DESIGN.md` + `TASK.md` + 各 `*-SUMMARY.md` 存在。

### 第二步 · 加载输入

- 读 `docs/andao_specs/<change-id>/MODEL.md`（AC）
- 读 `docs/andao_specs/<change-id>/DESIGN.md` `## 0` 段
- 读 `docs/andao_specs/<change-id>/TASK.md`
- 读各 `T<NN>-SUMMARY.md`

### 第三步 · 声明测试轮次

对每轮输出：

```
## 第 1 轮 · 功能测试：✅
（✅ 功能/ ⚠️ 部分/ ❌ 跳过 + 理由）
```

### 第四步 · 第 1 轮 功能测试

| 子项 | 做法 |
|------|------|
| AC → 测试矩阵 | 每条 AC 对应至少一个测试用例 |
| 单元测试 | 覆盖核心逻辑分支 |
| 集成测试 | 覆盖模块间交互 |
| 覆盖率 | 默认 ≥80%，核心模块 ≥90% |
| 边界值 | 空值、极端输入、错误路径 |

测试用例从 AC 派生，不从实现派生。

### 第五步 · 第 2 轮 性能测试

| 子项 | 做法 |
|------|------|
| 性能预算 | 确认响应时间/资源消耗预算 |
| 工具 | Lighthouse / k6 / ab 等 |
| N+1 检测 | 检查数据库查询 |
| 基线对比 | 与上一版本对比 |

### 第六步 · 第 3 轮 安全测试

| 子项 | 做法 |
|------|------|
| 依赖漏洞 | `npm audit` / `pip audit` |
| 密钥扫描 | 检查硬编码密钥 |
| OWASP Top 10 | 逐项检查（XSS/CSRF/注入等） |
| 鉴权 | 验证权限控制 |

### 第七步 · 第 4 轮 兼容性测试

| 子项 | 做法 |
|------|------|
| 跨浏览器（前端） | Chrome/Firefox/Safari/Edge |
| 视口（前端） | 桌面/平板/手机 |
| 数据迁移 | 预演 up/down |
| 跨版本 | 新代码和旧版本交互 |
| 视觉回归（前端） | 按 ui-design 定义的策略执行：Percy/Chromatic/Playwright 截图测试，每个变体 + 每个状态，diff 基线比对 |

### 第八步 · 第 5 轮 可观测性测试

| 子项 | 做法 |
|------|------|
| 日志 | 结构化、无 PII、有追踪 ID |
| 指标 | RED（Rate/Error/Duration） |
| 告警 | 关键路径有告警规则 |
| 健康检查 | 有 `/health` 端点 |

### 第九步 · 产出 TEST.md

路径：`docs/andao_specs/<change-id>/TEST.md`

### 第十步 · 确认完成

```
✅ TEST.md 已生成（docs/andao_specs/<change-id>/TEST.md）
测试声明：5 轮 ✅，覆盖 <N> 条 AC

准备进入下一阶段：review（审查）
继续吗？
```

用户确认 → 路由到 `review`。

## 约束

- 测试用例必须源自 AC，不从实现派生
- 禁止跳过轮次而无明确理由（每轮声明必须写明理由）
- 禁止通过删除/弱化测试来修复失败
