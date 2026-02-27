# GitHub 仓库配置指南

## 一、创建 GitHub 仓库

### 1.1 在 GitHub 上创建新仓库

1. 访问 https://github.com/new
2. 填写仓库名称（例如：`selfish-blog`）
3. 选择公开（Public）或私有（Private）
4. **不要**勾选 "Initialize this repository with a README"
5. 点击 "Create repository"

### 1.2 关联远程仓库

```bash
# 添加远程仓库（替换为你的仓库地址）
git remote add origin https://github.com/你的用户名/你的仓库名.git

# 或者使用 SSH
git remote add origin git@github.com:你的用户名/你的仓库名.git

# 验证远程仓库
git remote -v

# 推送到 GitHub
git branch -M main
git push -u origin main
```

---

## 二、配置 GitHub SSH（推荐）

### 2.1 生成 SSH 密钥

```bash
# 生成新的 SSH 密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 按提示操作，一般直接回车即可
```

### 2.2 添加 SSH 密钥到 GitHub

1. 复制公钥内容
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

2. 访问 https://github.com/settings/keys

3. 点击 "New SSH key"

4. 填写标题（如 "My Laptop"）

5. 粘贴公钥内容

6. 点击 "Add SSH key"

### 2.3 测试 SSH 连接

```bash
ssh -T git@github.com
```

看到 "Hi username! You've successfully authenticated" 表示成功。

---

## 三、GitHub Actions 配置

### 3.1 创建 CI 工作流

创建 `.github/workflows/ci.yml`：

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Run lint
        run: pnpm lint

  test:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Run tests
        run: pnpm test
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
```

### 3.2 目录结构

```
project1/
├── .github/
│   └── workflows/
│       ├── ci.yml          # CI 工作流
│       └── deploy.yml      # 部署工作流
├── docs/
├── frontend/
├── backend/
└── ...
```

---

## 四、GitHub Pages 部署（可选）

### 4.1 配置 GitHub Actions 部署

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Build
        run: pnpm build
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v3
```

### 4.2 启用 GitHub Pages

1. 访问仓库 Settings
2. 点击 Pages
3. 在 "Source" 下选择 "GitHub Actions"
4. 保存

---

## 五、仓库设置建议

### 5.1 分支保护

1. 访问 Settings → Branches
2. 点击 "Add branch protection rule"
3. 分支名称模式：`main`
4. 勾选：
   - Require a pull request before merging
   - Require status checks to pass before merging
   - Require branches to be up to date before merging

### 5.2 Issue 模板

创建 `.github/ISSUE_TEMPLATE/bug-report.md`：

```markdown
---
name: Bug Report
about: 报告一个 bug
title: '[BUG] '
labels: bug
---

## 问题描述


## 复现步骤


## 期望行为


## 实际行为


## 环境信息
- OS: 
- Node.js: 
- 浏览器：
```

### 5.3 Pull Request 模板

创建 `.github/pull_request_template.md`：

```markdown
## 变更描述


## 关联 Issue


## 测试清单
- [ ] 代码已通过 ESLint
- [ ] 已添加/更新测试
- [ ] 已更新文档
- [ ] 本地测试通过

## 截图（如适用）

```

---

## 六、推送代码到 GitHub

### 6.1 使用 HTTPS

```bash
# 添加远程仓库
git remote add origin https://github.com/你的用户名/你的仓库名.git

# 推送
git push -u origin main
```

### 6.2 使用 SSH

```bash
# 添加远程仓库
git remote add origin git@github.com:你的用户名/你的仓库名.git

# 推送
git push -u origin main
```

### 6.3 后续推送

```bash
# 推送到 main 分支
git push

# 或者指定分支
git push origin develop
```

---

## 七、常用命令

```bash
# 查看远程仓库
git remote -v

# 修改远程仓库
git remote set-url origin https://github.com/新地址.git

# 拉取最新代码
git pull origin main

# 推送代码
git push origin main

# 查看分支
git branch -a

# 切换分支
git checkout develop
```

---

## 八、检查清单

- [ ] GitHub 仓库已创建
- [ ] 远程仓库已关联
- [ ] SSH 密钥已配置（如使用 SSH）
- [ ] 代码已推送
- [ ] GitHub Actions 已配置
- [ ] 分支保护已设置
- [ ] Issue/PR 模板已创建
