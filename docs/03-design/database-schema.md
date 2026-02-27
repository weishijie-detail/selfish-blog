# 数据库设计文档

> 本文档定义了个人博客系统的数据库结构设计，使用 Prisma ORM 和 SQLite 数据库。

---

## 一、数据库 ER 图

```
┌─────────────────┐       ┌─────────────────┐
│      User       │       │    Category     │
├─────────────────┤       ├─────────────────┤
│ id: Int         │       │ id: Int         │
│ username: String│       │ name: String    │
│ email: String   │       │ slug: String    │
│ password: String│       │ description: Str│
│ role: Role      │       │ parentId: Int?  │
│ createdAt: Date │       │ createdAt: Date │
│ updatedAt: Date │       │ updatedAt: Date │
└────────┬────────┘       └────────┬────────┘
         │                        │
         │ 1:N                    │ 1:N
         │                        │
         ▼                        ▼
┌─────────────────┐       ┌─────────────────┐
│    Article      │──────▶│  ArticleCategory│
├─────────────────┤       ├─────────────────┤
│ id: Int         │       │ articleId: Int  │
│ title: String   │       │ categoryId: Int │
│ content: String │       └─────────────────┘
│ summary: String │
│ status: Status  │       ┌─────────────────┐
│ viewCount: Int  │       │      Tag        │
│ likeCount: Int  │       ├─────────────────┤
│ isFeatured: Bool│       │ id: Int         │
│ authorId: Int   │       │ name: String    │
│ createdAt: Date │       │ slug: String    │
│ updatedAt: Date │       │ createdAt: Date │
└────────┬────────┘       │ updatedAt: Date │
         │                └────────┬────────┘
         │ 1:N                     │
         │                         │ N:M
         ▼                         │
┌─────────────────┐                │
│   ArticleTag    │◀───────────────┘
├─────────────────┤
│ articleId: Int  │
│ tagId: Int      │
└─────────────────┘

┌─────────────────┐       ┌─────────────────┐
│     Link        │       │   Settings      │
├─────────────────┤       ├─────────────────┤
│ id: Int         │       │ id: Int         │
│ name: String    │       │ key: String     │
│ url: String     │       │ value: String   │
│ description: Str│       │ type: String    │
│ isActive: Bool  │       │ description: Str│
│ sortOrder: Int  │       │ createdAt: Date │
│ createdAt: Date │       │ updatedAt: Date │
│ updatedAt: Date │       └─────────────────┘
└─────────────────┘
```

---

## 二、数据表详细设计

### 2.1 用户表（users）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 用户 ID（自增） |
| username | String(50) | UNIQUE, NOT NULL | 用户名 |
| email | String(100) | UNIQUE, NOT NULL | 邮箱 |
| password | String(255) | NOT NULL | 密码（bcrypt 加密） |
| role | Role | DEFAULT 'USER' | 角色：ADMIN/EDITOR/USER |
| avatar | String(255) | NULL | 头像 URL |
| bio | String(500) | NULL | 个人简介 |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

### 2.2 文章表（articles）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 文章 ID（自增） |
| title | String(200) | NOT NULL | 文章标题 |
| content | Text | NOT NULL | 文章内容（Markdown） |
| summary | Text | NULL | 文章摘要 |
| coverImage | String(255) | NULL | 封面图片 URL |
| status | ArticleStatus | DEFAULT 'DRAFT' | 状态：DRAFT/PUBLISHED/ARCHIVED |
| viewCount | Int | DEFAULT 0 | 阅读量 |
| likeCount | Int | DEFAULT 0 | 点赞数 |
| isFeatured | Boolean | DEFAULT FALSE | 是否精选 |
| isTop | Boolean | DEFAULT FALSE | 是否置顶 |
| publishedAt | DateTime | NULL | 发布时间 |
| authorId | Int | FOREIGN KEY | 作者 ID |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

**索引**：
- `idx_status_created` (status, createdAt) - 用于按状态和时间查询
- `idx_author_id` (authorId) - 用于按作者查询
- `idx_published_at` (publishedAt) - 用于按发布时间排序

### 2.3 分类表（categories）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 分类 ID（自增） |
| name | String(50) | NOT NULL | 分类名称 |
| slug | String(50) | UNIQUE, NOT NULL | 分类别名（URL 友好） |
| description | String(200) | NULL | 分类描述 |
| parentId | Int | NULL, SELF FK | 父分类 ID（支持多级分类） |
| sortOrder | Int | DEFAULT 0 | 排序顺序 |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

**索引**：
- `idx_slug` (slug) - 用于 URL 路由
- `idx_parent_id` (parentId) - 用于查询子分类

### 2.4 标签表（tags）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 标签 ID（自增） |
| name | String(30) | NOT NULL | 标签名称 |
| slug | String(30) | UNIQUE, NOT NULL | 标签别名（URL 友好） |
| color | String(7) | DEFAULT '#666666' | 标签颜色（16 进制） |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

**索引**：
- `idx_slug` (slug) - 用于 URL 路由

### 2.5 文章分类关联表（article_categories）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| articleId | Int | PRIMARY KEY, FOREIGN KEY | 文章 ID |
| categoryId | Int | PRIMARY KEY, FOREIGN KEY | 分类 ID |

**复合主键**：(articleId, categoryId)

### 2.6 文章标签关联表（article_tags）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| articleId | Int | PRIMARY KEY, FOREIGN KEY | 文章 ID |
| tagId | Int | PRIMARY KEY, FOREIGN KEY | 标签 ID |

**复合主键**：(articleId, tagId)

**索引**：
- `idx_tag_id` (tagId) - 用于按标签查询文章

### 2.7 友情链接表（links）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 友链 ID（自增） |
| name | String(50) | NOT NULL | 网站名称 |
| url | String(255) | NOT NULL | 网站 URL |
| description | String(200) | NULL | 网站描述 |
| logo | String(255) | NULL | 网站 Logo URL |
| isActive | Boolean | DEFAULT TRUE | 是否激活 |
| sortOrder | Int | DEFAULT 0 | 排序顺序 |
| verifiedAt | DateTime | NULL | 验证时间 |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

### 2.8 系统配置表（settings）

| 字段 | 类型 | 约束 | 说明 |
|------|------|------|------|
| id | Int | PRIMARY KEY, AUTOINCREMENT | 配置 ID（自增） |
| key | String(50) | UNIQUE, NOT NULL | 配置键 |
| value | Text | NOT NULL | 配置值（JSON 字符串） |
| type | String(20) | DEFAULT 'string' | 配置类型：string/number/boolean/json |
| group | String(30) | DEFAULT 'general' | 配置分组 |
| description | String(200) | NULL | 配置说明 |
| createdAt | DateTime | DEFAULT NOW() | 创建时间 |
| updatedAt | DateTime | DEFAULT NOW() | 更新时间 |

**索引**：
- `idx_key` (key) - 用于按配置键查询
- `idx_group` (group) - 用于按分组查询

---

## 三、枚举类型定义

### 3.1 用户角色（Role）

```prisma
enum Role {
  ADMIN    # 管理员 - 拥有所有权限
  EDITOR   # 编辑 - 可以管理文章和内容
  USER     # 普通用户 - 只能浏览
}
```

### 3.2 文章状态（ArticleStatus）

```prisma
enum ArticleStatus {
  DRAFT      # 草稿 - 未发布
  PUBLISHED  # 已发布 - 公开可见
  ARCHIVED   # 已归档 - 不再更新
}
```

---

## 四、Prisma Schema 完整定义

```prisma
// schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

// ========== 枚举类型 ==========

enum Role {
  ADMIN
  EDITOR
  USER
}

enum ArticleStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

// ========== 模型定义 ==========

model User {
  id        Int      @id @default(autoincrement())
  username  String   @unique @db.VarChar(50)
  email     String   @unique @db.VarChar(100)
  password  String   @db.VarChar(255)
  role      Role     @default(USER)
  avatar    String?  @db.VarChar(255)
  bio       String?  @db.VarChar(500)
  articles  Article[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@map("users")
}

model Article {
  id           Int            @id @default(autoincrement())
  title        String         @db.VarChar(200)
  content      String         @db.Text
  summary      String?        @db.Text
  coverImage   String?        @db.VarChar(255)
  status       ArticleStatus  @default(DRAFT)
  viewCount    Int            @default(0)
  likeCount    Int            @default(0)
  isFeatured   Boolean        @default(false)
  isTop        Boolean        @default(false)
  publishedAt  DateTime?
  author       User           @relation(fields: [authorId], references: [id])
  authorId     Int
  categories   Category[]     @relation("ArticleCategories")
  tags         Tag[]          @relation("ArticleTags")
  createdAt    DateTime       @default(now())
  updatedAt    DateTime       @updatedAt

  @@index([status, createdAt])
  @@index([authorId])
  @@index([publishedAt])
  @@map("articles")
}

model Category {
  id          Int        @id @default(autoincrement())
  name        String     @db.VarChar(50)
  slug        String     @unique @db.VarChar(50)
  description String?    @db.VarChar(200)
  parentId    Int?
  parent      Category?  @relation("CategoryHierarchy", fields: [parentId], references: [id])
  children    Category[] @relation("CategoryHierarchy")
  articles    Article[]  @relation("ArticleCategories")
  sortOrder   Int        @default(0)
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt

  @@index([slug])
  @@index([parentId])
  @@map("categories")
}

model Tag {
  id        Int       @id @default(autoincrement())
  name      String    @db.VarChar(30)
  slug      String    @unique @db.VarChar(30)
  color     String    @default("#666666") @db.VarChar(7)
  articles  Article[] @relation("ArticleTags")
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt

  @@index([slug])
  @@map("tags")
}

model Link {
  id          Int      @id @default(autoincrement())
  name        String   @db.VarChar(50)
  url         String   @db.VarChar(255)
  description String?  @db.VarChar(200)
  logo        String?  @db.VarChar(255)
  isActive    Boolean  @default(true)
  sortOrder   Int      @default(0)
  verifiedAt  DateTime?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@map("links")
}

model Settings {
  id          Int      @id @default(autoincrement())
  key         String   @unique @db.VarChar(50)
  value       String   @db.Text
  type        String   @default("string") @db.VarChar(20)
  group       String   @default("general") @db.VarChar(30)
  description String?  @db.VarChar(200)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([key])
  @@index([group])
  @@map("settings")
}
```

---

## 五、数据库迁移

### 5.1 初始迁移

```bash
# 创建初始迁移
npx prisma migrate dev --name init

# 生成 Prisma Client
npx prisma generate
```

### 5.2 种子数据

```typescript
// prisma/seed.ts
import { PrismaClient, Role, ArticleStatus } from '@prisma/client'
import * as bcrypt from 'bcrypt'

const prisma = new PrismaClient()

async function main() {
  // 创建管理员用户
  const adminPassword = await bcrypt.hash('admin123', 10)
  await prisma.user.upsert({
    where: { username: 'admin' },
    update: {},
    create: {
      username: 'admin',
      email: 'admin@example.com',
      password: adminPassword,
      role: Role.ADMIN,
      bio: '博客管理员',
    },
  })

  // 创建分类
  const techCategory = await prisma.category.create({
    data: {
      name: '技术文章',
      slug: 'tech',
      description: '技术相关的文章',
    },
  })

  const lifeCategory = await prisma.category.create({
    data: {
      name: '生活随笔',
      slug: 'life',
      description: '生活相关的文章',
    },
  })

  // 创建标签
  await prisma.tag.createMany({
    data: [
      { name: 'JavaScript', slug: 'javascript', color: '#f7df1e' },
      { name: 'TypeScript', slug: 'typescript', color: '#3178c6' },
      { name: 'Vue', slug: 'vue', color: '#42b883' },
      { name: 'NestJS', slug: 'nestjs', color: '#e0234e' },
      { name: '生活', slug: 'life', color: '#ff6b6b' },
    ],
  })

  // 创建示例文章
  await prisma.article.create({
    data: {
      title: 'Hello World - 我的第一篇博客',
      content: '# 你好，世界！\n\n这是我的第一篇博客文章...',
      summary: '这是我的第一篇博客文章，记录开始写博客的时刻。',
      status: ArticleStatus.PUBLISHED,
      authorId: 1,
      categoryIds: [techCategory.id],
      tagIds: [1, 2],
    },
  })

  // 创建系统配置
  await prisma.settings.createMany({
    data: [
      { key: 'site_name', value: '我的个人博客', type: 'string', group: 'general' },
      { key: 'site_description', value: '记录技术，分享生活', type: 'string', group: 'general' },
      { key: 'seo_keywords', value: '博客，技术，分享', type: 'string', group: 'seo' },
      { key: 'posts_per_page', value: '10', type: 'number', group: 'general' },
    ],
  })
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
```

---

## 六、数据库查询示例

### 6.1 获取文章列表（带分页和筛选）

```typescript
// 获取已发布文章列表
const articles = await prisma.article.findMany({
  where: {
    status: 'PUBLISHED',
  },
  include: {
    author: {
      select: { id: true, username: true, avatar: true },
    },
    categories: {
      select: { id: true, name: true, slug: true },
    },
    tags: {
      select: { id: true, name: true, slug: true },
    },
  },
  orderBy: [
    { isTop: 'desc' },
    { publishedAt: 'desc' },
  ],
  skip: 0,
  take: 10,
})

// 获取总数
const total = await prisma.article.count({
  where: { status: 'PUBLISHED' },
})
```

### 6.2 获取分类下的文章

```typescript
const categoryArticles = await prisma.category.findUnique({
  where: { slug: 'tech' },
  include: {
    articles: {
      where: { status: 'PUBLISHED' },
      include: {
        author: { select: { id: true, username: true } },
        tags: true,
      },
      orderBy: { publishedAt: 'desc' },
    },
  },
})
```

### 6.3 获取标签下的文章

```typescript
const tagArticles = await prisma.tag.findUnique({
  where: { slug: 'javascript' },
  include: {
    articles: {
      where: { status: 'PUBLISHED' },
      include: {
        author: { select: { id: true, username: true } },
        categories: true,
      },
      orderBy: { publishedAt: 'desc' },
    },
  },
})
```

### 6.4 获取文章详情

```typescript
const article = await prisma.article.findUnique({
  where: { id: articleId },
  include: {
    author: {
      select: { id: true, username: true, avatar: true, bio: true },
    },
    categories: true,
    tags: true,
  },
})

// 增加阅读量
await prisma.article.update({
  where: { id: articleId },
  data: { viewCount: { increment: 1 } },
})
```

### 6.5 获取统计数据

```typescript
// 文章总数
const totalArticles = await prisma.article.count()

// 已发布文章数
const publishedArticles = await prisma.article.count({
  where: { status: 'PUBLISHED' },
})

// 分类数量
const totalCategories = await prisma.category.count()

// 标签数量
const totalTags = await prisma.tag.count()

// 总阅读量
const totalViews = await prisma.article.aggregate({
  _sum: { viewCount: true },
})

// 总点赞数
const totalLikes = await prisma.article.aggregate({
  _sum: { likeCount: true },
})
```

---

## 七、数据库设计规范

### 7.1 命名规范

| 对象 | 规范 | 示例 |
|------|------|------|
| 表名 | 复数，小写，下划线分隔 | `users`, `articles`, `article_categories` |
| 字段名 | 驼峰命名 | `createdAt`, `updatedAt`, `authorId` |
| 主键 | 统一使用 `id` | `id` |
| 外键 | `关联表名单数 + Id` | `authorId`, `categoryId` |
| 关联表 | 按字母顺序组合表名 | `article_categories`, `article_tags` |
| 索引 | `idx_字段名` 或 `idx_字段 1_字段 2` | `idx_slug`, `idx_status_created` |

### 7.2 数据类型选择

| 用途 | 类型 | 说明 |
|------|------|------|
| 主键 | `Int` | 自增 ID |
| 短文本 | `String @VarChar(n)` | 指定最大长度 |
| 长文本 | `String @Text` | 文章内容等 |
| 布尔值 | `Boolean` | 状态标志 |
| 数字 | `Int` | 计数、排序 |
| 日期时间 | `DateTime` | 创建/更新时间 |
| 可空字段 | `Type?` | 使用 `?` 标记 |

### 7.3 关系定义

| 关系类型 | 定义方式 |
|----------|----------|
| 一对一 | 使用 `@relation` 和唯一外键 |
| 一对多 | 在"多"方使用外键和 `@relation` |
| 多对多 | 使用关联表和 `@relation("关系名")` |

### 7.4 索引设计原则

1. **主键索引** - 自动创建
2. **唯一索引** - `@unique` 标记
3. **外键索引** - 手动创建 `@@index`
4. **查询索引** - 根据常用查询条件创建复合索引
5. **避免过度索引** - 影响写入性能

---

## 八、检查清单

- [ ] 所有表使用 Int 作为主键类型
- [ ] 所有外键使用 Int 类型
- [ ] 字符串字段指定最大长度
- [ ] 常用查询字段创建索引
- [ ] 多对多关系使用关联表
- [ ] 时间字段使用 DateTime 类型
- [ ] 枚举类型定义清晰
- [ ] 种子数据脚本就绪
