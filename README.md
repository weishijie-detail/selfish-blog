# 个人博客系统

一个基于 Vue 3 和 NestJS 的现代化个人博客系统。

## 技术栈

### 前端
- **框架**: Vue 3 + TypeScript
- **构建工具**: Vite
- **UI 组件**: Element Plus / Naive UI
- **状态管理**: Pinia
- **路由**: Vue Router 4
- **HTTP**: Axios
- **样式**: Tailwind CSS

### 后端
- **框架**: NestJS
- **ORM**: Prisma
- **数据库**: SQLite
- **认证**: JWT + bcrypt

### 测试
- **E2E 测试**: Playwright
- **单元测试**: Vitest (前端) / Jest (后端)

## 项目结构

```
project1/
├── docs/                      # 文档目录
│   ├── development-environment-setup.md  # 开发环境配置
│   ├── blog-features.md                  # 功能清单
│   ├── code-acceptance-standards.md      # 代码验收标准
│   ├── git-commit-guide.md               # Git 提交规范
│   └── implementation/                   # 实现与测试文档
│       ├── README.md
│       ├── 01-frontend-home.md
│       ├── 02-frontend-article-detail.md
│       ├── 03-backend-auth.md
│       └── 04-backend-article.md
├── frontend/                    # 前端项目
├── backend/                     # 后端项目
└── README.md                    # 项目说明
```

## 快速开始

### 环境要求
- Node.js >= 20
- pnpm >= 8
- Git

### 安装依赖

```bash
# 前端
cd frontend && pnpm install

# 后端
cd backend && pnpm install
```

### 开发

```bash
# 前端开发
cd frontend && pnpm dev

# 后端开发
cd backend && pnpm dev
```

### 测试

```bash
# 单元测试
pnpm test

# E2E 测试
pnpm test:e2e

# 覆盖率
pnpm test:coverage
```

### 构建

```bash
# 前端构建
cd frontend && pnpm build

# 后端构建
cd backend && pnpm build
```

## 文档

- [开发环境配置](docs/development-environment-setup.md)
- [功能清单](docs/blog-features.md)
- [实现与测试文档](docs/implementation/README.md)
- [代码验收标准](docs/code-acceptance-standards.md)
- [Git 提交规范](docs/git-commit-guide.md)

## 功能特性

### 前台功能
- 首页文章列表
- 文章详情（Markdown 渲染、代码高亮）
- 分类/标签筛选
- 关于页面
- 友情链接

### 后台功能
- 用户认证（JWT）
- 文章管理（CRUD、发布、置顶）
- 分类管理
- 标签管理
- 系统设置
- 用户管理

## 开发计划

### 第一阶段（MVP）
- [x] 环境配置
- [x] 文档设计
- [ ] 文章 CRUD
- [ ] 分类/标签管理
- [ ] 前台展示
- [ ] 用户认证

### 第二阶段
- [ ] 系统设置
- [ ] 数据统计
- [ ] 权限管理
- [ ] 日志管理

### 第三阶段
- [ ] 第三方登录
- [ ] 主题切换
- [ ] 数据备份

## 许可证

MIT
