# API 响应格式规范

> 本文档定义了个人博客系统 API 的统一响应格式、错误码定义和使用说明。

---

## 一、统一响应格式

### 1.1 成功响应格式

```json
{
  "success": true,
  "data": { ... },
  "message": "操作成功"
}
```

### 1.2 分页响应格式

```json
{
  "success": true,
  "data": {
    "items": [...],
    "pagination": {
      "page": 1,
      "size": 10,
      "total": 100,
      "totalPages": 10
    }
  }
}
```

### 1.3 错误响应格式

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "错误描述信息",
    "details": [
      {
        "field": "fieldName",
        "message": "字段级别的错误信息"
      }
    ]
  },
  "statusCode": 400
}
```

---

## 二、HTTP 状态码使用

### 2.1 成功状态码

| 状态码 | 含义 | 使用场景 |
|--------|------|----------|
| 200 | OK | GET/PUT/PATCH 请求成功 |
| 201 | Created | POST 创建资源成功 |
| 204 | No Content | DELETE 删除成功，无返回内容 |

### 2.2 客户端错误状态码

| 状态码 | 含义 | 使用场景 |
|--------|------|----------|
| 400 | Bad Request | 请求参数错误、验证失败 |
| 401 | Unauthorized | 未授权，需要登录 |
| 403 | Forbidden | 禁止访问，权限不足 |
| 404 | Not Found | 资源不存在 |
| 409 | Conflict | 资源冲突（如用户名已存在） |
| 422 | Unprocessable Entity | 请求格式正确但无法处理 |
| 429 | Too Many Requests | 请求次数超限 |

### 2.3 服务端错误状态码

| 状态码 | 含义 | 使用场景 |
|--------|------|----------|
| 500 | Internal Server Error | 服务器内部错误 |
| 502 | Bad Gateway | 网关错误 |
| 503 | Service Unavailable | 服务不可用 |

---

## 三、业务错误码定义

### 3.1 通用错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `VALIDATION_ERROR` | 400 | 参数验证失败 |
| `UNAUTHORIZED` | 401 | 未授权 |
| `FORBIDDEN` | 403 | 禁止访问 |
| `NOT_FOUND` | 404 | 资源不存在 |
| `INTERNAL_ERROR` | 500 | 服务器内部错误 |
| `RATE_LIMIT_EXCEEDED` | 429 | 请求频率超限 |

### 3.2 认证模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `INVALID_CREDENTIALS` | 401 | 用户名或密码错误 |
| `TOKEN_EXPIRED` | 401 | Token 已过期 |
| `TOKEN_INVALID` | 401 | Token 无效 |
| `USER_DISABLED` | 403 | 用户已被禁用 |
| `PASSWORD_TOO_WEAK` | 400 | 密码强度不足 |
| `OLD_PASSWORD_INCORRECT` | 400 | 原密码错误 |

### 3.3 用户模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `USER_NOT_FOUND` | 404 | 用户不存在 |
| `USER_EXISTS` | 409 | 用户名已存在 |
| `EMAIL_EXISTS` | 409 | 邮箱已被使用 |
| `USERNAME_EXISTS` | 409 | 用户名已被占用 |

### 3.4 文章模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `ARTICLE_NOT_FOUND` | 404 | 文章不存在 |
| `ARTICLE_ALREADY_PUBLISHED` | 409 | 文章已发布 |
| `ARTICLE_ALREADY_DRAFT` | 409 | 文章已是草稿 |
| `INVALID_ARTICLE_STATUS` | 400 | 文章状态无效 |
| `AUTHOR_NOT_FOUND` | 404 | 作者不存在 |

### 3.5 分类模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `CATEGORY_NOT_FOUND` | 404 | 分类不存在 |
| `CATEGORY_EXISTS` | 409 | 分类已存在 |
| `CATEGORY_SLUG_EXISTS` | 409 | 分类别名已存在 |
| `CATEGORY_HAS_ARTICLES` | 409 | 分类下有文章，无法删除 |
| `INVALID_PARENT_CATEGORY` | 400 | 父分类无效 |

### 3.6 标签模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `TAG_NOT_FOUND` | 404 | 标签不存在 |
| `TAG_EXISTS` | 409 | 标签已存在 |
| `TAG_SLUG_EXISTS` | 409 | 标签别名已存在 |
| `TAG_HAS_ARTICLES` | 409 | 标签下有文章，无法删除 |

### 3.7 友情链接模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `LINK_NOT_FOUND` | 404 | 友链不存在 |
| `LINK_URL_EXISTS` | 409 | 友链 URL 已存在 |
| `INVALID_LINK_URL` | 400 | 友链 URL 格式无效 |

### 3.8 系统设置模块错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|------------|------|
| `SETTING_NOT_FOUND` | 404 | 配置不存在 |
| `SETTING_KEY_EXISTS` | 409 | 配置键已存在 |
| `INVALID_SETTING_VALUE` | 400 | 配置值格式无效 |

---

## 四、数据格式规范

### 4.1 日期时间格式

使用 ISO 8601 格式：

```json
{
  "createdAt": "2026-02-27T10:00:00.000Z",
  "publishedAt": "2026-02-27T10:00:00.000+08:00"
}
```

### 4.2 数字格式

| 类型 | 示例 | 说明 |
|------|------|------|
| 整数 | `100` | 阅读量、点赞数 |
| 浮点数 | `3.14` | 统计数据 |
| 布尔值 | `true` | 是否置顶、是否精选 |

### 4.3 字符串格式

| 类型 | 示例 | 说明 |
|------|------|------|
| 普通文本 | `"Hello World"` | 标题、内容 |
| URL | `"https://example.com"` | 图片 URL、友链 URL |
| 邮箱 | `"user@example.com"` | 用户邮箱 |
| Slug | `"hello-world"` | URL 友好的标识 |

### 4.4 数组格式

```json
{
  "categoryIds": [1, 2, 3],
  "tags": [
    { "id": 1, "name": "JavaScript" },
    { "id": 2, "name": "Vue" }
  ]
}
```

---

## 五、响应头规范

### 5.1 通用响应头

```http
Content-Type: application/json; charset=utf-8
X-Request-Id: 550e8400-e29b-41d4-a716-446655440000
X-Response-Time: 45ms
```

### 5.2 认证相关响应头

```http
Authorization: Bearer <token>
Set-Cookie: refreshToken=<token>; HttpOnly; Secure; SameSite=Strict
```

### 5.3 缓存相关响应头

```http
# 可缓存响应
Cache-Control: public, max-age=3600
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"

# 不可缓存响应
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
```

### 5.4 速率限制响应头

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1677484800
```

### 5.5 分页响应头

```http
Link: <https://api.example.com/articles?page=2>; rel="next",
      <https://api.example.com/articles?page=5>; rel="last"
```

---

## 六、请求体验证规则

### 6.1 通用验证规则

| 字段类型 | 验证规则 | 错误信息 |
|----------|----------|----------|
| 必填字段 | `@IsNotEmpty()` | "{field} 不能为空" |
| 字符串 | `@IsString()` | "{field} 必须是字符串" |
| 邮箱 | `@IsEmail()` | "{field} 格式不正确" |
| URL | `@IsUrl()` | "{field} 格式不正确" |
| 数字 | `@IsNumber()` | "{field} 必须是数字" |
| 正整数 | `@IsInt() @Min(1)` | "{field} 必须是正整数" |

### 6.2 长度验证

| 字段 | 最小长度 | 最大长度 | 错误信息 |
|------|----------|----------|----------|
| 用户名 | 3 | 30 | "用户名长度 3-30 个字符" |
| 密码 | 6 | 50 | "密码长度 6-50 个字符" |
| 文章标题 | 1 | 200 | "标题长度 1-200 个字符" |
| 文章摘要 | 0 | 500 | "摘要长度不能超过 500 个字符" |
| 分类名称 | 1 | 50 | "分类名称长度 1-50 个字符" |
| 标签名称 | 1 | 30 | "标签名称长度 1-30 个字符" |

### 6.3 验证错误响应示例

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "参数验证失败",
    "details": [
      {
        "field": "title",
        "message": "标题不能为空"
      },
      {
        "field": "title",
        "message": "标题长度不能超过 200 个字符"
      },
      {
        "field": "content",
        "message": "内容不能为空"
      }
    ]
  },
  "statusCode": 400
}
```

---

## 七、API 响应示例

### 7.1 获取单个资源

**请求**：
```http
GET /api/v1/articles/1
```

**响应**：
```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Hello World",
    "content": "...",
    "summary": "...",
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
    "viewCount": 100,
    "likeCount": 10,
    "isFeatured": true,
    "isTop": false,
    "status": "PUBLISHED",
    "publishedAt": "2026-02-27T10:00:00.000Z",
    "createdAt": "2026-02-27T09:00:00.000Z",
    "updatedAt": "2026-02-27T10:00:00.000Z"
  }
}
```

### 7.2 创建资源

**请求**：
```http
POST /api/v1/articles
Content-Type: application/json

{
  "title": "新文章",
  "content": "文章内容..."
}
```

**响应** (201 Created)：
```json
{
  "success": true,
  "data": {
    "id": 2,
    "title": "新文章",
    "status": "DRAFT",
    "createdAt": "2026-02-27T11:00:00.000Z"
  },
  "message": "文章创建成功"
}
```

### 7.3 删除资源

**请求**：
```http
DELETE /api/v1/articles/1
```

**响应** (204 No Content)：
```
（无响应体）
```

### 7.4 错误响应

**请求**：
```http
GET /api/v1/articles/999
```

**响应** (404 Not Found)：
```json
{
  "success": false,
  "error": {
    "code": "ARTICLE_NOT_FOUND",
    "message": "文章不存在"
  },
  "statusCode": 404
}
```

---

## 八、检查清单

- [ ] 所有 API 使用统一响应格式
- [ ] 错误响应包含错误码和详细信息
- [ ] 分页响应包含完整的分页信息
- [ ] 日期时间使用 ISO 8601 格式
- [ ] 响应头包含必要的元信息
- [ ] 验证错误返回字段级详情
- [ ] HTTP 状态码使用正确
- [ ] 错误码定义完整
