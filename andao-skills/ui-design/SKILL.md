---
name: ui-design
description: 前端项目的 UI 美学设计。产出 Design Tokens + 组件规范 + UI-DESIGN.md。前端项目在 design 完成后进入。
---

# ui-design — UI 美学设计（阶段 2a）

## Goal

1. Greenfield/Brownfield 判断
2. 四问美学方向
3. 五维决策（字体/颜色/动效/间距/质感）
4. 产出 Design Tokens（OKLCH 颜色、CSS 变量）
5. 关键组件规范
6. 反 AI-slop 自检
7. 产出 UI-DESIGN.md

## Workflow

### 第一步 · Preflight Gate

检查 `docs/andao_specs/<change-id>/DESIGN.md` 存在。

### 第二步 · 加载输入

- 读 `docs/andao_specs/<change-id>/DESIGN.md` `## 0` 段
- 读 `docs/andao_specs/<change-id>/PROPOSAL.md`（视觉风格章节）
- 读 `docs/andao_specs/CONTEXT.md`

### 第三步 · 判断项目类型

检查项目中是否已有 UI 代码（主题文件、CSS 变量、设计 token 文件）：
- 有 → Brownfield：反向提炼现有 tokens
- 无 → Greenfield：从零设计

### 第四步 · 四问美学方向

```
1. 目的：这个界面主要用于什么场景？（展示/操作/引导）
2. 风格：你心中想到的风格？（极简/圆润/硬朗/鲜艳/冷色系）
3. 约束：有没有品牌指南或现有设计系统要遵循？
4. 差异化：希望和竞品有什么视觉区别？
```

### 第五步 · ASCII 线框图

在确认美学方向后、进入像素级设计前，输出此 change 涉及的所有关键页面的低精度线框图。

**内容**：布局结构（header/sidebar/main/footer）、信息层级、用户流转路径

**格式**（ASCII）：

```
+------------------+
| Header / Nav     |
+--------+---------+
| Sidebar| Main    |
| - Link1| Content |
| - Link2| here    |
|        |         |
+--------+---------+
| Footer            |
+------------------+
```

**目的**：在设计 tokens 之前确认布局和信息架构，避免"先画颜色再改布局"的返工。

**确认后进入下一阶段**。

### 第六步 · 五维决策

| 维度 | 输出 | 示例 |
|------|------|------|
| 字体 | 主字体 + 等宽字体 | Inter (UI) / JetBrains Mono (code) |
| 颜色 | 主色 + 辅色 + 语义色 | OKLCH(65% 0.15 250) |
| 动效 | 过渡 + 动画曲线 | 200ms ease-in-out |
| 间距 | 4px 基准网格 | 4/8/12/16/20/24/32 |
| 质感 | 圆角 + 阴影 + 边框 | 8px radius / 0 2px 8px shadow |

**颜色必须用 OKLCH**，禁止用 hex/rgb 设计阶段。

### 第六步 · Design Tokens

产出 CSS 变量格式的 tokens：

```css
:root {
  --color-primary: oklch(55% 0.15 250);
  --color-surface: oklch(98% 0.01 250);
  --font-sans: 'Inter', system-ui, sans-serif;
  --radius-sm: 4px;
  --space-md: 16px;
}
```

### 第七步 · 关键组件规范

只定义本 change 涉及的关键组件（不定义全局库）：

```
### Button
- 尺寸：sm / md / lg
- 变体：primary / secondary / ghost
- 状态：default / hover / active / disabled
- tokens 映射：--color-primary / --radius-sm
```

### 第八步 · 反 AI-slop 自检

检查以下常见 AI 默认值：

| 检查项 | AI 默认（slop） | 建议 |
|--------|----------------|------|
| 字体 | Inter / Roboto | 确认是否刻意选择 |
| 颜色 | 蓝 #3B82F6 | 必须用 OKLCH 指定 |
| 圆角 | 8px | 和间距网格一致 |
| 图标 | Lucide / Heroicons | 确认是否适用 |
| 布局 | Flexbox 万能 | 考虑 Grid 场景 |

### 第九步 · 视觉回归策略

定义本 change 的视觉回归测试计划（通知 `test` 阶段在第 4 轮集成）。

| 子项 | 做法 |
|------|------|
| 工具推荐 | Chromatic / Percy / Playwright 截图测试 |
| 快照范围 | 每个关键组件变体 + 每个页面状态（loading/empty/error/normal） |
| 基线管理 | 首次运行建立基线，后续 diff 检测 |
| test 阶段集成 | test 阶段第 4 轮（兼容性）加入视觉回归子项 |

### 第十步 · 产出 UI-DESIGN.md

路径：`docs/andao_specs/<change-id>/UI-DESIGN.md`

包含 frontmatter design tokens + 五维决策 + 关键组件规范 + 占位符策略 + 反 AI-slop 自检结果。

### 第十一步 · 确认完成

```
✅ UI-DESIGN.md 已生成（docs/andao_specs/<change-id>/UI-DESIGN.md）
Design Tokens：X 个
关键组件：X 个

准备进入下一阶段：slice（任务拆解）
继续吗？
```

用户确认 → 路由到 `slice`。

## Restyle 模式（视觉风格切换）

触发条件：用户说"换风格" / "restyle" / "改视觉"，且项目中已有 UI-DESIGN.md 或存在 UI 代码。

从 `ask-andao` 路由进入（路由表中"换风格"关键词指向 `ui-design` 的 Restyle 模式）。

### 流程

#### 第一步 · 识别当前风格 v1

从以下来源反向提取当前设计：

| 来源优先级 | 位置 |
|-----------|------|
| 1（最高） | 已有 UI-DESIGN.md |
| 2 | CSS 变量 / 主题文件 |
| 3 | 运行时 tokens（grep --color / --font- / --space-） |
| 4 | 组件代码直接读取（作为降级方案） |

输出当前风格摘要：
```
当前风格 v1：
- 字体：Inter
- 主色：OKLCH(55% 0.15 260)
- 圆角：8px
- 间距基准：4px
```

#### 第二步 · 候选风格 + 切换成本

展示候选风格卡片（参考原 flow-x 的 9 张风格卡片）让用户选择。

每张卡片含：
- 风格名称
- 一句话描述
- 切换成本估算（小/中/大）

成本估算因子：

| 因子 | 权重 |
|------|------|
| tokens 数量 | +1 per 5 tokens |
| 组件数量 | +1 per 3 components |
| 是否涉及全局布局 | +3 |
| 是否有对照参考 | -2（有参考） |

#### 第三步 · 影响面扫描

定义动/不动清单：

| 清单 | 内容 |
|------|------|
| ✅ 动 | tokens、组件视觉样式、CSS 变量 |
| ❌ 不动 | props/接口、业务逻辑、路由、测试、数据层 |

#### 第四步 · 产出 UI-DESIGN.md v2

含 v1→v2 视觉对比表：

```
| 维度 | v1 | v2 |
|------|----|----|
| 字体 | Inter | Plus Jakarta Sans |
| 主色 | OKLCH(55% 0.15 260) | OKLCH(50% 0.18 30) |
| 圆角 | 8px | 12px |
```

#### 第五步 · 三波次拆解任务

| 波次 | 内容 | 可并行 |
|------|------|--------|
| Wave 1 | Design Tokens 替换 | [P] |
| Wave 2 | 组件视觉更新 | [P] |
| Wave 3 | 回归检查（diff + 截图） | — |

任务写入 TASK.md（和 `slice` 阶段互动——如果 pipeline 正在进行，追加到现有 TASK.md；否则直接产任务列表）。

#### 第六步 · 风险披露

提示用户：

```
⚠️ Restyle 风险：
1. 用户感知：现有用户会看到视觉突变
2. 测试影响：截图测试基线需要全部更新
3. 性能：新 tokens 可能影响渲染性能
4. 无障碍：新颜色对比度需要验证
是否继续？
```

### 约束

- 触及业务逻辑/路由/测试逻辑 → 立即停止
- 一次 restyle 只切换一个风格（不允许跨风格覆盖）
- 不动清单中的项目严禁修改
