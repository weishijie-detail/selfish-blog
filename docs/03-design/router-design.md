# 路由设计文档

> 本文档定义了个人博客系统前端和后端的路由设计规范。

---

## 一、前端路由设计

### 1.1 路由结构

```
/                          # 首页（文章列表）
/article/:id               # 文章详情
/category/:slug            # 分类页
/tag/:slug                 # 标签页
/about                     # 关于页面

/admin                     # 后台首页（仪表盘）
/admin/login               # 后台登录
/admin/dashboard           # 仪表盘
/admin/articles            # 文章列表
/admin/articles/create     # 创建文章
/admin/articles/:id/edit   # 编辑文章
/admin/categories          # 分类管理
/admin/tags                # 标签管理
/admin/links               # 友链管理
/admin/settings            # 系统设置
/admin/users               # 用户管理
```

### 1.2 路由配置

```typescript
// src/router/routes.ts
import type { RouteRecordRaw } from 'vue-router'

export const routes: RouteRecordRaw[] = [
  // ==================== 前台路由 ====================
  {
    path: '/',
    component: () => import('@/layouts/HomeLayout.vue'),
    children: [
      {
        path: '',
        name: 'Home',
        component: () => import('@/pages/home/Home.vue'),
        meta: {
          title: '首页',
          keepAlive: true,
        },
      },
      {
        path: 'article/:id(\\d+)',
        name: 'ArticleDetail',
        component: () => import('@/pages/home/ArticleDetail.vue'),
        meta: {
          title: '文章详情',
          keepAlive: false,
        },
      },
      {
        path: 'category/:slug',
        name: 'Category',
        component: () => import('@/pages/home/CategoryPage.vue'),
        meta: {
          title: '分类',
          keepAlive: true,
        },
      },
      {
        path: 'tag/:slug',
        name: 'Tag',
        component: () => import('@/pages/home/TagPage.vue'),
        meta: {
          title: '标签',
          keepAlive: true,
        },
      },
      {
        path: 'about',
        name: 'About',
        component: () => import('@/pages/home/About.vue'),
        meta: {
          title: '关于',
          keepAlive: false,
        },
      },
    ],
  },

  // ==================== 后台路由 ====================
  {
    path: '/admin',
    component: () => import('@/layouts/AdminLayout.vue'),
    children: [
      {
        path: 'login',
        name: 'AdminLogin',
        component: () => import('@/pages/admin/Login.vue'),
        meta: {
          title: '登录',
          guest: true, // 已登录用户不能访问
        },
      },
      {
        path: '',
        name: 'AdminIndex',
        redirect: '/admin/dashboard',
      },
      {
        path: 'dashboard',
        name: 'Dashboard',
        component: () => import('@/pages/admin/Dashboard.vue'),
        meta: {
          title: '仪表盘',
          requiresAuth: true,
          icon: 'dashboard',
        },
      },
      {
        path: 'articles',
        name: 'ArticleList',
        component: () => import('@/pages/admin/ArticleList.vue'),
        meta: {
          title: '文章管理',
          requiresAuth: true,
          icon: 'article',
        },
      },
      {
        path: 'articles/create',
        name: 'ArticleCreate',
        component: () => import('@/pages/admin/ArticleEdit.vue'),
        meta: {
          title: '创建文章',
          requiresAuth: true,
          hidden: true, // 不在菜单中显示
        },
      },
      {
        path: 'articles/:id(\\d+)/edit',
        name: 'ArticleEdit',
        component: () => import('@/pages/admin/ArticleEdit.vue'),
        meta: {
          title: '编辑文章',
          requiresAuth: true,
          hidden: true,
        },
      },
      {
        path: 'categories',
        name: 'CategoryList',
        component: () => import('@/pages/admin/CategoryList.vue'),
        meta: {
          title: '分类管理',
          requiresAuth: true,
          icon: 'category',
        },
      },
      {
        path: 'tags',
        name: 'TagList',
        component: () => import('@/pages/admin/TagList.vue'),
        meta: {
          title: '标签管理',
          requiresAuth: true,
          icon: 'tag',
        },
      },
      {
        path: 'links',
        name: 'LinkList',
        component: () => import('@/pages/admin/LinkList.vue'),
        meta: {
          title: '友链管理',
          requiresAuth: true,
          icon: 'link',
        },
      },
      {
        path: 'settings',
        name: 'Settings',
        component: () => import('@/pages/admin/Settings.vue'),
        meta: {
          title: '系统设置',
          requiresAuth: true,
          icon: 'settings',
          roles: ['ADMIN'], // 仅管理员可访问
        },
      },
      {
        path: 'users',
        name: 'UserList',
        component: () => import('@/pages/admin/UserList.vue'),
        meta: {
          title: '用户管理',
          requiresAuth: true,
          icon: 'user',
          roles: ['ADMIN'],
        },
      },
    ],
  },

  // ==================== 异常页面 ====================
  {
    path: '/404',
    name: 'NotFound',
    component: () => import('@/pages/error/404.vue'),
  },
  {
    path: '/500',
    name: 'ServerError',
    component: () => import('@/pages/error/500.vue'),
  },
  {
    path: '/:pathMatch(.*)*',
    redirect: '/404',
  },
]
```

### 1.3 路由守卫

```typescript
// src/router/guards.ts
import type { Router, RouteLocationNormalized } from 'vue-router'
import { useUserStore } from '@/stores/user'
import { useMessage } from '@/composables/useMessage'

export function setupRouterGuards(router: Router) {
  router.beforeEach(async (to: RouteLocationNormalized, from: RouteLocationNormalized) => {
    const userStore = useUserStore()
    const { showMessage } = useMessage()

    // 设置页面标题
    if (to.meta.title) {
      document.title = `${to.meta.title} - 个人博客`
    }

    // 检查是否需要认证
    if (to.meta.requiresAuth && !userStore.isLoggedIn) {
      showMessage('error', '请先登录')
      return {
        name: 'AdminLogin',
        query: { redirect: to.fullPath },
      }
    }

    // 检查访客页面（已登录不能访问）
    if (to.meta.guest && userStore.isLoggedIn) {
      return { name: 'Dashboard' }
    }

    // 检查角色权限
    if (to.meta.roles && userStore.isLoggedIn) {
      const hasRole = to.meta.roles.some((role: string) => userStore.user?.role === role)
      if (!hasRole) {
        showMessage('error', '无权访问')
        return { name: 'Dashboard' }
      }
    }

    return true
  })

  router.afterEach((to: RouteLocationNormalized, from: RouteLocationNormalized) => {
    // 路由切换后的处理
    console.log(`从 ${from.path} 导航至 ${to.path}`)
  })
}
```

### 1.4 路由元信息类型

```typescript
// src/types/router.ts
import type { RouteMeta as VueRouteMeta } from 'vue-router'

export interface RouteMeta extends VueRouteMeta {
  title?: string           // 页面标题
  keepAlive?: boolean      // 是否缓存页面
  requiresAuth?: boolean   // 是否需要认证
  guest?: boolean          // 是否访客页面（已登录不可访问）
  roles?: string[]         // 允许访问的角色
  icon?: string            // 菜单图标
  hidden?: boolean         // 是否在菜单中隐藏
  order?: number           // 菜单排序
}

declare module 'vue-router' {
  interface RouteMeta extends RouteMeta {}
}
```

---

## 二、后端路由设计

### 2.1 API 路由前缀

```
/api/v1    # API 版本前缀
```

### 2.2 认证模块路由

```typescript
// POST   /api/v1/auth/login          # 用户登录
// POST   /api/v1/auth/register       # 用户注册
// POST   /api/v1/auth/logout         # 用户登出
// GET    /api/v1/auth/profile        # 获取当前用户信息
// PUT    /api/v1/auth/profile        # 更新当前用户信息
// PUT    /api/v1/auth/password       # 修改密码
```

### 2.3 文章模块路由

```typescript
// GET    /api/v1/articles            # 获取文章列表（公开）
// GET    /api/v1/articles/:id        # 获取文章详情（公开）
// POST   /api/v1/articles            # 创建文章（需认证）
// PUT    /api/v1/articles/:id        # 更新文章（需认证）
// DELETE /api/v1/articles/:id        # 删除文章（需认证）
// POST   /api/v1/articles/:id/like   # 点赞文章（公开）
// GET    /api/v1/articles/:id/related # 获取相关文章（公开）
```

### 2.4 分类模块路由

```typescript
// GET    /api/v1/categories          # 获取分类列表（公开）
// GET    /api/v1/categories/tree     # 获取分类树（公开）
// GET    /api/v1/categories/:id      # 获取分类详情（公开）
// POST   /api/v1/categories          # 创建分类（需认证）
// PUT    /api/v1/categories/:id      # 更新分类（需认证）
// DELETE /api/v1/categories/:id      # 删除分类（需认证）
```

### 2.5 标签模块路由

```typescript
// GET    /api/v1/tags                # 获取标签列表（公开）
// GET    /api/v1/tags/cloud          # 获取标签云（公开）
// GET    /api/v1/tags/:id            # 获取标签详情（公开）
// POST   /api/v1/tags                # 创建标签（需认证）
// PUT    /api/v1/tags/:id            # 更新标签（需认证）
// DELETE /api/v1/tags/:id            # 删除标签（需认证）
```

### 2.6 用户模块路由

```typescript
// GET    /api/v1/users               # 获取用户列表（需管理员）
// GET    /api/v1/users/:id           # 获取用户详情（需管理员）
// PUT    /api/v1/users/:id           # 更新用户（需管理员）
// DELETE /api/v1/users/:id           # 删除用户（需管理员）
// PUT    /api/v1/users/:id/role      # 更新用户角色（需管理员）
```

### 2.7 友链模块路由

```typescript
// GET    /api/v1/links               # 获取友链列表（公开）
// GET    /api/v1/links/:id           # 获取友链详情（公开）
// POST   /api/v1/links               # 创建友链（需认证）
// PUT    /api/v1/links/:id           # 更新友链（需认证）
// DELETE /api/v1/links/:id           # 删除友链（需认证）
// PUT    /api/v1/links/:id/sort      # 更新友链排序（需认证）
```

### 2.8 系统设置模块路由

```typescript
// GET    /api/v1/settings            # 获取所有设置（公开）
// GET    /api/v1/settings/:key       # 获取指定设置（公开）
// POST   /api/v1/settings            # 创建设置（需管理员）
// PUT    /api/v1/settings/:key       # 更新设置（需管理员）
// DELETE /api/v1/settings/:key       # 删除设置（需管理员）
```

### 2.9 仪表盘模块路由

```typescript
// GET    /api/v1/dashboard/stats     # 获取统计数据（需认证）
// GET    /api/v1/dashboard/views     # 获取访问统计（需认证）
// GET    /api/v1/dashboard/articles  # 获取文章统计（需认证）
```

### 2.10 日志模块路由

```typescript
// GET    /api/v1/logs                # 获取日志列表（需管理员）
// GET    /api/v1/logs/:id            # 获取日志详情（需管理员）
// DELETE /api/v1/logs                # 清空日志（需管理员）
```

---

## 三、路由权限控制

### 3.1 前端权限控制

```typescript
// src/router/permission.ts
import type { RouteRecordRaw } from 'vue-router'

// 路由权限映射
export const routePermissions: Record<string, string[]> = {
  '/admin/articles': ['ADMIN', 'EDITOR'],
  '/admin/categories': ['ADMIN', 'EDITOR'],
  '/admin/tags': ['ADMIN', 'EDITOR'],
  '/admin/links': ['ADMIN', 'EDITOR'],
  '/admin/settings': ['ADMIN'],
  '/admin/users': ['ADMIN'],
}

// 检查权限
export function hasPermission(route: string, role: string): boolean {
  const allowedRoles = routePermissions[route]
  if (!allowedRoles) return true
  return allowedRoles.includes(role)
}
```

### 3.2 后端权限控制

```typescript
// src/common/decorators/roles.decorator.ts
import { SetMetadata } from '@nestjs/common'

export const ROLES_KEY = 'roles'
export const Roles = (...roles: string[]) => SetMetadata(ROLES_KEY, roles)

// 使用示例
@Get('users')
@Roles('ADMIN')
findAll() {
  // 仅管理员可访问
}
```

---

## 四、路由命名规范

### 4.1 前端路由命名

| 类型 | 命名规则 | 示例 |
|------|----------|------|
| 首页 | `Home` | `Home` |
| 详情页 | `模块名 + Detail` | `ArticleDetail` |
| 列表页 | `模块名 + List` | `ArticleList` |
| 创建页 | `模块名 + Create` | `ArticleCreate` |
| 编辑页 | `模块名 + Edit` | `ArticleEdit` |
| 设置页 | `模块名 + Settings` | `Settings` |

### 4.2 后端路由命名

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/modules` | 获取列表 |
| GET | `/modules/:id` | 获取详情 |
| POST | `/modules` | 创建资源 |
| PUT | `/modules/:id` | 更新资源 |
| DELETE | `/modules/:id` | 删除资源 |

---

## 五、检查清单

- [ ] 前端路由配置完整
- [ ] 后端 API 路由定义完整
- [ ] 路由守卫实现正确
- [ ] 权限控制逻辑正确
- [ ] 路由命名规范统一
- [ ] 异常路由处理完善
