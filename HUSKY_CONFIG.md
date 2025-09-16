# Husky Git Hooks 配置文档

本项目已配置了 Husky Git Hooks，用于在提交代码时自动执行代码质量检查。

## 🚀 已安装的包

- **husky** - Git hooks 管理工具
- **@commitlint/cli** - 提交信息校验工具
- **@commitlint/config-conventional** - 传统提交信息格式配置
- **lint-staged** - 对暂存文件运行 lint 工具

## 📁 配置文件

### 1. Husky Hooks

#### `.husky/pre-commit`

- **触发时机**: 执行 `git commit` 时
- **执行内容**: 运行 `npx lint-staged`
- **作用**: 对暂存的文件进行 ESLint 检查和 Prettier 格式化

#### `.husky/commit-msg`

- **触发时机**: 提交信息输入完成后
- **执行内容**: 运行 `npx --no -- commitlint --edit $1`
- **作用**: 验证提交信息是否符合约定式提交格式

### 2. Lint-staged 配置 (package.json)

```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx,vue}": ["eslint --fix", "prettier --write"],
    "*.{json,md,yml,yaml,css,scss,html}": ["prettier --write"]
  }
}
```

### 3. Commitlint 配置 (commitlint.config.js)

使用 Conventional Commits 规范，支持以下提交类型：

- **feat**: 新功能
- **fix**: 修复
- **docs**: 文档
- **style**: 格式（不影响代码运行的变动）
- **refactor**: 重构
- **perf**: 性能优化
- **test**: 增加测试
- **chore**: 构建过程或辅助工具的变动
- **build**: 构建系统或外部依赖项的更改
- **ci**: 持续集成配置文件和脚本的更改
- **revert**: 回滚

## 📝 提交信息格式

### 基本格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 示例

```bash
feat(map): add cesium map component

Add a new MapView component that integrates Cesium.js
for 3D geospatial visualization.

Closes #123
```

### 简短格式示例

```bash
git commit -m "fix: resolve router path issue"
git commit -m "feat(ui): add new button component"
git commit -m "docs: update README with setup instructions"
```

## 🛠️ 可用脚本

### 手动运行

```bash
# 手动运行 lint-staged
pnpm pre-commit

# 手动验证提交信息
echo "feat: add new feature" | pnpm commitlint
```

### 绕过 Hooks（不推荐）

```bash
# 跳过 pre-commit hook
git commit --no-verify -m "message"

# 跳过所有 hooks
git commit --no-verify --no-edit
```

## 🔄 工作流程

### 正常提交流程

1. **修改文件** → 编辑代码
2. **暂存文件** → `git add .`
3. **提交代码** → `git commit -m "feat: add new feature"`
4. **自动执行**:
   - **pre-commit**: 对暂存文件运行 ESLint + Prettier
   - **commit-msg**: 验证提交信息格式
5. **提交成功** → 代码已提交到 Git

### 如果检查失败

- **ESLint 错误**: 修复代码质量问题后重新提交
- **格式问题**: Prettier 会自动修复，需要重新 `git add` 并提交
- **提交信息格式错误**: 使用正确的格式重新提交

## ✅ 验证配置

### 测试 lint-staged

```bash
# 修改一个文件
echo "console.log('test')" >> src/test.js

# 暂存文件
git add src/test.js

# 测试 lint-staged
npx lint-staged
```

### 测试 commitlint

```bash
# 测试有效的提交信息
echo "feat: add new feature" | npx commitlint

# 测试无效的提交信息
echo "invalid message" | npx commitlint
```

## 🎯 最佳实践

1. **小而频繁的提交**: 每个提交专注于一个功能或修复
2. **清晰的提交信息**: 使用约定式提交格式
3. **提交前自测**: 确保代码在本地正常运行
4. **修复 lint 错误**: 不要绕过代码质量检查
5. **保持代码整洁**: 让 Prettier 自动格式化代码

## 🚫 常见问题

### Q: 提交时卡住了怎么办？

A: 检查是否有 ESLint 错误需要手动修复，或者终端是否需要输入。

### Q: 能否跳过 hooks？

A: 技术上可以使用 `--no-verify`，但不推荐，因为这会跳过代码质量检查。

### Q: 提交信息格式要求太严格？

A: 这有助于生成更好的变更日志和团队协作，建议遵循规范。

配置完成！现在每次提交都会自动进行代码质量检查和格式化。🎉
