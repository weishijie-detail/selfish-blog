# Git 提交规范

## 一、Commit Message 格式

采用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 1.1 Type 类型

| 类型 | 描述 |
|------|------|
| `feat` | 新功能 |
| `fix` | 修复 bug |
| `docs` | 文档变更 |
| `style` | 代码格式（不影响代码运行） |
| `refactor` | 重构（既不是新功能也不是 bug 修复） |
| `perf` | 性能优化 |
| `test` | 测试相关 |
| `chore` | 构建过程或辅助工具变动 |
| `ci` | CI 配置变更 |
| `revert` | 回滚提交 |

### 1.2 Scope 范围

| 范围 | 描述 |
|------|------|
| `frontend` | 前端相关 |
| `backend` | 后端相关 |
| `docs` | 文档相关 |
| `config` | 配置相关 |
| `deps` | 依赖相关 |
| `auth` | 认证模块 |
| `article` | 文章模块 |
| `category` | 分类模块 |
| `tag` | 标签模块 |
| `user` | 用户模块 |
| `home` | 首页 |
| `ui` | UI 组件 |

### 1.3 Subject 主题

- 简洁描述变更内容
- 使用现在时态（如 "add" 而非 "added"）
- 首字母不大写
- 结尾不加句号

---

## 二、提交示例

### 2.1 新功能

```bash
git commit -m "feat(frontend): 添加首页文章列表组件"
git commit -m "feat(backend): 实现文章 CRUD API"
git commit -m "feat(auth): 添加 JWT 认证功能"
```

### 2.2 Bug 修复

```bash
git commit -m "fix(frontend): 修复文章详情页点赞计数不更新"
git commit -m "fix(backend): 修复分类筛选查询错误"
```

### 2.3 文档变更

```bash
git commit -m "docs: 更新 README 安装说明"
git commit -m "docs(api): 添加文章管理 API 文档"
git commit -m "docs(test): 补充测试用例文档"
```

### 2.4 重构

```bash
git commit -m "refactor(auth): 重构认证服务代码结构"
git commit -m "refactor(frontend): 提取公共组件"
```

### 2.5 性能优化

```bash
git commit -m "perf(frontend): 优化首页加载速度"
git commit -m "perf(backend): 优化数据库查询性能"
```

### 2.6 测试相关

```bash
git commit -m "test(frontend): 添加首页 E2E 测试用例"
git commit -m "test(backend): 添加认证服务单元测试"
```

### 2.7 配置变更

```bash
git commit -m "chore(config): 更新 ESLint 配置"
git commit -m "chore(deps): 升级 Vue 到 3.4.0"
```

---

## 三、完整提交示例

```bash
feat(frontend): 添加首页文章列表组件

实现首页文章列表展示功能
- 添加 ArticleList 组件
- 添加 ArticleCard 组件
- 实现分页功能
- 添加加载状态处理

Closes #12
```

---

## 四、分支命名规范

### 4.1 分支类型

| 分支 | 命名 | 描述 |
|------|------|------|
| 主分支 | `main` | 生产环境代码 |
| 开发分支 | `develop` | 开发环境代码 |
| 功能分支 | `feature/xxx` | 新功能开发 |
| 修复分支 | `fix/xxx` | Bug 修复 |
| 热修复分支 | `hotfix/xxx` | 生产环境紧急修复 |
| 文档分支 | `docs/xxx` | 文档更新 |

### 4.2 分支示例

```bash
# 功能分支
git checkout -b feature/home-page
git checkout -b feature/article-management
git checkout -b feature/user-auth

# 修复分支
git checkout -b fix/login-error
git checkout -b fix/article-delete

# 文档分支
git checkout -b docs/readme-update
git checkout -b docs/api-docs
```

---

## 五、标签命名规范

### 5.1 版本标签

```bash
# 格式：v<主版本>.<次版本>.<修订版本>
git tag -a v1.0.0 -m "发布 1.0.0 版本"
git tag -a v1.1.0 -m "发布 1.1.0 版本"
git tag -a v1.0.1 -m "修复 1.0.1 版本"
```

### 5.2 里程碑标签

```bash
git tag -a milestone-mvp -m "MVP 版本完成"
git tag -a milestone-beta -m "Beta 版本完成"
```

---

## 六、提交前检查清单

```markdown
## 提交前检查

- [ ] 代码已通过 ESLint 检查
- [ ] 代码已通过 Prettier 格式化
- [ ] 单元测试已运行并通过
- [ ] 相关测试已添加
- [ ] 文档已更新
- [ ] Commit Message 符合规范
```

---

## 七、Git 配置

### 7.1 全局配置

```bash
# 配置用户信息
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 配置默认分支
git config --global init.defaultBranch main

# 配置自动拉取
git config --global pull.rebase false
```

### 7.2 项目配置

```bash
# 配置提交模板
git config commit.template .gitmessage
```

### 7.3 提交模板（.gitmessage）

```
# <type>(<scope>): <subject>
# |<----  使用 50 个字符  ---->|

# 为什么需要这个变更？与当前状态有何不同？

# |<----   使用 72 个字符   ---->|

# 关联 Issue
# Closes #123
```

---

## 八、常用 Git 命令

### 8.1 日常操作

```bash
# 查看状态
git status

# 添加文件
git add .
git add docs/

# 提交
git commit -m "feat: 添加新功能"

# 推送
git push origin feature/xxx

# 拉取
git pull origin develop
```

### 8.2 分支操作

```bash
# 创建分支
git checkout -b feature/xxx

# 切换分支
git checkout develop

# 合并分支
git merge feature/xxx

# 删除分支
git branch -d feature/xxx
```

### 8.3 查看历史

```bash
# 查看提交历史
git log --oneline

# 查看图形历史
git log --graph --oneline --all

# 查看文件变更
git show <commit-hash>
```
