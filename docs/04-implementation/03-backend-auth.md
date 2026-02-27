# 后台 - 用户认证实现与测试文档

## 一、功能描述

用户认证模块提供登录、登出、记住登录状态、密码修改等功能。

---

## 二、技术实现

### 2.1 技术栈
- **框架**: NestJS
- **认证**: JWT + bcrypt
- **ORM**: Prisma
- **数据库**: SQLite
- **验证**: class-validator

### 2.2 目录结构

```
backend/src/modules/auth/
├── auth.module.ts         # 认证模块
├── auth.controller.ts     # 认证控制器
├── auth.service.ts        # 认证服务
├── strategies/
│   └── jwt.strategy.ts    # JWT 策略
├── guards/
│   └── jwt-auth.guard.ts  # JWT 守卫
├── decorators/
│   └── user.decorator.ts  # 用户装饰器
└── dto/
    ├── login.dto.ts       # 登录 DTO
    └── register.dto.ts    # 注册 DTO

backend/src/modules/user/
├── user.module.ts
├── user.service.ts
├── user.controller.ts
└── entities/
    └── user.entity.ts
```

### 2.3 数据库设计

```prisma
// schema.prisma
model User {
  id        String   @id @default(uuid())
  username  String   @unique
  email     String   @unique
  password  String
  role      Role     @default(USER)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  @@map('users')
}

enum Role {
  ADMIN
  EDITOR
  USER
}
```

### 2.4 API 接口

| 接口 | 方法 | 描述 | 认证 |
|------|------|------|------|
| `/api/auth/login` | POST | 用户登录 | 否 |
| `/api/auth/logout` | POST | 用户登出 | 是 |
| `/api/auth/refresh` | POST | 刷新 Token | 是 |
| `/api/auth/profile` | GET | 获取用户信息 | 是 |
| `/api/auth/password` | PUT | 修改密码 | 是 |

### 2.5 核心代码实现

#### auth.service.ts
```typescript
import { Injectable, UnauthorizedException } from '@nestjs/common'
import { JwtService } from '@nestjs/jwt'
import { UserService } from '../user/user.service'
import * as bcrypt from 'bcrypt'

@Injectable()
export class AuthService {
  constructor(
    private userService: UserService,
    private jwtService: JwtService
  ) {}

  async validateUser(username: string, password: string): Promise<any> {
    const user = await this.userService.findByUsername(username)
    if (user && await bcrypt.compare(password, user.password)) {
      const { password, ...result } = user
      return result
    }
    return null
  }

  async login(user: any) {
    const payload = { username: user.username, sub: user.id, role: user.role }
    return {
      access_token: this.jwtService.sign(payload),
      user: {
        id: user.id,
        username: user.username,
        email: user.email,
        role: user.role
      }
    }
  }

  async refreshToken(user: any) {
    const payload = { username: user.username, sub: user.id, role: user.role }
    return {
      access_token: this.jwtService.sign(payload)
    }
  }
}
```

#### auth.controller.ts
```typescript
import { Controller, Post, Body, UseGuards, Request } from '@nestjs/common'
import { AuthService } from './auth.service'
import { LoginDto } from './dto/login.dto'
import { JwtAuthGuard } from './guards/jwt-auth.guard'

@Controller('auth')
export class AuthController {
  constructor(private authService: AuthService) {}

  @Post('login')
  async login(@Body() loginDto: LoginDto) {
    const user = await this.authService.validateUser(
      loginDto.username,
      loginDto.password
    )
    if (!user) {
      throw new UnauthorizedException('用户名或密码错误')
    }
    return this.authService.login(user)
  }

  @Post('logout')
  @UseGuards(JwtAuthGuard)
  async logout(@Request() req) {
    // 可以在这里实现 token 黑名单
    return { message: '登出成功' }
  }

  @Get('profile')
  @UseGuards(JwtAuthGuard)
  async getProfile(@Request() req) {
    return req.user
  }
}
```

#### jwt.strategy.ts
```typescript
import { Injectable } from '@nestjs/common'
import { PassportStrategy } from '@nestjs/passport'
import { ExtractJwt, Strategy } from 'passport-jwt'

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET || 'default-secret'
    })
  }

  async validate(payload: any) {
    return { id: payload.sub, username: payload.username, role: payload.role }
  }
}
```

---

## 三、E2E 测试用例

### 3.1 测试文件位置
```
e2e/tests/auth.spec.ts
```

### 3.2 测试用例详情

#### 测试 1: 登录成功
```typescript
import { test, expect } from '@playwright/test'

test.describe('Authentication', () => {
  test('should login successfully with valid credentials', async ({ page }) => {
    // 访问登录页
    await page.goto('/login')
    
    // 填写用户名和密码
    await page.fill('input[name="username"]', 'admin')
    await page.fill('input[name="password"]', 'password123')
    
    // 点击登录按钮
    await page.click('button[type="submit"]')
    
    // 等待跳转
    await page.waitForURL('/admin/dashboard')
    
    // 验证登录成功
    await expect(page.locator('.user-name')).toContainText('admin')
  })
})
```

#### 测试 2: 登录失败
```typescript
test('should show error with invalid credentials', async ({ page }) => {
  await page.goto('/login')
  
  // 填写错误的密码
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'wrongpassword')
  
  // 点击登录
  await page.click('button[type="submit"]')
  
  // 等待错误提示
  await page.waitForSelector('.error-message', { state: 'visible' })
  
  // 验证错误信息
  await expect(page.locator('.error-message')).toContainText('用户名或密码错误')
})
```

#### 测试 3: 表单验证
```typescript
test('should validate form fields', async ({ page }) => {
  await page.goto('/login')
  
  // 不填写任何内容直接提交
  await page.click('button[type="submit"]')
  
  // 验证用户名必填提示
  await expect(page.locator('.username-error')).toBeVisible()
  
  // 只填写用户名
  await page.fill('input[name="username"]', 'admin')
  await page.click('button[type="submit"]')
  
  // 验证密码必填提示
  await expect(page.locator('.password-error')).toBeVisible()
})
```

#### 测试 4: 记住登录状态
```typescript
test('should remember login state', async ({ page }) => {
  // 登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.check('input[name="remember"]')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')
  
  // 关闭页面重新打开
  await page.context().clearCookies()
  await page.goto('/')
  
  // 验证仍然保持登录状态（通过 localStorage 或其他机制）
  // 这取决于具体实现
})
```

#### 测试 5: 登出功能
```typescript
test('should logout successfully', async ({ page }) => {
  // 先登录
  await page.goto('/login')
  await page.fill('input[name="username"]', 'admin')
  await page.fill('input[name="password"]', 'password123')
  await page.click('button[type="submit"]')
  await page.waitForURL('/admin/dashboard')
  
  // 点击登出
  await page.click('.logout-button')
  
  // 等待跳转回登录页或首页
  await page.waitForURL('/login')
  
  // 验证已登出
  await expect(page.locator('input[name="username"]')).toBeVisible()
})
```

#### 测试 6: 受保护路由
```typescript
test('should redirect to login when accessing protected route', async ({ page }) => {
  // 直接访问后台页面
  await page.goto('/admin/dashboard')
  
  // 应该被重定向到登录页
  await page.waitForURL('/login')
  
  // 验证在登录页
  await expect(page.locator('input[name="username"]')).toBeVisible()
})
```

---

## 四、单元测试

### 4.1 测试文件位置
```
backend/src/modules/auth/__tests__/auth.service.spec.ts
```

### 4.2 测试用例

```typescript
import { Test, TestingModule } from '@nestjs/testing'
import { JwtService } from '@nestjs/jwt'
import { AuthService } from '../auth.service'
import { UserService } from '../../user/user.service'
import * as bcrypt from 'bcrypt'

describe('AuthService', () => {
  let authService: AuthService
  let userService: UserService
  
  const mockUserService = {
    findByUsername: jest.fn(),
  }
  
  const mockJwtService = {
    sign: jest.fn(),
  }

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        AuthService,
        { provide: UserService, useValue: mockUserService },
        { provide: JwtService, useValue: mockJwtService },
      ],
    }).compile()

    authService = module.get<AuthService>(AuthService)
    userService = module.get<UserService>(UserService)
  })

  afterEach(() => {
    jest.clearAllMocks()
  })

  describe('validateUser', () => {
    it('should return user without password if valid', async () => {
      const mockUser = {
        id: '1',
        username: 'admin',
        password: await bcrypt.hash('password123', 10),
        email: 'admin@example.com',
      }
      
      mockUserService.findByUsername.mockResolvedValue(mockUser)
      
      const result = await authService.validateUser('admin', 'password123')
      
      expect(result).toEqual({
        id: '1',
        username: 'admin',
        email: 'admin@example.com',
      })
      expect(result.password).toBeUndefined()
    })

    it('should return null if user not found', async () => {
      mockUserService.findByUsername.mockResolvedValue(null)
      
      const result = await authService.validateUser('unknown', 'password')
      
      expect(result).toBeNull()
    })

    it('should return null if password is wrong', async () => {
      const mockUser = {
        id: '1',
        username: 'admin',
        password: await bcrypt.hash('password123', 10),
      }
      
      mockUserService.findByUsername.mockResolvedValue(mockUser)
      
      const result = await authService.validateUser('admin', 'wrongpassword')
      
      expect(result).toBeNull()
    })
  })

  describe('login', () => {
    it('should return access token and user info', async () => {
      const user = { id: '1', username: 'admin', role: 'ADMIN' }
      const mockToken = 'jwt-token-here'
      
      mockJwtService.sign.mockReturnValue(mockToken)
      
      const result = await authService.login(user)
      
      expect(result).toEqual({
        access_token: mockToken,
        user: {
          id: '1',
          username: 'admin',
          role: 'ADMIN',
        },
      })
    })
  })
})
```

---

## 五、API 测试（Supertest）

### 5.1 测试文件位置
```
backend/src/modules/auth/__tests__/auth.e2e-spec.ts
```

### 5.2 测试用例

```typescript
import { Test, TestingModule } from '@nestjs/testing'
import { INestApplication, ValidationPipe } from '@nestjs/common'
import * as request from 'supertest'
import { AuthModule } from '../auth.module'

describe('AuthController (e2e)', () => {
  let app: INestApplication

  beforeAll(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AuthModule],
    }).compile()

    app = moduleFixture.createNestApplication()
    app.useGlobalPipes(new ValidationPipe())
    await app.init()
  })

  afterAll(async () => {
    await app.close()
  })

  describe('/auth/login (POST)', () => {
    it('should return access token on valid login', () => {
      return request(app.getHttpServer())
        .post('/auth/login')
        .send({ username: 'admin', password: 'password123' })
        .expect(200)
        .expect((res) => {
          expect(res.body.access_token).toBeDefined()
          expect(res.body.user).toBeDefined()
        })
    })

    it('should return 401 on invalid credentials', () => {
      return request(app.getHttpServer())
        .post('/auth/login')
        .send({ username: 'admin', password: 'wrongpassword' })
        .expect(401)
    })

    it('should validate request body', () => {
      return request(app.getHttpServer())
        .post('/auth/login')
        .send({})
        .expect(400)
    })
  })
})
```

---

## 六、验收标准

| 标准 | 描述 | 状态 |
|------|------|------|
| 登录功能 | 正确验证用户凭据 | ☐ |
| JWT 认证 | Token 生成和验证正常 | ☐ |
| 密码加密 | 使用 bcrypt 加密存储 | ☐ |
| 表单验证 | 前端和后端都有验证 | ☐ |
| 错误处理 | 错误信息清晰友好 | ☐ |
| 安全性 | 防止暴力破解 | ☐ |
