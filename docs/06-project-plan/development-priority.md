# 开发优先级与功能划分

## 开发策略

采用**功能垂直切片**开发模式，每个功能模块包含完整的前后端实现和测试，做完一个功能再进行下一个功能。

---

## 第一阶段：MVP（最小可行产品）

### Sprint 0 - 基础架构（1-2 天）

**目标**：完成项目基础架构和核心配置

| 序号 | 任务 | 文档 | 验收标准 |
|------|------|------|----------|
| 0.1 | 初始化前端项目 | - | Vite + Vue 3 + TS 项目创建 |
| 0.2 | 初始化后端项目 | - | NestJS 项目创建 |
| 0.3 | 配置 Prisma 和数据库 | `database-schema.md` | 数据库表结构定义完成 |
| 0.4 | 配置 ESLint/Prettier | - | 代码规范配置完成 |
| 0.5 | 配置 Git 钩子 | - | 提交前自动检查 |

**输出文档**：
- `docs/design/database-schema.md` - 数据库设计
- `docs/design/frontend-structure.md` - 前端结构
- `docs/design/backend-structure.md` - 后端结构

---

### Sprint 1 - 用户认证模块（2-3 天）

**目标**：完成用户登录认证功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 1.1 | 用户数据表 | 后端 | database-schema.md | - |
| 1.2 | 登录 API | 后端 | 03-backend-auth.md | 单元测试 + E2E |
| 1.3 | JWT 认证 | 后端 | 03-backend-auth.md | 单元测试 |
| 1.4 | 登录页面 | 前端 | 03-backend-auth.md | E2E |
| 1.5 | 认证守卫 | 后端 | 03-backend-auth.md | 单元测试 |
| 1.6 | 路由守卫 | 前端 | router-design.md | E2E |

**验收标准**：
- [ ] 用户可以成功登录
- [ ] 登录后获取 JWT Token
- [ ] 受保护路由需要认证
- [ ] Token 过期自动刷新
- [ ] 登出功能正常

**完成标志**：`implementation/03-backend-auth.md` 中所有测试通过

---

### Sprint 2 - 分类管理模块（2 天）

**目标**：完成分类的 CRUD 功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 2.1 | 分类数据表 | 后端 | database-schema.md | - |
| 2.2 | 分类 CRUD API | 后端 | 06-backend-category.md | 单元测试 |
| 2.3 | 分类列表页 | 前端 | 06-backend-category.md | E2E |
| 2.4 | 分类新建/编辑表单 | 前端 | 06-backend-category.md | E2E |
| 2.5 | 分类删除功能 | 前后端 | 06-backend-category.md | E2E |

**验收标准**：
- [ ] 可以创建新分类
- [ ] 可以编辑分类信息
- [ ] 可以删除分类
- [ ] 分类列表正确显示
- [ ] 分类与文章关联正确

**完成标志**：`implementation/06-backend-category.md` 中所有测试通过

---

### Sprint 3 - 标签管理模块（2 天）

**目标**：完成标签的 CRUD 功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 3.1 | 标签数据表 | 后端 | database-schema.md | - |
| 3.2 | 标签 CRUD API | 后端 | 07-backend-tag.md | 单元测试 |
| 3.3 | 标签列表页 | 前端 | 07-backend-tag.md | E2E |
| 3.4 | 标签新建/编辑表单 | 前端 | 07-backend-tag.md | E2E |
| 3.5 | 标签删除功能 | 前后端 | 07-backend-tag.md | E2E |
| 3.6 | 文章关联标签 | 前后端 | 04-backend-article.md | E2E |

**验收标准**：
- [ ] 可以创建新标签
- [ ] 可以编辑标签信息
- [ ] 可以删除标签
- [ ] 标签列表正确显示
- [ ] 标签云功能正常

**完成标志**：`implementation/07-backend-tag.md` 中所有测试通过

---

### Sprint 4 - 文章管理模块（3-4 天）

**目标**：完成文章的 CRUD 功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 4.1 | 文章数据表 | 后端 | database-schema.md | - |
| 4.2 | 文章 CRUD API | 后端 | 04-backend-article.md | 单元测试 |
| 4.3 | 文章列表页（后台） | 前端 | 04-backend-article.md | E2E |
| 4.4 | Markdown 编辑器 | 前端 | 04-backend-article.md | E2E |
| 4.5 | 文章新建/编辑 | 前端 | 04-backend-article.md | E2E |
| 4.6 | 文章发布/下架 | 后端 | 04-backend-article.md | E2E |
| 4.7 | 文章置顶功能 | 后端 | 04-backend-article.md | E2E |
| 4.8 | 文章与分类/标签关联 | 前后端 | 04-backend-article.md | E2E |

**验收标准**：
- [ ] 可以创建新文章
- [ ] 可以编辑文章内容
- [ ] 可以删除文章
- [ ] 可以发布/下架文章
- [ ] 可以置顶文章
- [ ] 文章与分类/标签关联正确
- [ ] Markdown 渲染正常

**完成标志**：`implementation/04-backend-article.md` 中所有测试通过

---

### Sprint 5 - 前台首页模块（2-3 天）

**目标**：完成前台首页展示

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 5.1 | 文章列表 API | 后端 | 01-frontend-home.md | 单元测试 |
| 5.2 | 分页功能 | 后端 | 01-frontend-home.md | 单元测试 |
| 5.3 | 首页文章列表 | 前端 | 01-frontend-home.md | E2E |
| 5.4 | 分页组件 | 前端 | 01-frontend-home.md | E2E |
| 5.5 | 精选文章展示 | 前端 | 01-frontend-home.md | E2E |
| 5.6 | 最新文章侧边栏 | 前端 | 01-frontend-home.md | E2E |

**验收标准**：
- [ ] 文章列表正确显示
- [ ] 分页功能正常
- [ ] 精选文章正确展示
- [ ] 响应式布局正常
- [ ] 加载时间 < 2 秒

**完成标志**：`implementation/01-frontend-home.md` 中所有测试通过

---

### Sprint 6 - 前台文章详情模块（2 天）

**目标**：完成文章详情页展示

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 6.1 | 文章详情 API | 后端 | 02-frontend-article-detail.md | 单元测试 |
| 6.2 | 阅读计数 | 后端 | 02-frontend-article-detail.md | 单元测试 |
| 6.3 | 点赞功能 API | 后端 | 02-frontend-article-detail.md | 单元测试 |
| 6.4 | Markdown 渲染 | 前端 | 02-frontend-article-detail.md | E2E |
| 6.5 | 代码高亮 | 前端 | 02-frontend-article-detail.md | E2E |
| 6.6 | 目录导航 | 前端 | 02-frontend-article-detail.md | E2E |
| 6.7 | 点赞功能 | 前端 | 02-frontend-article-detail.md | E2E |
| 6.8 | 上一篇/下一篇 | 前端 | 02-frontend-article-detail.md | E2E |

**验收标准**：
- [ ] Markdown 内容正确渲染
- [ ] 代码高亮正常
- [ ] 目录导航生成正确
- [ ] 点赞功能正常
- [ ] 阅读计数正确累加

**完成标志**：`implementation/02-frontend-article-detail.md` 中所有测试通过

---

### Sprint 7 - 前台分类/标签页模块（1-2 天）

**目标**：完成前台分类/标签筛选功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 7.1 | 分类筛选 API | 后端 | 05-frontend-category-tag.md | 单元测试 |
| 7.2 | 标签筛选 API | 后端 | 05-frontend-category-tag.md | 单元测试 |
| 7.3 | 分类列表页 | 前端 | 05-frontend-category-tag.md | E2E |
| 7.4 | 标签云组件 | 前端 | 05-frontend-category-tag.md | E2E |
| 7.5 | 分类详情页 | 前端 | 05-frontend-category-tag.md | E2E |
| 7.6 | 标签详情页 | 前端 | 05-frontend-category-tag.md | E2E |

**验收标准**：
- [ ] 分类列表正确显示
- [ ] 标签云正确展示
- [ ] 分类筛选功能正常
- [ ] 标签筛选功能正常

**完成标志**：`implementation/05-frontend-category-tag.md` 中所有测试通过

---

### Sprint 8 - 前台关于页面（1 天）

**目标**：完成关于页面展示

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 8.1 | 关于页面路由 | 前端 | 08-frontend-about.md | E2E |
| 8.2 | 博主信息展示 | 前端 | 08-frontend-about.md | E2E |
| 8.3 | 技能栈展示 | 前端 | 08-frontend-about.md | E2E |
| 8.4 | 社交媒体链接 | 前端 | 08-frontend-about.md | E2E |

**验收标准**：
- [ ] 关于页面正常显示
- [ ] 博主信息正确展示
- [ ] 社交媒体链接正常

**完成标志**：`implementation/08-frontend-about.md` 中所有测试通过

---

### Sprint 9 - 友情链接模块（1 天）

**目标**：完成友情链接展示

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 9.1 | 友情链接数据表 | 后端 | database-schema.md | - |
| 9.2 | 友链查询 API | 后端 | 09-frontend-links.md | 单元测试 |
| 9.3 | 友链展示组件 | 前端 | 09-frontend-links.md | E2E |

**验收标准**：
- [ ] 友情链接正确显示

**完成标志**：`implementation/09-frontend-links.md` 中所有测试通过

---

## 第二阶段：增强功能

### Sprint 10 - 系统设置模块（2 天）

**目标**：完成系统配置功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 10.1 | 配置数据表 | 后端 | database-schema.md | - |
| 10.2 | 配置 CRUD API | 后端 | 11-backend-settings.md | 单元测试 |
| 10.3 | 站点配置页面 | 前端 | 11-backend-settings.md | E2E |
| 10.4 | SEO 设置页面 | 前端 | 11-backend-settings.md | E2E |

**完成标志**：`implementation/11-backend-settings.md` 中所有测试通过

---

### Sprint 11 - 仪表盘模块（2 天）

**目标**：完成后台仪表盘

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 11.1 | 统计数据 API | 后端 | 10-backend-dashboard.md | 单元测试 |
| 11.2 | 仪表盘页面 | 前端 | 10-backend-dashboard.md | E2E |
| 11.3 | 访问趋势图表 | 前端 | 10-backend-dashboard.md | E2E |

**完成标志**：`implementation/10-backend-dashboard.md` 中所有测试通过

---

### Sprint 12 - 用户管理模块（2 天）

**目标**：完成用户管理功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 12.1 | 用户 CRUD API | 后端 | 12-backend-user.md | 单元测试 |
| 12.2 | 用户列表页 | 前端 | 12-backend-user.md | E2E |
| 12.3 | 角色管理 | 后端 | 12-backend-user.md | E2E |

**完成标志**：`implementation/12-backend-user.md` 中所有测试通过

---

### Sprint 13 - 权限管理模块（2 天）

**目标**：完成 RBAC 权限控制

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 13.1 | 角色权限数据表 | 后端 | database-schema.md | - |
| 13.2 | 权限守卫 | 后端 | 13-backend-rbac.md | 单元测试 |
| 13.3 | 菜单权限 | 前端 | 13-backend-rbac.md | E2E |
| 13.4 | 角色分配 | 后端 | 13-backend-rbac.md | E2E |

**完成标志**：`implementation/13-backend-rbac.md` 中所有测试通过

---

### Sprint 14 - 日志管理模块（1-2 天）

**目标**：完成日志记录功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 14.1 | 日志数据表 | 后端 | database-schema.md | - |
| 14.2 | 日志记录中间件 | 后端 | 14-backend-logger.md | 单元测试 |
| 14.3 | 日志列表页 | 前端 | 14-backend-logger.md | E2E |
| 14.4 | 日志查询 | 后端 | 14-backend-logger.md | 单元测试 |

**完成标志**：`implementation/14-backend-logger.md` 中所有测试通过

---

### Sprint 15 - 数据统计模块（1-2 天）

**目标**：完成数据统计功能

| 序号 | 任务 | 前后端 | 文档 | 测试 |
|------|------|--------|------|------|
| 15.1 | 统计数据表 | 后端 | database-schema.md | - |
| 15.2 | 统计 API | 后端 | 15-backend-statistics.md | 单元测试 |
| 15.3 | 统计图表 | 前端 | 15-backend-statistics.md | E2E |

**完成标志**：`implementation/15-backend-statistics.md` 中所有测试通过

---

## 第三阶段：可选功能

### Sprint 16+ - 扩展功能

- 主题切换
- 数据备份
- 第三方登录
- 评论系统（如启用）
- 搜索功能（如启用）

---

## 开发流程图

```
Sprint 0 (基础架构)
    ↓
Sprint 1 (用户认证) ← 依赖 Sprint 0
    ↓
Sprint 2 (分类管理) ← 依赖 Sprint 1
    ↓
Sprint 3 (标签管理) ← 依赖 Sprint 1
    ↓
Sprint 4 (文章管理) ← 依赖 Sprint 2, 3
    ↓
    ├──→ Sprint 5 (前台首页) → Sprint 6 (文章详情) → Sprint 7 (分类/标签页)
    │                                                        ↓
    │                                                    Sprint 8 (关于页面)
    │                                                        ↓
    │                                                    Sprint 9 (友情链接)
    │
    └──→ Sprint 10 (系统设置)
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

---

## 每个 Sprint 的检查清单

```markdown
## Sprint X 检查清单

### 开发前
- [ ] 阅读功能清单
- [ ] 查看实现文档
- [ ] 确认 API 设计
- [ ] 确认数据库设计

### 开发中
- [ ] 后端 API 实现
- [ ] 后端单元测试
- [ ] 前端页面实现
- [ ] 前后端联调

### 开发后
- [ ] E2E 测试通过
- [ ] 代码审查通过
- [ ] 文档更新
- [ ] 提交代码

### 验收
- [ ] 所有测试用例通过
- [ ] 功能符合需求
- [ ] 性能达标
- [ ] 无已知 bug
```

---

## 优先级调整说明

1. **用户认证** 是基础，必须最先完成
2. **分类/标签** 是文章的基础数据，需在文章管理前完成
3. **文章管理** 是核心功能，完成后可以开始前台展示
4. **前台展示** 按用户访问流程：首页 → 详情 → 分类/标签 → 关于
5. **后台增强** 功能在 MVP 完成后逐步实现
