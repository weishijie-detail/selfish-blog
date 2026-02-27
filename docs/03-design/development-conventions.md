# 开发规范与约定

> 本文档定义了项目开发过程中需要遵循的开发规范和约定，确保代码一致性和可维护性。
> 
> **注意**：关于项目结构、API 设计、数据库设计等详细内容，请参阅相关专项文档：
> - [前端结构设计](./frontend-structure.md)
> - [后端结构设计](./backend-structure.md)
> - [API 设计](./api-design.md)
> - [API 响应格式](./api-response-format.md)
> - [数据库设计](./database-schema.md)
> - [路由设计](./router-design.md)
> - [状态管理](./state-management.md)

---

## 一、命名规范

### 1.1 文件命名

| 类型 | 规范 | 示例 |
|------|------|------|
| Vue 组件 | PascalCase | `ArticleList.vue`, `UserProfile.vue` |
| TypeScript 文件 | camelCase | `userService.ts`, `articleStore.ts` |
| 测试文件 | `*.spec.ts` | `auth.service.spec.ts` |
| E2E 测试 | `*.spec.ts` | `home.spec.ts`, `login.spec.ts` |
| 样式文件 | camelCase | `globalStyles.css` |
| 工具函数 | camelCase | `dateUtils.ts`, `stringUtils.ts` |
| 类型定义 | PascalCase | `user.types.ts`, `article.types.ts` |
| 常量文件 | UPPER_CASE | `API_ENDPOINTS.ts`, `ERROR_CODES.ts` |

### 1.2 变量和函数命名

```typescript
// 变量命名
const userName: string = 'John';           // camelCase
const MAX_RETRY_COUNT = 3;                 // UPPER_SNAKE_CASE (常量)
const ArticleList: Component = {...};      // PascalCase (组件)

// 函数命名
function getUserInfo() {}                  // camelCase
function createUser() {}                   // 动词 + 名词
function isUserLoggedIn() {}               // is/has/should 前缀 (布尔返回)
function formatDate() {}                   // 动词 + 名词

// 类命名
class UserService {}                       // PascalCase
class ArticleController {}                 // PascalCase

// 接口和类型
interface User { }                         // PascalCase
type ArticleStatus = 'draft' | 'published'; // PascalCase
```

### 1.3 CSS 类名命名

```css
/* BEM 命名规范 */
.article-list { }                          /* Block */
.article-list__item { }                    /* Element */
.article-list__item--active { }            /* Modifier */

/* 使用连字符分隔 */
.user-profile { }
.article-detail { }
.tag-cloud { }
```

---

## 二、代码风格规范

### 2.1 TypeScript 规范

```typescript
// 1. 类型注解使用
const name: string = 'John';
const age: number = 25;
const hobbies: string[] = ['reading', 'coding'];

// 2. 接口定义
interface User {
  id: number;
  name: string;
  email: string;
  createdAt: Date;
}

// 3. 类型别名
type ArticleStatus = 'draft' | 'published' | 'archived';

// 4. 泛型使用
function getData<T>(url: string): Promise<T> {
  return fetch(url).then(res => res.json());
}

// 5. 可选链和空值合并
const userName = user?.profile?.name ?? 'Anonymous';

// 6. 枚举使用
enum ArticleStatus {
  Draft = 'draft',
  Published = 'published',
  Archived = 'archived'
}
```

### 2.2 Vue 组件规范

```vue
<script setup lang="ts">
// 1. 导入顺序
// 第三方库
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

// 项目内模块
import { useArticleStore } from '@/stores/article'
import ArticleCard from '@/components/business/ArticleCard.vue'

// 类型和常量
import type { Article } from '@/types/article'
import { DEFAULT_PAGE_SIZE } from '@/constants'

// 2. Props 定义
interface Props {
  articles: Article[]
  loading?: boolean
  pageSize?: number
}

const props = withDefaults(defineProps<Props>(), {
  loading: false,
  pageSize: DEFAULT_PAGE_SIZE
})

// 3. Emits 定义
interface Emits {
  (e: 'update:articles', articles: Article[]): void
  (e: 'load-more'): void
}

const emit = defineEmits<Emits>()

// 4. 响应式数据
const currentPage = ref(1)

// 5. 计算属性
const totalPages = computed(() => {
  return Math.ceil(props.articles.length / props.pageSize)
})

// 6. 方法
const handleLoadMore = () => {
  emit('load-more')
}
</script>

<template>
  <!-- 模板内容 -->
</template>

<style scoped>
/* 样式 */
</style>
```

### 2.3 NestJS 规范

```typescript
// 1. Controller 规范
@Controller('articles')
export class ArticleController {
  constructor(private readonly articleService: ArticleService) {}

  @Get()
  @ApiOperation({ summary: '获取文章列表' })
  async findAll(@Query() query: GetArticlesDto): Promise<PaginatedResponse<Article>> {
    return this.articleService.findAll(query);
  }

  @Get(':id')
  @ApiOperation({ summary: '获取文章详情' })
  async findOne(@Param('id', ParseIntPipe) id: number): Promise<Article> {
    return this.articleService.findOne(id);
  }
}

// 2. Service 规范
@Injectable()
export class ArticleService {
  constructor(
    @InjectRepository(Article)
    private readonly articleRepository: Repository<Article>,
    private readonly categoryService: CategoryService,
  ) {}

  async findAll(dto: GetArticlesDto): Promise<PaginatedResponse<Article>> {
    // 业务逻辑
  }
}

// 3. Module 规范
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

// 4. DTO 规范
export class CreateArticleDto {
  @IsString()
  @IsNotEmpty()
  title: string;

  @IsString()
  @IsNotEmpty()
  content: string;

  @IsOptional()
  @IsString()
  summary?: string;

  @IsOptional()
  @IsArray()
  @IsInt({ each: true })
  categoryIds?: number[];

  @IsOptional()
  @IsArray()
  @IsInt({ each: true })
  tagIds?: number[];
}
```

---

## 三、Git 提交规范

> 详细的 Git 提交规范请参阅 [Git 提交指南](../02-specifications/git-commit-guide.md)

### 3.1 提交格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 3.2 类型定义

| 类型 | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | 修复 bug |
| `docs` | 文档更新 |
| `style` | 代码格式调整 |
| `refactor` | 代码重构 |
| `test` | 测试相关 |
| `chore` | 构建/工具/配置 |

### 3.3 提交示例

```bash
feat(article): 添加文章点赞功能

- 实现文章点赞 API
- 添加前端点赞组件
- 更新文章详情页面

Closes #123
```

---

## 四、测试规范

> 测试目录统一使用 `__tests__/`，详细的测试标准请参阅 [代码验收标准](../02-specifications/code-acceptance-standards.md)

### 4.1 测试文件命名

```
# 单元测试
src/services/__tests__/auth.service.spec.ts
src/utils/__tests__/date.utils.spec.ts

# E2E 测试
tests/e2e/__tests__/home.spec.ts
tests/e2e/__tests__/login.spec.ts
```

### 4.2 测试结构

```typescript
// 单元测试示例 (Jest/Vitest)
describe('AuthService', () => {
  let service: AuthService;
  let repository: Repository<User>;

  beforeEach(async () => {
    // 初始化
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  describe('login', () => {
    it('should return token on successful login', async () => {
      // 测试逻辑
    });

    it('should throw error on invalid credentials', async () => {
      // 测试逻辑
    });
  });
});
```

```typescript
// E2E 测试示例 (Playwright)
import { test, expect } from '@playwright/test';

test.describe('Home Page', () => {
  test('should display article list', async ({ page }) => {
    await page.goto('/');
    await expect(page.locator('.article-list')).toBeVisible();
  });

  test('should navigate to article detail', async ({ page }) => {
    await page.goto('/');
    await page.click('.article-card:first-child');
    await expect(page).toHaveURL(/\/article\/\d+/);
  });
});
```

### 4.3 测试覆盖率要求

| 模块类型 | 覆盖率要求 |
|----------|-----------|
| 核心业务逻辑 | ≥ 90% |
| 工具函数 | ≥ 80% |
| 组件 | ≥ 70% |
| API 接口 | ≥ 80% |

---

## 五、代码审查清单

### 5.1 通用检查

- [ ] 代码符合命名规范
- [ ] 代码有适当的注释
- [ ] 没有重复代码 (DRY)
- [ ] 函数职责单一 (SRP)
- [ ] 错误处理完善

### 5.2 TypeScript 检查

- [ ] 类型定义完整
- [ ] 没有使用 `any`
- [ ] 泛型使用恰当
- [ ] 接口/类型定义清晰

### 5.3 Vue 检查

- [ ] 组件职责单一
- [ ] Props/Emits 定义清晰
- [ ] 响应式数据使用正确
- [ ] 生命周期钩子使用恰当

### 5.4 NestJS 检查

- [ ] Controller 职责单一
- [ ] Service 逻辑清晰
- [ ] DTO 验证完善
- [ ] 异常处理正确

### 5.5 测试检查

- [ ] 测试用例覆盖主要场景
- [ ] 测试命名清晰
- [ ] 测试独立可运行
- [ ] 测试数据隔离

---

## 六、性能优化建议

### 6.1 前端优化

```typescript
// 1. 组件懒加载
const ArticleDetail = () => import('@/pages/home/ArticleDetail.vue')

// 2. 列表虚拟化
import { useVirtualList } from '@vueuse/core'

// 3. 防抖节流
import { useDebounceFn, useThrottleFn } from '@vueuse/core'

// 4. 图片懒加载
<img v-lazy="article.coverImage" />
```

### 6.2 后端优化

```typescript
// 1. 查询优化 - 使用索引
@@index([status, createdAt])

// 2. 缓存使用
@UseInterceptors(CacheInterceptor)
@CacheTTL(300)

// 3. 分页查询
const [items, total] = await repository.findAndCount({
  skip: (page - 1) * size,
  take: size,
});
```

---

## 七、安全检查清单

- [ ] SQL 注入防护（使用 ORM）
- [ ] XSS 防护（Vue 自动转义）
- [ ] CSRF 防护（JWT + 同源策略）
- [ ] 敏感信息不硬编码
- [ ] 密码加密存储（bcrypt）
- [ ] API 限流配置
- [ ] 输入验证完善

---

## 八、文档更新清单

- [ ] 代码注释完整
- [ ] README 更新
- [ ] API 文档更新
- [ ] 变更记录更新

---

## 相关文档

- [Git 提交指南](../02-specifications/git-commit-guide.md)
- [代码验收标准](../02-specifications/code-acceptance-standards.md)
- [前端结构设计](./frontend-structure.md)
- [后端结构设计](./backend-structure.md)
- [API 设计](./api-design.md)
- [数据库设计](./database-schema.md)
