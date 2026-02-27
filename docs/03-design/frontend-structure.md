# 前端项目结构设计文档

> 本文档定义了个人博客系统前端项目的目录结构、模块划分和设计规范。

---

## 一、项目目录结构

```
frontend/
├── public/                      # 公共静态资源
│   ├── favicon.ico              # 网站图标
│   └── robots.txt               # 搜索引擎爬虫配置
│
├── src/                         # 源代码目录
│   ├── main.ts                  # 应用入口文件
│   ├── App.vue                  # 根组件
│   │
│   ├── assets/                  # 静态资源
│   │   ├── images/              # 图片资源
│   │   ├── icons/               # SVG 图标
│   │   └── fonts/               # 字体文件
│   │
│   ├── components/              # 组件目录
│   │   ├── common/              # 通用组件
│   │   │   ├── AppHeader.vue    # 页头组件
│   │   │   ├── AppFooter.vue    # 页脚组件
│   │   │   ├── AppSidebar.vue   # 侧边栏组件
│   │   │   ├── Loading.vue      # 加载组件
│   │   │   ├── Empty.vue        # 空状态组件
│   │   │   ├── Error.vue        # 错误组件
│   │   │   ├── Pagination.vue   # 分页组件
│   │   │   └── ImageUpload.vue  # 图片上传组件
│   │   │
│   │   └── business/            # 业务组件
│   │       ├── ArticleCard.vue      # 文章卡片
│   │       ├── ArticleList.vue      # 文章列表
│   │       ├── ArticleMeta.vue      # 文章元信息
│   │       ├── CategoryTree.vue     # 分类树
│   │       ├── TagCloud.vue         # 标签云
│   │       ├── MarkdownEditor.vue   # Markdown 编辑器
│   │       ├── MarkdownViewer.vue   # Markdown 渲染器
│   │       └── CodeBlock.vue        # 代码块组件
│   │
│   ├── pages/                   # 页面组件
│   │   ├── home/                # 前台页面
│   │   │   ├── Home.vue         # 首页
│   │   │   ├── ArticleDetail.vue # 文章详情
│   │   │   ├── CategoryPage.vue  # 分类页
│   │   │   ├── TagPage.vue       # 标签页
│   │   │   └── About.vue         # 关于页面
│   │   │
│   │   └── admin/               # 后台页面
│   │       ├── Dashboard.vue    # 仪表盘
│   │       ├── Login.vue        # 登录页
│   │       ├── ArticleList.vue  # 文章列表
│   │       ├── ArticleEdit.vue  # 文章编辑
│   │       ├── CategoryList.vue # 分类列表
│   │       ├── TagList.vue      # 标签列表
│   │       ├── LinkList.vue     # 友链列表
│   │       └── Settings.vue     # 系统设置
│   │
│   ├── layouts/                 # 布局组件
│   │   ├── HomeLayout.vue       # 前台布局
│   │   ├── AdminLayout.vue      # 后台布局
│   │   └── BlankLayout.vue      # 空白布局
│   │
│   ├── router/                  # 路由配置
│   │   ├── index.ts             # 路由入口
│   │   ├── routes.ts            # 路由定义
│   │   └── guards.ts            # 路由守卫
│   │
│   ├── stores/                  # Pinia 状态管理
│   │   ├── index.ts             # Store 入口
│   │   ├── user.ts              # 用户状态
│   │   ├── article.ts           # 文章状态
│   │   ├── category.ts          # 分类状态
│   │   ├── tag.ts               # 标签状态
│   │   └── settings.ts          # 系统配置状态
│   │
│   ├── api/                     # API 接口
│   │   ├── index.ts             # API 入口
│   │   ├── request.ts           # 请求封装
│   │   ├── interceptors.ts      # 请求/响应拦截器
│   │   └── modules/             # 模块化 API
│   │       ├── auth.ts          # 认证 API
│   │       ├── article.ts       # 文章 API
│   │       ├── category.ts      # 分类 API
│   │       ├── tag.ts           # 标签 API
│   │       ├── user.ts          # 用户 API
│   │       ├── link.ts          # 友链 API
│   │       └── settings.ts      # 系统配置 API
│   │
│   ├── types/                   # TypeScript 类型定义
│   │   ├── index.ts             # 类型入口
│   │   ├── common.ts            # 通用类型
│   │   ├── user.ts              # 用户类型
│   │   ├── article.ts           # 文章类型
│   │   ├── category.ts          # 分类类型
│   │   ├── tag.ts               # 标签类型
│   │   └── api.ts               # API 类型
│   │
│   ├── utils/                   # 工具函数
│   │   ├── index.ts             # 工具入口
│   │   ├── request.ts           # 请求工具
│   │   ├── storage.ts           # 存储工具
│   │   ├── date.ts              # 日期工具
│   │   ├── format.ts            # 格式化工具
│   │   ├── validate.ts          # 验证工具
│   │   └── dom.ts               # DOM 工具
│   │
│   ├── composables/             # 组合式函数
│   │   ├── useRequest.ts        # 请求 Hook
│   │   ├── usePagination.ts     # 分页 Hook
│   │   ├── useLoading.ts        # 加载 Hook
│   │   ├── useMessage.ts        # 消息 Hook
│   │   └── useAuth.ts           # 认证 Hook
│   │
│   ├── constants/               # 常量定义
│   │   ├── index.ts             # 常量入口
│   │   ├── api.ts               # API 常量
│   │   ├── routes.ts            # 路由常量
│   │   ├── storage.ts           # 存储常量
│   │   └── regex.ts             # 正则常量
│   │
│   ├── directives/              # 自定义指令
│   │   ├── index.ts             # 指令入口
│   │   ├── permission.ts        # 权限指令
│   │   └── loading.ts           # 加载指令
│   │
│   └── styles/                  # 全局样式
│       ├── index.css            # 样式入口
│       ├── variables.css        # CSS 变量
│       ├── reset.css            # 样式重置
│       └── utils.css            # 工具类
│
├── tests/                       # 测试文件
│   ├── e2e/                     # E2E 测试
│   │   ├── home.spec.ts         # 首页测试
│   │   ├── article.spec.ts      # 文章测试
│   │   └── admin.spec.ts        # 后台测试
│   └── unit/                    # 单元测试
│       ├── components/          # 组件测试
│       └── utils/               # 工具测试
│
├── .env                         # 环境变量
├── .env.development             # 开发环境变量
├── .env.production              # 生产环境变量
├── .eslintrc.cjs                # ESLint 配置
├── .prettierrc                  # Prettier 配置
├── index.html                   # HTML 模板
├── package.json                 # 依赖配置
├── tsconfig.json                # TypeScript 配置
└── vite.config.ts               # Vite 配置
```

---

## 二、模块划分

### 2.1 核心模块

| 模块 | 说明 | 目录 |
|------|------|------|
| 路由模块 | Vue Router 配置、路由守卫 | `src/router/` |
| 状态管理 | Pinia Store 配置 | `src/stores/` |
| API 模块 | Axios 封装、API 接口 | `src/api/` |
| 组件库 | 通用组件、业务组件 | `src/components/` |

### 2.2 功能模块

| 模块 | 说明 | 页面 | 组件 |
|------|------|------|------|
| 认证模块 | 登录、登出、权限 | `/login` | - |
| 文章模块 | 文章列表、详情、管理 | `/`, `/article/:id`, `/admin/articles` | `ArticleCard`, `ArticleList` |
| 分类模块 | 分类展示、管理 | `/category/:slug`, `/admin/categories` | `CategoryTree` |
| 标签模块 | 标签展示、管理 | `/tag/:slug`, `/admin/tags` | `TagCloud` |
| 用户模块 | 用户管理 | `/admin/users` | - |
| 设置模块 | 系统配置 | `/admin/settings` | - |

---

## 三、组件设计规范

### 3.1 组件命名

```typescript
// PascalCase 命名
ArticleList.vue
ArticleCard.vue
MarkdownEditor.vue

// 组件内部 name 属性
export default {
  name: 'ArticleList'
}
```

### 3.2 Props 定义

```typescript
// 使用 interface 定义 Props
interface Props {
  articles: Article[]
  loading?: boolean
  pageSize?: number
}

// 使用 withDefaults 设置默认值
const props = withDefaults(defineProps<Props>(), {
  loading: false,
  pageSize: 10
})
```

### 3.3 Emits 定义

```typescript
// 使用 interface 定义 Emits
interface Emits {
  (e: 'update:articles', articles: Article[]): void
  (e: 'load-more'): void
  (e: 'delete', id: number): void
}

const emit = defineEmits<Emits>()
```

### 3.4 组件结构

```vue
<script setup lang="ts">
// 1. 导入（按顺序）
// 第三方库
import { ref, computed } from 'vue'

// 项目模块
import ArticleCard from './ArticleCard.vue'

// 类型
import type { Article } from '@/types/article'

// 2. Props/Emits
interface Props { ... }
const props = defineProps<Props>()

interface Emits { ... }
const emit = defineEmits<Emits>()

// 3. 响应式数据
const loading = ref(false)

// 4. 计算属性
const filteredArticles = computed(() => ...)

// 5. 方法
const handleLoadMore = () => { ... }

// 6. 生命周期
onMounted(() => { ... })
</script>

<template>
  <!-- 模板内容 -->
</template>

<style scoped>
/* 样式 */
</style>
```

---

## 四、API 调用规范

### 4.1 请求封装

```typescript
// src/api/request.ts
import axios from 'axios'
import type { AxiosInstance, AxiosRequestConfig } from 'axios'

const request = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000
})

// 请求拦截器
request.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

// 响应拦截器
request.interceptors.response.use(
  (response) => response.data,
  (error) => {
    // 统一错误处理
    return Promise.reject(error)
  }
)

export default request
```

### 4.2 API 模块

```typescript
// src/api/modules/article.ts
import request from '../request'
import type { Article, ArticleListParams, PaginatedResponse } from '@/types/article'

export const articleApi = {
  // 获取文章列表
  getList(params: ArticleListParams): Promise<PaginatedResponse<Article>> {
    return request.get('/articles', { params })
  },

  // 获取文章详情
  getDetail(id: number): Promise<Article> {
    return request.get(`/articles/${id}`)
  },

  // 创建文章
  create(data: Partial<Article>): Promise<Article> {
    return request.post('/articles', data)
  },

  // 更新文章
  update(id: number, data: Partial<Article>): Promise<Article> {
    return request.put(`/articles/${id}`, data)
  },

  // 删除文章
  delete(id: number): Promise<void> {
    return request.delete(`/articles/${id}`)
  },

  // 点赞文章
  like(id: number): Promise<void> {
    return request.post(`/articles/${id}/like`)
  }
}
```

### 4.3 组合式函数

```typescript
// src/composables/useArticles.ts
import { ref } from 'vue'
import { articleApi } from '@/api/modules/article'
import type { Article, ArticleListParams } from '@/types/article'

export function useArticles() {
  const articles = ref<Article[]>([])
  const total = ref(0)
  const currentPage = ref(1)
  const pageSize = ref(10)
  const loading = ref(false)

  const fetchArticles = async (page: number = 1, params?: ArticleListParams) => {
    loading.value = true
    try {
      const { data, pagination } = await articleApi.getList({
        page,
        pageSize: pageSize.value,
        ...params
      })
      articles.value = data
      total.value = pagination.total
      currentPage.value = page
    } finally {
      loading.value = false
    }
  }

  return {
    articles,
    total,
    currentPage,
    pageSize,
    loading,
    fetchArticles
  }
}
```

---

## 五、状态管理规范

### 5.1 Store 定义

```typescript
// src/stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import type { User } from '@/types/user'

export const useUserStore = defineStore('user', () => {
  // 状态
  const user = ref<User | null>(null)
  const token = ref<string | null>(null)

  // 计算属性
  const isLoggedIn = computed(() => !!token.value)
  const isAdmin = computed(() => user.value?.role === 'ADMIN')

  // 方法
  const login = async (username: string, password: string) => {
    const { authApi } = await import('@/api/modules/auth')
    const { data } = await authApi.login({ username, password })
    token.value = data.access_token
    user.value = data.user
    localStorage.setItem('token', data.access_token)
  }

  const logout = () => {
    token.value = null
    user.value = null
    localStorage.removeItem('token')
  }

  const fetchProfile = async () => {
    const { authApi } = await import('@/api/modules/auth')
    const { data } = await authApi.getProfile()
    user.value = data
  }

  return {
    user,
    token,
    isLoggedIn,
    isAdmin,
    login,
    logout,
    fetchProfile
  }
})
```

### 5.2 Store 使用

```typescript
// 在组件中使用
import { useUserStore } from '@/stores/user'

const userStore = useUserStore()

// 访问状态
console.log(userStore.isLoggedIn)

// 调用方法
await userStore.login('admin', 'password')
```

---

## 六、路由设计规范

### 6.1 路由定义

```typescript
// src/router/routes.ts
import type { RouteRecordRaw } from 'vue-router'

export const routes: RouteRecordRaw[] = [
  // 前台路由
  {
    path: '/',
    component: () => import('@/layouts/HomeLayout.vue'),
    children: [
      {
        path: '',
        name: 'Home',
        component: () => import('@/pages/home/Home.vue')
      },
      {
        path: 'article/:id',
        name: 'ArticleDetail',
        component: () => import('@/pages/home/ArticleDetail.vue')
      },
      {
        path: 'category/:slug',
        name: 'Category',
        component: () => import('@/pages/home/CategoryPage.vue')
      },
      {
        path: 'tag/:slug',
        name: 'Tag',
        component: () => import('@/pages/home/TagPage.vue')
      },
      {
        path: 'about',
        name: 'About',
        component: () => import('@/pages/home/About.vue')
      }
    ]
  },

  // 后台路由
  {
    path: '/admin',
    component: () => import('@/layouts/AdminLayout.vue'),
    children: [
      {
        path: 'login',
        name: 'AdminLogin',
        component: () => import('@/pages/admin/Login.vue')
      },
      {
        path: 'dashboard',
        name: 'Dashboard',
        component: () => import('@/pages/admin/Dashboard.vue'),
        meta: { requiresAuth: true }
      },
      {
        path: 'articles',
        name: 'ArticleList',
        component: () => import('@/pages/admin/ArticleList.vue'),
        meta: { requiresAuth: true }
      },
      {
        path: 'articles/:id/edit',
        name: 'ArticleEdit',
        component: () => import('@/pages/admin/ArticleEdit.vue'),
        meta: { requiresAuth: true }
      }
    ]
  }
]
```

### 6.2 路由守卫

```typescript
// src/router/guards.ts
import type { Router } from 'vue-router'
import { useUserStore } from '@/stores/user'

export function setupRouterGuards(router: Router) {
  router.beforeEach(async (to, from, next) => {
    const userStore = useUserStore()

    // 需要认证的路由
    if (to.meta.requiresAuth && !userStore.isLoggedIn) {
      next({ name: 'AdminLogin', query: { redirect: to.fullPath } })
      return
    }

    // 已登录访问登录页，重定向到仪表盘
    if (to.name === 'AdminLogin' && userStore.isLoggedIn) {
      next({ name: 'Dashboard' })
      return
    }

    next()
  })
}
```

---

## 七、类型定义规范

### 7.1 通用类型

```typescript
// src/types/common.ts
// 分页参数
export interface PaginationParams {
  page: number
  size: number
}

// 分页响应
export interface PaginationMeta {
  page: number
  size: number
  total: number
  totalPages: number
}

// 分页数据
export interface PaginatedResponse<T> {
  items: T[]
  pagination: PaginationMeta
}

// API 响应
export interface ApiResponse<T> {
  success: boolean
  data: T
  message?: string
}
```

### 7.2 文章类型

```typescript
// src/types/article.ts
import type { User } from './user'
import type { Category } from './category'
import type { Tag } from './tag'

export enum ArticleStatus {
  DRAFT = 'DRAFT',
  PUBLISHED = 'PUBLISHED',
  ARCHIVED = 'ARCHIVED'
}

export interface Article {
  id: number
  title: string
  content: string
  summary?: string
  coverImage?: string
  status: ArticleStatus
  viewCount: number
  likeCount: number
  isFeatured: boolean
  isTop: boolean
  publishedAt?: string
  author: User
  categories: Category[]
  tags: Tag[]
  createdAt: string
  updatedAt: string
}

export interface ArticleListParams {
  page: number
  size: number
  status?: ArticleStatus
  categoryId?: number
  tagId?: number
  keyword?: string
  sort?: string
}
```

---

## 八、检查清单

- [ ] 目录结构清晰合理
- [ ] 组件命名规范统一
- [ ] API 调用封装完善
- [ ] 状态管理规范
- [ ] 路由配置完整
- [ ] 类型定义完整
- [ ] 测试文件就位
