# 后端项目结构设计文档

> 本文档定义了个人博客系统后端项目的目录结构、模块划分和设计规范。

---

## 一、项目目录结构

```
backend/
├── prisma/                      # Prisma 数据库配置
│   ├── schema.prisma            # 数据模型定义
│   ├── migrations/              # 数据库迁移文件
│   └── seed.ts                  # 种子数据脚本
│
├── src/                         # 源代码目录
│   ├── main.ts                  # 应用入口文件
│   ├── app.module.ts            # 根模块
│   │
│   ├── common/                  # 公共模块
│   │   ├── decorators/          # 自定义装饰器
│   │   │   ├── public.decorator.ts    # 公开接口装饰器
│   │   │   └── user.decorator.ts      # 用户装饰器
│   │   │
│   │   ├── filters/             # 异常过滤器
│   │   │   ├── http-exception.filter.ts  # HTTP 异常过滤器
│   │   │   └── validation-exception.filter.ts  # 验证异常过滤器
│   │   │
│   │   ├── guards/              # 守卫
│   │   │   ├── jwt-auth.guard.ts       # JWT 认证守卫
│   │   │   ├── local-auth.guard.ts     # 本地认证守卫
│   │   │   └── roles.guard.ts          # 角色守卫
│   │   │
│   │   ├── interceptors/        # 拦截器
│   │   │   ├── response.interceptor.ts  # 响应拦截器
│   │   │   ├── logging.interceptor.ts   # 日志拦截器
│   │   │   └── cache.interceptor.ts     # 缓存拦截器
│   │   │
│   │   ├── pipes/               # 管道
│   │   │   ├── validation.pipe.ts       # 验证管道
│   │   │   └── parse-int.pipe.ts        # 整数解析管道
│   │   │
│   │   └── middleware/          # 中间件
│   │       ├── logger.middleware.ts     # 日志中间件
│   │       └── cors.middleware.ts       # CORS 中间件
│   │
│   ├── config/                  # 配置文件
│   │   ├── index.ts             # 配置入口
│   │   ├── database.config.ts   # 数据库配置
│   │   ├── jwt.config.ts        # JWT 配置
│   │   └── app.config.ts        # 应用配置
│   │
│   ├── modules/                 # 功能模块
│   │   ├── auth/                # 认证模块
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── strategies/
│   │   │   │   └── jwt.strategy.ts
│   │   │   ├── guards/
│   │   │   │   └── jwt-auth.guard.ts
│   │   │   └── dto/
│   │   │       ├── login.dto.ts
│   │   │       └── register.dto.ts
│   │   │
│   │   ├── user/                # 用户模块
│   │   │   ├── user.module.ts
│   │   │   ├── user.controller.ts
│   │   │   ├── user.service.ts
│   │   │   └── dto/
│   │   │       ├── create-user.dto.ts
│   │   │       └── update-user.dto.ts
│   │   │
│   │   ├── article/             # 文章模块
│   │   │   ├── article.module.ts
│   │   │   ├── article.controller.ts
│   │   │   ├── article.service.ts
│   │   │   └── dto/
│   │   │       ├── create-article.dto.ts
│   │   │       └── update-article.dto.ts
│   │   │
│   │   ├── category/            # 分类模块
│   │   │   ├── category.module.ts
│   │   │   ├── category.controller.ts
│   │   │   ├── category.service.ts
│   │   │   └── dto/
│   │   │       ├── create-category.dto.ts
│   │   │       └── update-category.dto.ts
│   │   │
│   │   ├── tag/                 # 标签模块
│   │   │   ├── tag.module.ts
│   │   │   ├── tag.controller.ts
│   │   │   ├── tag.service.ts
│   │   │   └── dto/
│   │   │       ├── create-tag.dto.ts
│   │   │       └── update-tag.dto.ts
│   │   │
│   │   ├── link/                # 友链模块
│   │   │   ├── link.module.ts
│   │   │   ├── link.controller.ts
│   │   │   └── link.service.ts
│   │   │
│   │   ├── settings/            # 系统设置模块
│   │   │   ├── settings.module.ts
│   │   │   ├── settings.controller.ts
│   │   │   └── settings.service.ts
│   │   │
│   │   ├── dashboard/           # 仪表盘模块
│   │   │   ├── dashboard.module.ts
│   │   │   ├── dashboard.controller.ts
│   │   │   └── dashboard.service.ts
│   │   │
│   │   └── logger/              # 日志模块
│   │       ├── logger.module.ts
│   │       ├── logger.controller.ts
│   │       └── logger.service.ts
│   │
│   ├── types/                   # TypeScript 类型定义
│   │   ├── index.ts             # 类型入口
│   │   ├── common.ts            # 通用类型
│   │   ├── response.ts          # 响应类型
│   │   └── request.ts           # 请求类型
│   │
│   └── utils/                   # 工具函数
│       ├── index.ts             # 工具入口
│       ├── bcrypt.ts            # 加密工具
│       ├── jwt.ts               # JWT 工具
│       └── logger.ts            # 日志工具
│
├── test/                        # 测试文件
│   ├── e2e/                     # E2E 测试
│   │   ├── auth.e2e-spec.ts     # 认证测试
│   │   ├── article.e2e-spec.ts  # 文章测试
│   │   └── setup.ts             # 测试配置
│   └── unit/                    # 单元测试
│       ├── auth/                # 认证测试
│       ├── article/             # 文章测试
│       └── ...
│
├── .env                         # 环境变量
├── .env.development             # 开发环境变量
├── .env.production              # 生产环境变量
├── .eslintrc.js                 # ESLint 配置
├── .prettierrc                  # Prettier 配置
├── nest-cli.json                # NestJS 配置
├── package.json                 # 依赖配置
├── tsconfig.json                # TypeScript 配置
└── tsconfig.build.json          # 构建配置
```

---

## 二、模块划分

### 2.1 核心模块

| 模块 | 说明 | 目录 |
|------|------|------|
| 认证模块 | JWT 认证、用户登录 | `src/modules/auth/` |
| 用户模块 | 用户管理、角色管理 | `src/modules/user/` |
| 公共模块 | 守卫、过滤器、拦截器 | `src/common/` |
| 配置模块 | 应用配置、数据库配置 | `src/config/` |

### 2.2 功能模块

| 模块 | 说明 | 主要功能 |
|------|------|----------|
| 文章模块 | 文章 CRUD、发布管理 | 创建、编辑、删除、发布、置顶 |
| 分类模块 | 分类管理 | 分类 CRUD、树形结构 |
| 标签模块 | 标签管理 | 标签 CRUD、标签云 |
| 友链模块 | 友情链接管理 | 友链 CRUD、排序 |
| 设置模块 | 系统配置 | 配置 CRUD、分组管理 |
| 仪表盘模块 | 数据统计 | 访问统计、文章统计 |
| 日志模块 | 操作日志 | 日志记录、查询 |

---

## 三、模块设计规范

### 3.1 Module 定义

```typescript
// src/modules/article/article.module.ts
import { Module } from '@nestjs/common'
import { TypeOrmModule } from '@nestjs/typeorm'
import { ArticleController } from './article.controller'
import { ArticleService } from './article.service'
import { Article } from './entities/article.entity'
import { CategoryModule } from '../category/category.module'
import { TagModule } from '../tag/tag.module'

@Module({
  imports: [
    TypeOrmModule.forFeature([Article]),
    CategoryModule,
    TagModule,
  ],
  controllers: [ArticleController],
  providers: [ArticleService],
  exports: [ArticleService],
})
export class ArticleModule {}
```

### 3.2 Controller 定义

```typescript
// src/modules/article/article.controller.ts
import {
  Controller,
  Get,
  Post,
  Body,
  Param,
  ParseIntPipe,
  Query,
  UseGuards,
  Request,
} from '@nestjs/common'
import { ApiTags, ApiOperation, ApiBearerAuth } from '@nestjs/swagger'
import { ArticleService } from './article.service'
import { CreateArticleDto } from './dto/create-article.dto'
import { UpdateArticleDto } from './dto/update-article.dto'
import { JwtAuthGuard } from 'src/common/guards/jwt-auth.guard'

@ApiTags('文章管理')
@Controller('articles')
export class ArticleController {
  constructor(private readonly articleService: ArticleService) {}

  @Get()
  @ApiOperation({ summary: '获取文章列表' })
  findAll(@Query() query: any) {
    return this.articleService.findAll(query)
  }

  @Get(':id')
  @ApiOperation({ summary: '获取文章详情' })
  findOne(@Param('id', ParseIntPipe) id: number) {
    return this.articleService.findOne(id)
  }

  @Post()
  @UseGuards(JwtAuthGuard)
  @ApiBearerAuth()
  @ApiOperation({ summary: '创建文章' })
  create(@Body() createArticleDto: CreateArticleDto, @Request() req) {
    return this.articleService.create(createArticleDto, req.user.id)
  }

  @Put(':id')
  @UseGuards(JwtAuthGuard)
  @ApiBearerAuth()
  @ApiOperation({ summary: '更新文章' })
  update(
    @Param('id', ParseIntPipe) id: number,
    @Body() updateArticleDto: UpdateArticleDto,
  ) {
    return this.articleService.update(id, updateArticleDto)
  }

  @Delete(':id')
  @UseGuards(JwtAuthGuard)
  @ApiBearerAuth()
  @ApiOperation({ summary: '删除文章' })
  remove(@Param('id', ParseIntPipe) id: number) {
    return this.articleService.remove(id)
  }
}
```

### 3.3 Service 定义

```typescript
// src/modules/article/article.service.ts
import { Injectable, NotFoundException } from '@nestjs/common'
import { InjectRepository } from '@nestjs/typeorm'
import { Repository } from 'typeorm'
import { Article } from './entities/article.entity'
import { CreateArticleDto } from './dto/create-article.dto'
import { UpdateArticleDto } from './dto/update-article.dto'

@Injectable()
export class ArticleService {
  constructor(
    @InjectRepository(Article)
    private readonly articleRepository: Repository<Article>,
  ) {}

  async create(
    createArticleDto: CreateArticleDto,
    authorId: number,
  ): Promise<Article> {
    const article = this.articleRepository.create({
      ...createArticleDto,
      authorId,
    })
    return this.articleRepository.save(article)
  }

  async findAll(query: any): Promise<{ items: Article[]; total: number }> {
    const { page = 1, size = 10, status } = query
    const [items, total] = await this.articleRepository.findAndCount({
      where: status ? { status } : {},
      relations: ['author', 'categories', 'tags'],
      skip: (page - 1) * size,
      take: size,
      order: { createdAt: 'DESC' },
    })
    return { items, total }
  }

  async findOne(id: number): Promise<Article> {
    const article = await this.articleRepository.findOne({
      where: { id },
      relations: ['author', 'categories', 'tags'],
    })
    if (!article) {
      throw new NotFoundException(`文章 ${id} 不存在`)
    }
    return article
  }

  async update(id: number, updateArticleDto: UpdateArticleDto): Promise<Article> {
    const article = await this.findOne(id)
    Object.assign(article, updateArticleDto)
    return this.articleRepository.save(article)
  }

  async remove(id: number): Promise<void> {
    const article = await this.findOne(id)
    await this.articleRepository.remove(article)
  }
}
```

### 3.4 DTO 定义

```typescript
// src/modules/article/dto/create-article.dto.ts
import {
  IsString,
  IsNotEmpty,
  IsOptional,
  IsArray,
  IsInt,
  IsBoolean,
  MinLength,
  MaxLength,
} from 'class-validator'
import { ApiProperty } from '@nestjs/swagger'

export class CreateArticleDto {
  @ApiProperty({ description: '文章标题', example: 'Hello World' })
  @IsString()
  @IsNotEmpty()
  @MinLength(1)
  @MaxLength(200)
  title: string

  @ApiProperty({ description: '文章内容' })
  @IsString()
  @IsNotEmpty()
  content: string

  @ApiProperty({ description: '文章摘要', required: false })
  @IsString()
  @IsOptional()
  @MaxLength(500)
  summary?: string

  @ApiProperty({ description: '封面图片 URL', required: false })
  @IsString()
  @IsOptional()
  coverImage?: string

  @ApiProperty({ description: '分类 ID 数组', required: false })
  @IsOptional()
  @IsArray()
  @IsInt({ each: true })
  categoryIds?: number[]

  @ApiProperty({ description: '标签 ID 数组', required: false })
  @IsOptional()
  @IsArray()
  @IsInt({ each: true })
  tagIds?: number[]

  @ApiProperty({ description: '是否精选', required: false })
  @IsOptional()
  @IsBoolean()
  isFeatured?: boolean

  @ApiProperty({ description: '是否置顶', required: false })
  @IsOptional()
  @IsBoolean()
  isTop?: boolean
}
```

---

## 四、认证模块设计

### 4.1 JWT 策略

```typescript
// src/modules/auth/strategies/jwt.strategy.ts
import { Injectable } from '@nestjs/common'
import { PassportStrategy } from '@nestjs/passport'
import { ExtractJwt, Strategy } from 'passport-jwt'
import { ConfigService } from '@nestjs/config'

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private configService: ConfigService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get('JWT_SECRET'),
    })
  }

  async validate(payload: any) {
    return {
      id: payload.sub,
      username: payload.username,
      role: payload.role,
    }
  }
}
```

### 4.2 认证服务

```typescript
// src/modules/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common'
import { JwtService } from '@nestjs/jwt'
import { UserService } from '../user/user.service'
import * as bcrypt from 'bcrypt'

@Injectable()
export class AuthService {
  constructor(
    private userService: UserService,
    private jwtService: JwtService,
  ) {}

  async validateUser(username: string, password: string): Promise<any> {
    const user = await this.userService.findByUsername(username)
    if (user && (await bcrypt.compare(password, user.password))) {
      const { password, ...result } = user
      return result
    }
    return null
  }

  async login(user: any) {
    const payload = {
      username: user.username,
      sub: user.id,
      role: user.role,
    }
    return {
      access_token: this.jwtService.sign(payload),
      expires_in: 7200,
      user: {
        id: user.id,
        username: user.username,
        email: user.email,
        role: user.role,
      },
    }
  }
}
```

---

## 五、异常处理

### 5.1 异常过滤器

```typescript
// src/common/filters/http-exception.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
} from '@nestjs/common'
import { Request, Response } from 'express'

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp()
    const response = ctx.getResponse<Response>()
    const request = ctx.getRequest<Request>()

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR

    const message =
      exception instanceof HttpException
        ? exception.message
        : 'Internal server error'

    response.status(status).json({
      success: false,
      error: {
        code: exception instanceof HttpException ? 'HTTP_ERROR' : 'INTERNAL_ERROR',
        message,
      },
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    })
  }
}
```

### 5.2 自定义异常

```typescript
// src/common/exceptions/business.exception.ts
import { HttpException, HttpStatus } from '@nestjs/common'

export class BusinessException extends HttpException {
  constructor(code: string, message: string, statusCode?: number) {
    super(
      {
        success: false,
        error: { code, message },
        statusCode: statusCode || HttpStatus.BAD_REQUEST,
      },
      statusCode || HttpStatus.BAD_REQUEST,
    )
  }
}

// 使用示例
throw new BusinessException(
  'ARTICLE_NOT_FOUND',
  `文章 ${id} 不存在`,
  HttpStatus.NOT_FOUND,
)
```

---

## 六、响应拦截器

```typescript
// src/common/interceptors/response.interceptor.ts
import {
  CallHandler,
  ExecutionContext,
  Injectable,
  NestInterceptor,
} from '@nestjs/common'
import { Observable } from 'rxjs'
import { map } from 'rxjs/operators'

export interface Response<T> {
  success: boolean
  data: T
  message?: string
}

@Injectable()
export class ResponseInterceptor<T> implements NestInterceptor<T, Response<T>> {
  intercept(context: ExecutionContext, next: CallHandler): Observable<Response<T>> {
    return next.handle().pipe(
      map((data) => ({
        success: true,
        data,
      })),
    )
  }
}
```

---

## 七、配置管理

### 7.1 数据库配置

```typescript
// src/config/database.config.ts
import { TypeOrmModuleOptions } from '@nestjs/typeorm'

export const databaseConfig = (): TypeOrmModuleOptions => ({
  type: 'sqlite',
  database: process.env.DATABASE_PATH || 'database.db',
  entities: [__dirname + '/../**/*.entity{.ts,.js}'],
  synchronize: process.env.NODE_ENV === 'development',
  logging: process.env.NODE_ENV === 'development',
})
```

### 7.2 JWT 配置

```typescript
// src/config/jwt.config.ts
export const jwtConfig = () => ({
  secret: process.env.JWT_SECRET || 'default-secret-key',
  expiresIn: parseInt(process.env.JWT_EXPIRES_IN, 10) || 7200,
})
```

---

## 八、检查清单

- [ ] 目录结构清晰合理
- [ ] 模块划分明确
- [ ] Controller/Service/Entity 分离
- [ ] DTO 验证完善
- [ ] 认证授权正确实施
- [ ] 异常处理统一
- [ ] 响应格式统一
- [ ] 配置文件完善
