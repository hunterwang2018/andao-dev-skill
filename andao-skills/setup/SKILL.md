---
name: setup
description: 工具配置。安装 pre-commit hooks + git guardrails。
---

# setup — 工具配置

## Goal

1. 检测包管理器
2. 配置 pre-commit hooks（Husky + lint-staged）
3. 配置 git guardrails

## Workflow

### 第一步 · 检测包管理器

| 文件 | 管理器 |
|------|--------|
| package-lock.json | npm |
| yarn.lock | yarn |
| pnpm-lock.yaml | pnpm |
| 无 | 提示"未检测到 Node.js 项目，跳过 pre-commit" |

### 第二步 · 安装 Husky + lint-staged

```bash
npx husky init
npm install --save-dev lint-staged
```

配置 `package.json`：

```json
{
  "lint-staged": {
    "*.{js,ts,tsx,vue}": ["prettier --write"],
    "*.{css,scss,md}": ["prettier --write"]
  }
}
```

配置 `.husky/pre-commit`：

```bash
npx lint-staged
npm run typecheck
npm test
```

### 第三步 · 确认

```
✅ Pre-commit hooks 已配置
  格式化：Prettier
  类型检查：typecheck
  测试：test
```

### 第四步 · 配置 git guardrails

问用户：
```
是否安装 git guardrails 防止危险 git 操作（如 push / reset --hard / branch -D）？
1. 安装
2. 跳过
```

安装：
```bash
# 复制 guardrail 脚本到 .git/hooks/pre-exec
# 配置危险命令黑名单
```

## 约束

- 不修改已有 lint-staged 配置（追加而非覆盖）
- 用户确认每一步
