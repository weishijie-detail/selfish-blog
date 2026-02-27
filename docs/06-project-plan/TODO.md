# 待办文档清单

> **注意**：本文档已按功能垂直切片开发模式重新组织，每个 Sprint 包含完整的前后端实现和测试。
> 
> 详细开发优先级请查看：[`development-priority.md`](./development-priority.md)
> 
> **文档目录结构**：
> - [`01-getting-started/`](../01-getting-started/) - 入门指南
> - [`02-specifications/`](../02-specifications/) - 规范文档
> - [`03-design/`](../03-design/) - 设计文档
> - [`04-implementation/`](../04-implementation/) - 实现文档
> - [`05-deployment/`](../05-deployment/) - 部署文档
> - [`06-project-plan/`](../06-project-plan/) - 项目计划

---

## P0 - 必须编写（第一阶段 MVP）

### Sprint 0 - 基础架构

| 文档 | 内容 | 状态 |
|------|------|------|
| [`database-schema.md`](../03-design/database-schema.md) | 数据库 ER 图、表结构设计 | ✅ 已完成 |
| [`frontend-structure.md`](../03-design/frontend-structure.md) | 前端项目目录结构、模块划分 | ✅ 已完成 |
| [`backend-structure.md`](../03-design/backend-structure.md) | 后端项目目录结构、模块划分 | ✅ 已完成 |

### Sprint 1 - 用户认证模块

| 文档 | 内容 | 状态 |
|------|------|------|
| [`03-backend-auth.md`](../04-implementation/03-backend-auth.md) | 登录 API、JWT 认证、路由守卫 | ✅ 已完成 |

### Sprint 2 - 分类管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `06-backend-category.md` | 分类 CRUD、分类列表页、表单 | 待编写 |

### Sprint 3 - 标签管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `07-backend-tag.md` | 标签 CRUD、标签列表页、标签云 | 待编写 |

### Sprint 4 - 文章管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| [`04-backend-article.md`](../04-implementation/04-backend-article.md) | 文章 CRUD、Markdown 编辑器、发布/置顶 | ✅ 已完成 |

### Sprint 5 - 前台首页模块

| 文档 | 内容 | 状态 |
|------|------|------|
| [`01-frontend-home.md`](../04-implementation/01-frontend-home.md) | 文章列表、分页、精选文章 | ✅ 已完成 |

### Sprint 6 - 前台文章详情模块

| 文档 | 内容 | 状态 |
|------|------|------|
| [`02-frontend-article-detail.md`](../04-implementation/02-frontend-article-detail.md) | Markdown 渲染、点赞、目录导航 | ✅ 已完成 |

### Sprint 7 - 前台分类/标签页模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `05-frontend-category-tag.md` | 分类筛选、标签云、分类/标签详情页 | 待编写 |

### Sprint 8 - 前台关于页面

| 文档 | 内容 | 状态 |
|------|------|------|
| `08-frontend-about.md` | 博主信息、技能栈、社交链接 | 待编写 |

### Sprint 9 - 友情链接模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `09-frontend-links.md` | 友情链接展示 | 待编写 |

---

## P1 - 重要文档（第二阶段增强功能）

### Sprint 10 - 系统设置模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `11-backend-settings.md` | 站点配置、SEO 设置 | 待编写 |

### Sprint 11 - 仪表盘模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `10-backend-dashboard.md` | 统计数据、访问趋势图表 | 待编写 |

### Sprint 12 - 用户管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `12-backend-user.md` | 用户 CRUD、角色管理 | 待编写 |

### Sprint 13 - 权限管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `13-backend-rbac.md` | RBAC 权限控制、菜单权限 | 待编写 |

### Sprint 14 - 日志管理模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `14-backend-logger.md` | 日志记录、日志查询 | 待编写 |

### Sprint 15 - 数据统计模块

| 文档 | 内容 | 状态 |
|------|------|------|
| `15-backend-statistics.md` | 访问统计、文章统计 | 待编写 |

### 设计规范文档

| 文档 | 内容 | 状态 |
|------|------|------|
| [`api-design.md`](../03-design/api-design.md) | RESTful API 设计规范、接口列表 | ✅ 已完成 |
| [`api-response-format.md`](../03-design/api-response-format.md) | 统一响应格式、错误码定义 | ✅ 已完成 |
| [`router-design.md`](../03-design/router-design.md) | 路由设计、路由守卫配置 | ✅ 已完成 |
| [`state-management.md`](../03-design/state-management.md) | Pinia Store 设计规范 | ✅ 已完成 |
| `component-design.md` | 组件设计规范 | 待编写 |
| `ui-design-system.md` | UI 设计规范 | 待编写 |

### 部署配置文档

| 文档 | 内容 | 状态 |
|------|------|------|
| `deployment-guide.md` | 生产环境部署指南 | 待编写 |
| `docker-setup.md` | Docker 容器化部署 | 待编写 |
| `ci-cd-setup.md` | CI/CD 配置 | 待编写 |
| `server-config.md` | 服务器配置（Nginx、PM2） | 待编写 |

---

## P2 - 可选文档（第三阶段或之后）

| 文档 | 内容 | 状态 |
|------|------|------|
| `16-backend-links.md` | 友链管理后台 | 待编写 |
| `17-backend-backup.md` | 数据备份 | 待编写 |
| `18-frontend-theme.md` | 主题切换 | 待编写 |
| `monitoring-setup.md` | 监控配置 | 待编写 |
| `troubleshooting.md` | 问题排查指南 | 待编写 |
| `backup-restore.md` | 备份恢复操作手册 | 待编写 |

---

## 文档状态汇总

| 类别 | 已完成 | 待编写 | 总计 |
|------|--------|--------|------|
| 设计文档 | 7 | 2 | 9 |
| 实现文档 | 4 | 12 | 16 |
| 部署文档 | 0 | 4 | 4 |
| **总计** | **11** | **18** | **29** |

---

## 开发优先级说明

详细开发优先级和 Sprint 划分请查看：[`development-priority.md`](./development-priority.md)

### 第一阶段（MVP）开发顺序

```
Sprint 0 (基础架构)
    ↓
Sprint 1 (用户认证)
    ↓
Sprint 2 (分类管理)
    ↓
Sprint 3 (标签管理)
    ↓
Sprint 4 (文章管理)
    ↓
Sprint 5 (前台首页)
    ↓
Sprint 6 (文章详情)
    ↓
Sprint 7 (分类/标签页)
    ↓
Sprint 8 (关于页面)
    ↓
Sprint 9 (友情链接)
```

### 第二阶段（增强功能）开发顺序

```
Sprint 10 (系统设置)
    ↓
Sprint 11 (仪表盘)
    ↓
Sprint 12 (用户管理)
    ↓
Sprint 13 (权限管理)
    ↓
Sprint 14 (日志管理)
    ↓
Sprint 15 (数据统计)
```
