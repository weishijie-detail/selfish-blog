# Web 个人博客开发环境推荐清单

## 一、开发环境推荐

### 1. 前端技术栈

| 项目 | 推荐 | 说明 |
|------|------|------|
| **框架** | Vue 3 + Vite | 轻量、快速、易上手，适合个人博客 |
| **UI 组件库** | Element Plus / Naive UI | 丰富的组件，快速搭建界面 |
| **CSS 框架** | Tailwind CSS | 原子化 CSS，提高开发效率 |
| **状态管理** | Pinia | Vue 3 官方推荐，比 Vuex 更简洁 |
| **路由** | Vue Router 4 | Vue 3 官方路由 |
| **HTTP 客户端** | Axios | 成熟的 HTTP 请求库 |
| **TypeScript** | 推荐启用 | 类型安全，减少 bug |

### 2. 后端技术栈

| 项目 | 推荐 | 说明 |
|------|------|------|
| **运行时** | Node.js 20 LTS | 长期支持版本，稳定可靠 |
| **框架** | NestJS / Express | NestJS 架构清晰，Express 轻量灵活 |
| **ORM** | Prisma / TypeORM | Prisma 类型安全，开发体验好 |
| **数据库** | SQLite / PostgreSQL | SQLite 轻量无需配置，PostgreSQL 功能强大 |
| **认证** | JWT + Passport | 成熟的认证方案 |
| **API 文档** | Swagger / OpenAPI | 自动生成 API 文档 |

### 3. 开发工具

| 项目 | 推荐 | 说明 |
|------|------|------|
| **包管理器** | pnpm | 快速、节省磁盘空间 |
| **版本控制** | Git | 必备版本控制工具 |
| **数据库管理** | DB Browser for SQLite / DBeaver | 可视化数据库管理 |
| **API 测试** | Postman / Apifox | API 调试工具 |
| **终端** | Windows Terminal | 更好的终端体验 |

### 4. 部署环境

| 项目 | 推荐 | 说明 |
|------|------|------|
| **容器化** | Docker + Docker Compose | 一键部署，环境隔离 |
| **Web 服务器** | Nginx | 反向代理，静态资源服务 |
| **PM2** | PM2 | Node.js 进程管理 |
| **CI/CD** | GitHub Actions | 自动化构建部署 |

---

## 二、VSCode 插件推荐清单

### 1. 核心开发插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **Vue - Official** | Vue.js | Vue 3 官方支持，语法高亮、智能提示 |
| **Volar** | Vue.js | Vue 语言支持（已合并到 Vue - Official） |
| **TypeScript Vue Plugin (Volar)** | Vue.js | TS 支持 |
| **ESLint** | Microsoft | 代码检查和格式化 |
| **Prettier - Code formatter** | Prettier | 代码格式化 |
| **Auto Rename Tag** | Jun Han | HTML/XML 标签自动重命名 |
| **Auto Close Tag** | Jun Han | HTML/XML 标签自动闭合 |

### 2. 后端开发插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **NestJS Snippets** | NestJS | NestJS 代码片段 |
| **Prisma** | Prisma | Prisma ORM 支持 |
| **REST Client** | Huachao Mao | 在 VSCode 中测试 API |
| **Thunder Client** | Rangav | 轻量级 API 测试工具 |

### 3. 数据库插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **SQLite Viewer** | Florian Klampfer | SQLite 数据库查看 |
| **Database Client** | Weijan Chen | 多数据库支持 |
| **SQLTools** | Matheus Teixeira | SQL 工具和查询编辑器 |

### 3.5 测试相关插件（新增）

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **Playwright Test for VSCode** | Microsoft | Playwright E2E 测试支持，直接在 VSCode 中运行和调试测试 |
| **Test Explorer UI** | Ben Hu | 统一测试管理界面，显示所有测试结果 |
| **Jest Runner** | Firsttris | Jest 测试运行器，快速运行单元测试 |
| **Vitest Explorer** | Vitest | Vitest 测试支持，Vue 3 项目推荐 |

### 4. 工具类插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **GitLens** | GitKraken | Git 增强功能 |
| **Git Graph** | mhutchie | Git 可视化图形 |
| **Path Intellisense** | Christian Kohler | 路径自动补全 |
| **Import Cost** | Wix | 显示导入包大小 |
| **npm Intellisense** | Christian Kohler | npm 包名自动补全 |
| **DotENV** | mikestead | .env 文件高亮支持 |
| **YAML** | Red Hat | YAML 文件支持 |
| **JSON Crack** | JSON Crack | JSON 可视化 |
| **Markdown All in One** | Yu Zhang | Markdown 编辑增强 |
| **Markdown Preview Enhanced** | Yiyi Wang | Markdown 预览增强 |

### 5. 代码质量插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **Code Spell Checker** | Street Side Software | 拼写检查 |
| **Better Comments** | Aaron Bond | 更好的注释高亮 |
| **Todo Tree** | Gruntfuggly | TODO 注释管理 |
| **Error Lens** | Alexander | 增强错误显示 |
| **SonarLint** | SonarSource | 代码质量检查 |

### 6. 主题和美化插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **One Dark Pro** | binaryify | 流行主题 |
| **Material Icon Theme** | Philipp Kief | 材料设计图标 |
| **Indent-Rainbow** | oderwat | 缩进彩虹色 |
| **Bracket Pair Colorizer** | Coenraads | 括号颜色配对 |

### 7. AI 辅助插件

| 插件名称 | 发布者 | 用途 |
|----------|--------|------|
| **GitHub Copilot** | GitHub | AI 代码补全 |
| **Codeium** | Codeium | 免费 AI 代码助手 |
| **Tabnine** | Tabnine | AI 代码补全 |

---

## 三、环境安装顺序建议

### 第一步：基础环境（已完成）
- ✅ Node.js v24.13.1
- ✅ npm 11.8.0
- ✅ pnpm 10.30.3
- ✅ Git 2.52.0

### 第二步：VSCode 插件（已完成）
已安装 22 个插件：
- 核心开发插件（18 个）：Vue.volar、ESLint、Prettier、Auto Rename Tag、Auto Close Tag、Prisma、REST Client、Database Client、GitLens、Git Graph、Path Intellisense、npm Intellisense、DotENV、Code Spell Checker、Better Comments、Todo Tree、Material Icon Theme、Indent-Rainbow
- 测试相关插件（4 个）：Playwright Test、Test Explorer UI、Jest Runner、Vitest Explorer

### 第三步：可选工具（待安装）
1. Windows Terminal (Microsoft Store)
2. DBeaver (数据库管理)
3. Postman / Apifox (API 测试)
4. Docker Desktop (容器化部署)

---

## 四、项目结构预览

> 详细的项目结构设计请参阅：
> - [前端结构设计](../03-design/frontend-structure.md)
> - [后端结构设计](../03-design/backend-structure.md)
> - [数据库设计](../03-design/database-schema.md)

```
project1/
├── docs/                    # 文档目录
├── frontend/                # 前端项目 (Vue 3 + Vite)
│   ├── src/
│   │   ├── components/      # 组件
│   │   ├── views/           # 页面
│   │   ├── router/          # 路由
│   │   ├── stores/          # 状态管理
│   │   └── api/             # API 请求
│   └── package.json
├── backend/                 # 后端项目 (NestJS)
│   ├── src/
│   │   ├── modules/         # 模块
│   │   ├── controllers/     # 控制器
│   │   ├── services/        # 服务
│   │   └── entities/        # 实体
│   └── package.json
├── docker/                  # Docker 配置
├── docker-compose.yml       # Docker Compose 配置
└── README.md
```

---

## 五、环境配置状态

### 已完成
- ✅ 基础环境检查和验证
- ✅ VSCode 命令行工具添加到 PATH
- ✅ 22 个 VSCode 插件安装完成

### 测试方案说明

| 测试类型 | 工具 | 用途 |
|----------|------|------|
| E2E 测试 | Playwright | 博客功能自动化测试（页面点击、表单提交、导航测试） |
| 前端单元测试 | Vitest | Vue 组件和工具函数测试 |
| 后端单元测试 | Jest | API 控制器和服务层测试 |
| 后端 E2E 测试 | Supertest + Jest | API 集成测试 |

### 博客 E2E 测试用例规划

```
首页测试
├── 页面加载正常
├── 文章列表显示
├── 分页功能测试
└── 搜索功能测试

文章详情测试
├── 文章内容显示
├── 评论功能测试
├── 点赞功能测试
└── 分享功能测试

后台管理测试
├── 登录认证测试
├── 文章 CRUD 测试
├── 分类管理测试
└── 标签管理测试
```

## 六、相关文档

- [博客功能规格](../02-specifications/blog-features.md)
- [前端结构设计](../03-design/frontend-structure.md)
- [后端结构设计](../03-design/backend-structure.md)
- [数据库设计](../03-design/database-schema.md)
- [API 设计](../03-design/api-design.md)
- [开发规范](../03-design/development-conventions.md)
- [开发优先级](../06-project-plan/development-priority.md)

## 七、下一步

1. 重启 VSCode 以启用所有插件
2. 创建项目结构
3. 配置前端（Vue 3 + Vite + TypeScript）
4. 配置后端（NestJS + Prisma + SQLite）
5. 编写 E2E 测试用例
6. 配置 CI/CD 和部署
