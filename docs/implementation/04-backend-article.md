# 后台 - 文章管理实现与测试文档

## 一、功能描述

文章管理模块提供文章的 CRUD 操作，包括新建、编辑、删除、发布/下架、置顶等功能。

---

## 二、技术实现

### 2.1 技术栈
- **框架**: NestJS
- **ORM**: Prisma
- **数据库**: SQLite
- **验证**: class-validator
- **API 文档**: Swagger

### 2.2 目录结构

```
backend/src/modules/article/
├── article.module.ts        # 文章模块
├── article.controller.ts    # 文章控制器
├── article.service.ts       # 文章服务
├── entities/
│   └── article.entity.ts    # 文章实体
├── dto/
│   ├── create-article.dto.ts # 创建文章 DTO
│   ├── update-article.dto.ts # 更新文章 DTO
│   └── query-article.dto.ts  # 查询参数 DTO
└── __tests__/
    ├── article.service.spec.ts
    └── article.e2e-spec.ts
```

### 2.3 数据库设计

```prisma
// schema.prisma
model Article {
  id          String   @id @default(uuid())
  title       String
  slug        String   @unique
  content     String   @db.Text
  excerpt     String?
  status      ArticleStatus @default(DRAFT)
  isFeatured  Boolean  @default(false)
  isTop       Boolean  @default(false)
  views       Int      @default(0)
  likes       Int      @default(0)
  authorId    String
  author      User     @relation(fields: [authorId], references: [id])
  categoryId  String?
  category    Category? @relation(fields: [categoryId], references: [id])
  tags        Tag[]    @relation("ArticleTags")
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  publishedAt DateTime?

  @@map('articles')
}

enum ArticleStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

model Category {
  id       String    @id @default(uuid())
  name     String
  slug     String    @unique
  articles Article[]
  createdAt DateTime @default(now())

  @@map('categories')
}

model Tag {
  id       String    @id @default(uuid())
  name     String
  slug     String    @unique
  articles Article[] @relation("ArticleTags")
  createdAt DateTime @default(now())

  @@map('tags')
}
```

### 2.4 API 接口

| 接口 | 方法 | 描述 | 认证 |
|------|------|------|------|
| `/api/articles` | GET | 获取文章列表 | 否 |
| `/api/articles/:id` | GET | 获取文章详情 | 否 |
| `/api/articles` | POST | 创建文章 | 是 |
| `/api/articles/:id` | PUT | 更新文章 | 是 |
| `/api/articles/:id` | DELETE | 删除文章 | 是 |
| `/api/articles/:id/publish` | POST | 发布文章 | 是 |
| `/api/articles/:id/toggle-top` | POST | 切换置顶 | 是 |

### 2.5 核心代码实现

#### article.service.ts
```typescript
import { Injectable, NotFoundException } from '@nestjs/common'
import { PrismaService } from '@/prisma/prisma.service'
import { CreateArticleDto } from './dto/create-article.dto'
import { UpdateArticleDto } from './dto/update-article.dto'
import { QueryArticleDto } from './dto/query-article.dto'

@Injectable()
export class ArticleService {
  constructor(private prisma: PrismaService) {}

  async create(createArticleDto: CreateArticleDto, authorId: string) {
    const slug = await this.generateSlug(createArticleDto.title)
    
    return this.prisma.article.create({
      data: {
        ...createArticleDto,
        slug,
        authorId,
        status: 'DRAFT',
      },
      include: {
        author: { select: { id: true, username: true } },
        category: true,
        tags: true,
      },
    })
  }

  async findAll(query: QueryArticleDto) {
    const { page = 1, pageSize = 10, status, categoryId, tagId, keyword } = query

    const where: any = {}

    if (status) {
      where.status = status
    }

    if (categoryId) {
      where.categoryId = categoryId
    }

    if (tagId) {
      where.tags = { some: { id: tagId } }
    }

    if (keyword) {
      where.OR = [
        { title: { contains: keyword } },
        { content: { contains: keyword } },
      ]
    }

    const [total, articles] = await this.prisma.$transaction([
      this.prisma.article.count({ where }),
      this.prisma.article.findMany({
        where,
        include: {
          author: { select: { id: true, username: true } },
          category: true,
          tags: true,
        },
        orderBy: [
          { isTop: 'desc' },
          { publishedAt: 'desc' },
        ],
        skip: (page - 1) * pageSize,
        take: pageSize,
      }),
    ])

    return { data: articles, total }
  }

  async findOne(id: string) {
    const article = await this.prisma.article.findUnique({
      where: { id },
      include: {
        author: { select: { id: true, username: true, email: true } },
        category: true,
        tags: true,
      },
    })

    if (!article) {
      throw new NotFoundException(`文章 ${id} 不存在`)
    }

    return article
  }

  async update(id: string, updateArticleDto: UpdateArticleDto) {
    await this.findOne(id) // 验证文章存在

    const data = { ...updateArticleDto }
    
    // 如果标题改变，重新生成 slug
    if (updateArticleDto.title) {
      data.slug = await this.generateSlug(updateArticleDto.title)
    }

    return this.prisma.article.update({
      where: { id },
      data,
      include: {
        author: { select: { id: true, username: true } },
        category: true,
        tags: true,
      },
    })
  }

  async remove(id: string) {
    await this.findOne(id)
    return this.prisma.article.delete({ where: { id } })
  }

  async publish(id: string) {
    return this.prisma.article.update({
      where: { id },
      data: {
        status: 'PUBLISHED',
        publishedAt: new Date(),
      },
    })
  }

  async toggleTop(id: string) {
    const article = await this.findOne(id)
    return this.prisma.article.update({
      where: { id },
      data: { isTop: !article.isTop },
    })
  }

  async incrementViews(id: string) {
    return this.prisma.article.update({
      where: { id },
      data: { views: { increment: 1 } },
    })
  }

  async incrementLikes(id: string) {
    return this.prisma.article.update({
      where: { id },
      data: { likes: { increment: 1 } },
    })
  }

  private async generateSlug(title: string): Promise<string> {
    const baseSlug = title
      .toLowerCase()
      .replace(/[^a-z0-9]+/g, '-')
      .replace(/(^-|-$)/g, '')
    
    let slug = baseSlug
    let counter = 1
    
    while (await this.prisma.article.findUnique({ where: { slug } })) {
      slug = `${baseSlug}-${counter}`
      counter++
    }
    
    return slug
  }
}
```

#### article.controller.ts
```typescript
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  Query,
  UseGuards,
  Request,
} from '@nestjs/common'
import { ArticleService } from './article.service'
import { CreateArticleDto } from './dto/create-article.dto'
import { UpdateArticleDto } from './dto/update-article.dto'
import { QueryArticleDto } from './dto/query-article.dto'
import { JwtAuthGuard } from '../auth/guards/jwt-auth.guard'

@Controller('articles')
export class ArticleController {
  constructor(private articleService: ArticleService) {}

  @Get()
  async findAll(@Query() query: QueryArticleDto) {
    return this.articleService.findAll(query)
  }

  @Get(':id')
  async findOne(@Param('id') id: string) {
    return this.articleService.findOne(id)
  }

  @Post()
  @UseGuards(JwtAuthGuard)
  async create(@Body() createArticleDto: CreateArticleDto, @Request() req) {
    return this.articleService.create(createArticleDto, req.user.id)
  }

  @Put(':id')
  @UseGuards(JwtAuthGuard)
  async update(
    @Param('id') id: string,
    @Body() updateArticleDto: UpdateArticleDto,
  ) {
    return this.articleService.update(id, updateArticleDto)
  }

  @Delete(':id')
  @UseGuards(JwtAuthGuard)
  async remove(@Param('id') id: string) {
    return this.articleService.remove(id)
  }

  @Post(':id/publish')
  @UseGuards(JwtAuthGuard)
  async publish(@Param('id') id: string) {
    return this.articleService.publish(id)
  }

  @Post(':id/toggle-top')
  @UseGuards(JwtAuthGuard)
  async toggleTop(@Param('id') id: string) {
    return this.articleService.toggleTop(id)
  }
}
```

---

## 三、E2E 测试用例

### 3.1 测试文件位置
```
e2e/tests/article-management.spec.ts
```

### 3.2 测试用例详情

#### 测试 1: 创建文章
```typescript
import { test, expect } from '@playwright/test'

test.describe('Article Management', () => {
  test('should create a new article', async ({ page }) => {
    // 先登录
    await page.goto('/login')
    await page.fill('input[name="username"]', 'admin')
    await page.fill('input[name="password"]', 'password123')
    await page.click('button[type="submit"]')
    await page.waitForURL('/admin/dashboard')

    // 导航到新建文章页面
    await page.goto('/admin/articles/new')

    // 填写文章信息
    await page.fill('input[name="title"]', '我的第一篇文章')
    await page.fill('textarea[name="content"]', '# Hello World\n\n这是文章内容')
    await page.fill('input[name="excerpt"]', '文章摘要')

    // 选择分类
    await page.selectOption('select[name="categoryId"]', '1')

    // 点击保存
    await page.click('button[type="submit"]')

    // 等待保存成功并跳转
    await page.waitForURL('/admin/articles')

    // 验证文章创建成功
    await expect(page.locator('.article-list')).toContainText('我的第一篇文章')
  })
})
```

#### 测试 2: 编辑文章
```typescript
test('should edit an existing article', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入文章列表
  await page.goto('/admin/articles')

  // 点击编辑按钮
  await page.click('.article-item:first-child .edit-btn')

  // 等待编辑页面加载
  await page.waitForURL(/\/admin\/articles\/.*\/edit/)

  // 修改标题
  await page.fill('input[name="title"]', '修改后的标题')

  // 保存
  await page.click('button[type="submit"]')

  // 验证修改成功
  await page.waitForURL('/admin/articles')
  await expect(page.locator('.article-list')).toContainText('修改后的标题')
})
```

#### 测试 3: 删除文章
```typescript
test('should delete an article', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入文章列表
  await page.goto('/admin/articles')

  // 获取删除前的文章数量
  const initialCount = await page.locator('.article-item').count()

  // 点击删除按钮
  await page.click('.article-item:last-child .delete-btn')

  // 确认删除
  await page.waitForSelector('.confirm-dialog')
  await page.click('.confirm-dialog .confirm-btn')

  // 等待删除完成
  await page.waitForResponse(/\/api\/articles\/\d+/)

  // 验证文章数量减少
  const finalCount = await page.locator('.article-item').count()
  expect(finalCount).toBe(initialCount - 1)
})
```

#### 测试 4: 发布文章
```typescript
test('should publish a draft article', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入草稿列表
  await page.goto('/admin/articles?status=draft')

  // 点击发布按钮
  await page.click('.article-item:first-child .publish-btn')

  // 确认发布
  await page.waitForSelector('.confirm-dialog')
  await page.click('.confirm-dialog .confirm-btn')

  // 等待发布完成
  await page.waitForResponse(/\/api\/articles\/.*\/publish/)

  // 验证发布成功提示
  await expect(page.locator('.toast-success')).toBeVisible()
})
```

#### 测试 5: 置顶文章
```typescript
test('should toggle article top status', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入文章列表
  await page.goto('/admin/articles')

  // 点击置顶按钮
  await page.click('.article-item:first-child .toggle-top-btn')

  // 等待响应
  await page.waitForResponse(/\/api\/articles\/.*\/toggle-top/)

  // 验证置顶状态变化
  const firstItem = page.locator('.article-item:first-child')
  await expect(firstItem).toHaveClass(/is-top/)
})
```

#### 测试 6: 文章列表分页
```typescript
test('should handle article list pagination', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入文章列表
  await page.goto('/admin/articles')

  // 验证分页控件存在
  await expect(page.locator('.pagination')).toBeVisible()

  // 点击下一页
  await page.click('.pagination .next-page')

  // 等待数据加载
  await page.waitForResponse(/\/api\/articles\?page=2/)

  // 验证页码更新
  await expect(page.locator('.pagination .current-page')).toHaveText('2')
})
```

#### 测试 7: 文章搜索
```typescript
test('should search articles', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')

  // 进入文章列表
  await page.goto('/admin/articles')

  // 输入搜索关键词
  await page.fill('input[name="search"]', '测试')

  // 等待搜索结果
  await page.waitForResponse(/\/api\/articles\?.*keyword=/)

  // 验证搜索结果
  const articles = page.locator('.article-item')
  const count = await articles.count()
  
  // 验证所有结果都包含关键词
  for (let i = 0; i < count; i++) {
    await expect(articles.nth(i)).toContainText('测试')
  }
})
```

---

## 四、单元测试

### 4.1 测试文件位置
```
backend/src/modules/article/__tests__/article.service.spec.ts
```

### 4.2 测试用例

```typescript
import { Test, TestingModule } from '@nestjs/testing'
import { NotFoundException } from '@nestjs/common'
import { ArticleService } from '../article.service'
import { PrismaService } from '@/prisma/prisma.service'

describe('ArticleService', () => {
  let service: ArticleService
  let prisma: PrismaService

  const mockPrismaService = {
    article: {
      create: jest.fn(),
      findMany: jest.fn(),
      findUnique: jest.fn(),
      update: jest.fn(),
      delete: jest.fn(),
      count: jest.fn(),
    },
    $transaction: jest.fn(),
  }

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        ArticleService,
        { provide: PrismaService, useValue: mockPrismaService },
      ],
    }).compile()

    service = module.get<ArticleService>(ArticleService)
    prisma = module.get<PrismaService>(PrismaService)
  })

  afterEach(() => {
    jest.clearAllMocks()
  })

  describe('create', () => {
    it('should create a new article', async () => {
      const createDto = {
        title: 'Test Article',
        content: 'Content here',
        excerpt: 'Summary',
      }
      const authorId = 'user-1'
      const expectedArticle = {
        id: '1',
        ...createDto,
        slug: 'test-article',
        status: 'DRAFT',
        authorId,
      }

      mockPrismaService.article.create.mockResolvedValue(expectedArticle)

      const result = await service.create(createDto, authorId)

      expect(result).toEqual(expectedArticle)
      expect(prisma.article.create).toHaveBeenCalledWith({
        data: expect.objectContaining({
          ...createDto,
          slug: expect.any(String),
          authorId,
          status: 'DRAFT',
        }),
      })
    })
  })

  describe('findOne', () => {
    it('should return an article', async () => {
      const mockArticle = { id: '1', title: 'Test Article' }
      mockPrismaService.article.findUnique.mockResolvedValue(mockArticle)

      const result = await service.findOne('1')

      expect(result).toEqual(mockArticle)
    })

    it('should throw NotFoundException when article not found', async () => {
      mockPrismaService.article.findUnique.mockResolvedValue(null)

      await expect(service.findOne('999')).rejects.toThrow(NotFoundException)
    })
  })

  describe('update', () => {
    it('should update an article', async () => {
      const updateDto = { title: 'Updated Title' }
      const updatedArticle = { id: '1', ...updateDto }

      mockPrismaService.article.findUnique.mockResolvedValue({ id: '1' })
      mockPrismaService.article.update.mockResolvedValue(updatedArticle)

      const result = await service.update('1', updateDto)

      expect(result).toEqual(updatedArticle)
    })
  })

  describe('remove', () => {
    it('should delete an article', async () => {
      mockPrismaService.article.findUnique.mockResolvedValue({ id: '1' })
      mockPrismaService.article.delete.mockResolvedValue({ id: '1' })

      const result = await service.remove('1')

      expect(result).toEqual({ id: '1' })
    })
  })

  describe('publish', () => {
    it('should publish an article', async () => {
      const publishedArticle = {
        id: '1',
        status: 'PUBLISHED',
        publishedAt: expect.any(Date),
      }

      mockPrismaService.article.update.mockResolvedValue(publishedArticle)

      const result = await service.publish('1')

      expect(result.status).toBe('PUBLISHED')
      expect(result.publishedAt).toBeDefined()
    })
  })
})
```

---

## 五、验收标准

| 标准 | 描述 | 状态 |
|------|------|------|
| CRUD 操作 | 创建、读取、更新、删除正常 | ☐ |
| 发布功能 | 草稿/发布状态切换正常 | ☐ |
| 置顶功能 | 置顶状态正确切换 | ☐ |
| 分页查询 | 分页和筛选正常工作 | ☐ |
| 权限控制 | 只有认证用户可操作 | ☐ |
| 数据验证 | 输入数据正确验证 | ☐ |
