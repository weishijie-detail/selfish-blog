# 个人博客系统文档索引

> 本文档提供了个人博客系统所有文档的导航和概述。

---

## 📁 文档目录结构

```
docs/
├── 01-getting-started/       # 入门指南
├── 02-specifications/        # 规范文档
├── 03-design/               # 设计文档
├── 04-implementation/       # 实现文档
├── 05-deployment/           # 部署文档
└── 06-project-plan/         # 项目计划
```

---

## 📖 文档列表

### 01 - 入门指南 (Getting Started)

| 文档 | 说明 |
|------|------|
| [`development-environment-setup.md`](01-getting-started/development-environment-setup.md) | 开发环境搭建指南 |
| [`github-setup-guide.md`](01-getting-started/github-setup-guide.md) | GitHub 配置指南 |

### 02 - 规范文档 (Specifications)

| 文档 | 说明 |
|------|------|
| [`blog-features.md`](02-specifications/blog-features.md) | 博客功能规格说明 |
| [`code-acceptance-standards.md`](02-specifications/code-acceptance-standards.md) | 代码验收标准 |
| [`git-commit-guide.md`](02-specifications/git-commit-guide.md) | Git 提交规范 |

### 03 - 设计文档 (Design)

| 文档 | 说明 |
|------|------|
| [`database-schema.md`](03-design/database-schema.md) | 数据库 ER 图和表结构设计 |
| [`api-design.md`](03-design/api-design.md) | RESTful API 设计 |
| [`api-response-format.md`](03-design/api-response-format.md) | API 响应格式和错误码 |
| [`frontend-structure.md`](03-design/frontend-structure.md) | 前端项目结构设计 |
| [`backend-structure.md`](03-design/backend-structure.md) | 后端项目结构设计 |
| [`router-design.md`](03-design/router-design.md) | 前端和后端路由设计 |
| [`state-management.md`](03-design/state-management.md) | Pinia 状态管理设计 |
| [`development-conventions.md`](03-design/development-conventions.md) | 开发规范约定 |

### 04 - 实现文档 (Implementation)

| 文档 | 说明 |
|------|------|
| [`README.md`](04-implementation/README.md) | 实现文档概述 |
| [`01-frontend-home.md`](04-implementation/01-frontend-home.md) | 前台首页实现 |
| [`02-frontend-article-detail.md`](04-implementation/02-frontend-article-detail.md) | 文章详情页实现 |
| [`03-backend-auth.md`](04-implementation/03-backend-auth.md) | 后端认证模块实现 |
| [`04-backend-article.md`](04-implementation/04-backend-article.md) | 后端文章模块实现 |

### 05 - 部署文档 (Deployment)

> 待创建

| 文档 | 说明 |
|------|------|
| `docker-deployment.md` | Docker 部署指南 |
| `github-actions-ci.md` | GitHub Actions CI/CD 配置 |

### 06 - 项目计划 (Project Plan)

| 文档 | 说明 |
|------|------|
| [`development-priority.md`](06-project-plan/development-priority.md) | 开发优先级和 Sprint 计划 |
| [`TODO.md`](06-project-plan/TODO.md) | 任务清单和进度跟踪 |

---

## 🚀 快速开始

1. **新手入门**：阅读 [`01-getting-started/development-environment-setup.md`](01-getting-started/development-environment-setup.md) 搭建开发环境

2. **了解功能**：阅读 [`02-specifications/blog-features.md`](02-specifications/blog-features.md) 了解系统功能

3. **查看设计**：阅读 [`03-design/`](03-design/) 目录下的设计文档

4. **开始开发**：按照 [`06-project-plan/development-priority.md`](06-project-plan/development-priority.md) 的优先级进行开发

5. **参考实现**：查看 [`04-implementation/`](04-implementation/) 目录下的实现文档

---

## 📝 文档更新记录

| 日期 | 更新内容 |
|------|----------|
| 2026-02-27 | 重组文档目录结构，创建设计文档 |
| 2026-02-26 | 创建开发优先级文档 |
| 2026-02-25 | 创建初始文档 |

---

## 🔗 相关链接

- [项目根目录 README](../README.md)
- [GitHub 仓库](https://github.com/weishijie-detail/selfish-blog)
