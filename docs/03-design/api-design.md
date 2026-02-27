# API 设计文档

> 本文档定义了个人博客系统的 RESTful API 接口设计规范、接口列表和使用说明。

---

## 一、API 设计规范

### 1.1 RESTful 原则

| 原则 | 说明 |
|------|------|
| 资源导向 | 所有接口围绕资源（名词）设计 |
| 统一接口 | 使用标准 HTTP 方法操作资源 |
| 无状态 | 每个请求包含所有必要信息 |
| 可缓存 | 响应明确是否可缓存 |
| 分层系统 | 客户端不需要知道服务端内部结构 |

### 1.2 HTTP 方法使用

| 方法 | 用途 | 幂等 |
|------|------|------|
| GET | 获取资源 | 是 |
| POST | 创建资源 | 否 |
| PUT | 更新资源（全量） | 是 |
| PATCH | 更新资源（部分） | 否 |
| DELETE | 删除资源 | 是 |

### 1.3 URL 设计规范

```
格式：/api/{version}/{resource}/{id}/{sub-resource}

示例：
GET  /api/v1/articles          # 获取文章列表
GET  /api/v1/articles/123      # 获取文章详情
POST /api/v1/articles          # 创建文章
PUT  /api/v1/articles/123      # 更新文章
DELETE /api/v1/articles/123    # 删除文章
```

### 1.4 查询参数规范

| 参数 | 说明 | 示例 |
|------|------|------|
| page | 页码 | `?page=1` |
| size | 每页数量 | `?size=10` |
| sort | 排序字段 | `?sort=createdAt,desc` |
| filter | 筛选条件 | `?filter[status]=published` |
| search | 搜索关键词 | `?search=keyword` |

---

## 二、API 接口列表

### 2.1 认证模块（Auth）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/auth/login` | POST | 否 | 用户登录 |
| `/api/v1/auth/logout` | POST | 是 | 用户登出 |
| `/api/v1/auth/refresh` | POST | 是 | 刷新 Token |
| `/api/v1/auth/profile` | GET | 是 | 获取当前用户信息 |
| `/api/v1/auth/password` | PUT | 是 | 修改密码 |

### 2.2 文章模块（Articles）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/articles` | GET | 否 | 获取文章列表 |
| `/api/v1/articles/:id` | GET | 否 | 获取文章详情 |
| `/api/v1/articles` | POST | 是 | 创建文章 |
| `/api/v1/articles/:id` | PUT | 是 | 更新文章 |
| `/api/v1/articles/:id` | DELETE | 是 | 删除文章 |
| `/api/v1/articles/:id/publish` | POST | 是 | 发布文章 |
| `/api/v1/articles/:id/unpublish` | POST | 是 | 下架文章 |
| `/api/v1/articles/:id/like` | POST | 否 | 点赞文章 |
| `/api/v1/articles/:id/view` | POST | 否 | 增加阅读数 |

### 2.3 分类模块（Categories）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/categories` | GET | 否 | 获取分类列表 |
| `/api/v1/categories/:id` | GET | 否 | 获取分类详情 |
| `/api/v1/categories/:id/articles` | GET | 否 | 获取分类下文章 |
| `/api/v1/categories` | POST | 是 | 创建分类 |
| `/api/v1/categories/:id` | PUT | 是 | 更新分类 |
| `/api/v1/categories/:id` | DELETE | 是 | 删除分类 |

### 2.4 标签模块（Tags）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/tags` | GET | 否 | 获取标签列表 |
| `/api/v1/tags/:id` | GET | 否 | 获取标签详情 |
| `/api/v1/tags/:id/articles` | GET | 否 | 获取标签下文章 |
| `/api/v1/tags` | POST | 是 | 创建标签 |
| `/api/v1/tags/:id` | PUT | 是 | 更新标签 |
| `/api/v1/tags/:id` | DELETE | 是 | 删除标签 |

### 2.5 用户模块（Users）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/users` | GET | 是 | 获取用户列表（管理员） |
| `/api/v1/users/:id` | GET | 是 | 获取用户详情 |
| `/api/v1/users/:id` | PUT | 是 | 更新用户 |
| `/api/v1/users/:id` | DELETE | 是 | 删除用户 |
| `/api/v1/users/:id/role` | PUT | 是 | 修改用户角色 |

### 2.6 友情链接模块（Links）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/links` | GET | 否 | 获取友链列表 |
| `/api/v1/links` | POST | 是 | 创建友链 |
| `/api/v1/links/:id` | PUT | 是 | 更新友链 |
| `/api/v1/links/:id` | DELETE | 是 | 删除友链 |

### 2.7 系统设置模块（Settings）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/settings` | GET | 是 | 获取所有配置 |
| `/api/v1/settings/:key` | GET | 是 | 获取单个配置 |
| `/api/v1/settings` | POST | 是 | 创建配置 |
| `/api/v1/settings/:key` | PUT | 是 | 更新配置 |
| `/api/v1/settings/:key` | DELETE | 是 | 删除配置 |

### 2.8 仪表盘模块（Dashboard）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/dashboard/stats` | GET | 是 | 获取统计数据 |
| `/api/v1/dashboard/views` | GET | 是 | 获取访问趋势 |
| `/api/v1/dashboard/popular` | GET | 是 | 获取热门文章 |

### 2.9 日志模块（Logs）

| 接口 | 方法 | 认证 | 说明 |
|------|------|------|------|
| `/api/v1/logs` | GET | 是 | 获取日志列表 |
| `/api/v1/logs/:id` | GET | 是 | 获取日志详情 |

---

## 三、请求/响应示例

### 3.1 用户登录

**请求**：
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "admin123"
}
```

**响应**：
```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 7200,
    "user": {
      "id": 1,
      "username": "admin",
      "email": "admin@example.com",
      "role": "ADMIN",
      "avatar": null
    }
  }
}
```

### 3.2 获取文章列表

**请求**：
```http
GET /api/v1/articles?page=1&size=10&status=published
```

**响应**：
```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": 1,
        "title": "Hello World - 我的第一篇博客",
        "summary": "这是我的第一篇博客文章...",
        "coverImage": "https://example.com/cover.jpg",
        "viewCount": 100,
        "likeCount": 10,
        "isFeatured": true,
        "isTop": false,
        "publishedAt": "2026-02-27T10:00:00.000Z",
        "author": {
          "id": 1,
          "username": "admin",
          "avatar": null
        },
        "categories": [
          { "id": 1, "name": "技术文章", "slug": "tech" }
        ],
        "tags": [
          { "id": 1, "name": "JavaScript", "slug": "javascript", "color": "#f7df1e" }
        ],
        "createdAt": "2026-02-27T09:00:00.000Z",
        "updatedAt": "2026-02-27T10:00:00.000Z"
      }
    ],
    "pagination": {
      "page": 1,
      "size": 10,
      "total": 50,
      "totalPages": 5
    }
  }
}
```

### 3.3 创建文章

**请求**：
```http
POST /api/v1/articles
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

{
  "title": "Vue 3 组合式 API 入门",
  "content": "# Vue 3 组合式 API 入门\n\nVue 3 引入了组合式 API...",
  "summary": "介绍 Vue 3 组合式 API 的基本用法",
  "categoryIds": [1],
  "tagIds": [1, 2],
  "isFeatured": false
}
```

**响应**：
```json
{
  "success": true,
  "data": {
    "id": 2,
    "title": "Vue 3 组合式 API 入门",
    "status": "DRAFT",
    "authorId": 1,
    "createdAt": "2026-02-27T11:00:00.000Z",
    "updatedAt": "2026-02-27T11:00:00.000Z"
  },
  "message": "文章创建成功"
}
```

### 3.4 更新文章

**请求**：
```http
PUT /api/v1/articles/2
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

{
  "title": "Vue 3 组合式 API 完全指南",
  "content": "# Vue 3 组合式 API 完全指南\n\n这是一份完整的指南...",
  "summary": "Vue 3 组合式 API 的完整指南",
  "categoryIds": [1],
  "tagIds": [1, 2, 3]
}
```

**响应**：
```json
{
  "success": true,
  "data": {
    "id": 2,
    "title": "Vue 3 组合式 API 完全指南",
    "updatedAt": "2026-02-27T12:00:00.000Z"
  },
  "message": "文章更新成功"
}
```

### 3.5 删除文章

**请求**：
```http
DELETE /api/v1/articles/2
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**响应**：
```json
{
  "success": true,
  "message": "文章已删除"
}
```

---

## 四、认证与授权

### 4.1 JWT Token 认证

所有需要认证的接口需要在请求头中携带 JWT Token：

```http
Authorization: Bearer <access_token>
```

### 4.2 Token 刷新

当 Token 即将过期时，使用刷新接口获取新的 Token：

```http
POST /api/v1/auth/refresh
Authorization: Bearer <current_token>
```

### 4.3 权限控制

| 角色 | 权限 |
|------|------|
| ADMIN | 所有权限 |
| EDITOR | 文章管理、分类管理、标签管理 |
| USER | 仅浏览权限 |

---

## 五、错误处理

### 5.1 错误响应格式

```json
{
  "success": false,
  "error": {
    "code": "ARTICLE_NOT_FOUND",
    "message": "文章不存在",
    "details": [
      {
        "field": "id",
        "message": "文章 ID 必须为正整数"
      }
    ]
  },
  "statusCode": 404
}
```

### 5.2 错误码定义

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `VALIDATION_ERROR` | 400 | 参数验证失败 |
| `UNAUTHORIZED` | 401 | 未授权 |
| `FORBIDDEN` | 403 | 禁止访问 |
| `NOT_FOUND` | 404 | 资源不存在 |
| `CONFLICT` | 409 | 资源冲突 |
| `INTERNAL_ERROR` | 500 | 服务器内部错误 |

### 5.3 业务错误码

| 错误码 | 说明 |
|--------|------|
| `USER_NOT_FOUND` | 用户不存在 |
| `USER_EXISTS` | 用户已存在 |
| `INVALID_CREDENTIALS` | 用户名或密码错误 |
| `ARTICLE_NOT_FOUND` | 文章不存在 |
| `CATEGORY_NOT_FOUND` | 分类不存在 |
| `TAG_NOT_FOUND` | 标签不存在 |
| `CATEGORY_EXISTS` | 分类已存在 |
| `TAG_EXISTS` | 标签已存在 |

---

## 六、API 版本控制

### 6.1 版本命名

使用 URL 路径版本控制：`/api/v1/...`

### 6.2 版本策略

| 版本 | 状态 | 说明 |
|------|------|------|
| v1 | 当前版本 | 正在使用的版本 |
| v2 | 规划中 | 未来版本 |

### 6.3 废弃策略

- 旧版本 API 至少维护 6 个月
- 在响应头中添加废弃通知
- 提供迁移指南

---

## 七、速率限制

### 7.1 限制策略

| 接口类型 | 限制 | 说明 |
|----------|------|------|
| 认证接口 | 10 次/分钟 | 防止暴力破解 |
| 公开接口 | 100 次/分钟 | 一般限制 |
| 管理接口 | 50 次/分钟 | 后台操作 |

### 7.2 响应头

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1677484800
```

---

## 八、检查清单

- [ ] 所有接口遵循 RESTful 规范
- [ ] 响应格式统一
- [ ] 错误处理完善
- [ ] 认证授权正确实施
- [ ] 参数验证完整
- [ ] API 文档完整
- [ ] 速率限制配置正确
