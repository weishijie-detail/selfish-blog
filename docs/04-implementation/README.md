# 个人博客实现与测试文档索引

## 文档目录

### 环境配置
- [`../development-environment-setup.md`](../development-environment-setup.md) - 开发环境配置清单
- [`../blog-features.md`](../blog-features.md) - 博客功能清单

### 功能实现与测试文档

| 模块 | 文档 | 描述 |
|------|------|------|
| 前台 - 首页 | [`01-frontend-home.md`](01-frontend-home.md) | 首页实现与测试（文章列表、分页、精选文章） |
| 前台 - 文章详情 | [`02-frontend-article-detail.md`](02-frontend-article-detail.md) | 文章详情页实现与测试（Markdown 渲染、点赞、目录） |
| 后台 - 用户认证 | [`03-backend-auth.md`](03-backend-auth.md) | 认证模块实现与测试（登录、JWT、权限） |
| 后台 - 文章管理 | [`04-backend-article.md`](04-backend-article.md) | 文章管理实现与测试（CRUD、发布、置顶） |

### 待补充文档

| 模块 | 描述 | 状态 |
|------|------|------|
| 前台 - 分类/标签 | 分类和标签页面实现 | 待编写 |
| 前台 - 关于页面 | 关于页面实现 | 待编写 |
| 后台 - 分类管理 | 分类 CRUD 操作 | 待编写 |
| 后台 - 标签管理 | 标签 CRUD 操作 | 待编写 |
| 后台 - 系统设置 | 站点配置、SEO 设置 | 待编写 |
| 后台 - 用户管理 | 用户列表、角色管理 | 待编写 |
| 系统 - 权限管理 | RBAC 权限控制 | 待编写 |
| 系统 - 日志管理 | 操作日志、访问日志 | 待编写 |
| 系统 - 数据统计 | PV/UV 统计、文章统计 | 待编写 |

---

## 技术栈总览

### 前端
- **框架**: Vue 3 + TypeScript
- **构建工具**: Vite
- **UI 组件**: Element Plus / Naive UI
- **状态管理**: Pinia
- **路由**: Vue Router 4
- **HTTP**: Axios
- **样式**: Tailwind CSS
- **测试**: Vitest + Playwright

### 后端
- **框架**: NestJS
- **ORM**: Prisma
- **数据库**: SQLite
- **认证**: JWT + bcrypt
- **API 文档**: Swagger
- **测试**: Jest + Supertest + Playwright

---

## 测试用例总览

### E2E 测试覆盖

| 功能模块 | 测试用例数 | 优先级 |
|----------|------------|--------|
| 首页 | 5 | P0 |
| 文章详情 | 6 | P0 |
| 用户认证 | 6 | P0 |
| 文章管理 | 7 | P0 |
| 分类管理 | 5 | P0 |
| 标签管理 | 5 | P0 |
| 系统设置 | 4 | P1 |
| 用户管理 | 4 | P1 |

### 单元测试覆盖

| 模块 | 文件 | 覆盖率目标 |
|------|------|------------|
| 认证服务 | auth.service.spec.ts | 90% |
| 文章服务 | article.service.spec.ts | 90% |
| 用户服务 | user.service.spec.ts | 90% |
| 前端组件 | *.spec.ts | 80% |

---

## 快速开始

### 运行 E2E 测试
```bash
# 安装依赖
pnpm install

# 启动测试服务器
pnpm test:e2e

# 或单独运行某个测试
pnpm test:e2e -- home.spec.ts
```

### 运行单元测试
```bash
# 前端单元测试
cd frontend && pnpm test

# 后端单元测试
cd backend && pnpm test
```

### 生成测试报告
```bash
# 生成覆盖率报告
pnpm test:coverage
```

---

## 开发流程

1. **阅读功能清单** - 了解需要实现的功能
2. **查看实现文档** - 参考技术实现和代码示例
3. **编写代码** - 按照文档实现功能
4. **编写测试** - 根据测试文档编写 E2E 和单元测试
5. **运行测试** - 确保所有测试通过
6. **验收** - 对照验收标准检查

---

## 文档更新记录

| 日期 | 文档 | 更新内容 |
|------|------|----------|
| 2026-02-27 | 所有文档 | 初始版本创建 |
