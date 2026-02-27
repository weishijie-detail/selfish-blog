# 状态管理设计文档

> 本文档定义了个人博客系统前端 Pinia Store 的设计规范和使用指南。

---

## 一、Store 划分

### 1.1 Store 列表

| Store | 说明 | 持久化 |
|-------|------|--------|
| `user` | 用户信息、认证状态 | 是（token） |
| `article` | 文章数据、文章列表缓存 | 否 |
| `category` | 分类数据、分类树 | 是 |
| `tag` | 标签数据、标签云 | 是 |
| `settings` | 系统配置 | 是 |
| `app` | 应用全局状态（侧边栏、主题等） | 是 |

### 1.2 Store 目录结构

```
src/stores/
├── index.ts              # Store 注册入口
├── user.ts               # 用户 Store
├── article.ts            # 文章 Store
├── category.ts           # 分类 Store
├── tag.ts                # 标签 Store
├── settings.ts           # 设置 Store
├── app.ts                # 应用 Store
└── types.ts              # Store 类型定义
```

---

## 二、Store 设计规范

### 2.1 User Store

```typescript
// src/stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { authApi } from '@/api/modules/auth'
import type { User, LoginParams, RegisterParams } from '@/types/user'
import { storage } from '@/utils/storage'
import { STORAGE_KEYS } from '@/constants/storage'

export const useUserStore = defineStore('user', () => {
  // ==================== State ====================
  const user = ref<User | null>(null)
  const token = ref<string | null>(storage.get(STORAGE_KEYS.TOKEN))

  // ==================== Getters ====================
  const isLoggedIn = computed(() => !!token.value)
  const isAdmin = computed(() => user.value?.role === 'ADMIN')
  const isEditor = computed(() => {
    const role = user.value?.role
    return role === 'ADMIN' || role === 'EDITOR'
  })
  const username = computed(() => user.value?.username || '')
  const avatar = computed(() => user.value?.avatar || '')

  // ==================== Actions ====================
  
  // 登录
  const login = async (params: LoginParams) => {
    const { data } = await authApi.login(params)
    token.value = data.access_token
    user.value = data.user
    storage.set(STORAGE_KEYS.TOKEN, data.access_token)
  }

  // 注册
  const register = async (params: RegisterParams) => {
    const { data } = await authApi.register(params)
    token.value = data.access_token
    user.value = data.user
    storage.set(STORAGE_KEYS.TOKEN, data.access_token)
  }

  // 登出
  const logout = () => {
    token.value = null
    user.value = null
    storage.remove(STORAGE_KEYS.TOKEN)
  }

  // 获取用户信息
  const fetchProfile = async () => {
    if (!token.value) return
    
    try {
      const { data } = await authApi.getProfile()
      user.value = data
    } catch (error) {
      // Token 失效，清除状态
      logout()
      throw error
    }
  }

  // 更新用户信息
  const updateProfile = async (data: Partial<User>) => {
    const { data: updated } = await authApi.updateProfile(data)
    user.value = updated
  }

  // 修改密码
  const changePassword = async (oldPassword: string, newPassword: string) => {
    await authApi.changePassword({ oldPassword, newPassword })
  }

  // 初始化（从存储恢复状态）
  const init = async () => {
    if (token.value) {
      await fetchProfile()
    }
  }

  return {
    // State
    user,
    token,
    // Getters
    isLoggedIn,
    isAdmin,
    isEditor,
    username,
    avatar,
    // Actions
    login,
    register,
    logout,
    fetchProfile,
    updateProfile,
    changePassword,
    init,
  }
})
```

### 2.2 Article Store

```typescript
// src/stores/article.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { articleApi } from '@/api/modules/article'
import type { Article, ArticleListParams, ArticleFilters } from '@/types/article'

export const useArticleStore = defineStore('article', () => {
  // ==================== State ====================
  const articles = ref<Article[]>([])
  const currentArticle = ref<Article | null>(null)
  const total = ref(0)
  const currentPage = ref(1)
  const pageSize = ref(10)
  const loading = ref(false)
  const filters = ref<ArticleFilters>({})

  // ==================== Getters ====================
  const featuredArticles = computed(() => 
    articles.value.filter(a => a.isFeatured)
  )
  
  const topArticles = computed(() => 
    articles.value.filter(a => a.isTop).slice(0, 5)
  )

  const hasMore = computed(() => {
    return currentPage.value * pageSize.value < total.value
  })

  // ==================== Actions ====================
  
  // 获取文章列表
  const fetchArticles = async (params: ArticleListParams = {}) => {
    loading.value = true
    try {
      const { data, pagination } = await articleApi.getList({
        page: params.page || 1,
        size: params.size || pageSize.value,
        ...filters.value,
        ...params,
      })
      articles.value = data
      total.value = pagination.total
      currentPage.value = pagination.page
    } finally {
      loading.value = false
    }
  }

  // 加载更多
  const loadMore = async () => {
    if (!hasMore.value || loading.value) return
    
    const nextPage = currentPage.value + 1
    loading.value = true
    try {
      const { data, pagination } = await articleApi.getList({
        page: nextPage,
        size: pageSize.value,
        ...filters.value,
      })
      articles.value = [...articles.value, ...data]
      total.value = pagination.total
      currentPage.value = pagination.page
    } finally {
      loading.value = false
    }
  }

  // 获取文章详情
  const fetchArticle = async (id: number) => {
    loading.value = true
    try {
      const data = await articleApi.getDetail(id)
      currentArticle.value = data
      return data
    } finally {
      loading.value = false
    }
  }

  // 创建文章
  const createArticle = async (data: Partial<Article>) => {
    const result = await articleApi.create(data)
    articles.value.unshift(result)
    total.value++
    return result
  }

  // 更新文章
  const updateArticle = async (id: number, data: Partial<Article>) => {
    const result = await articleApi.update(id, data)
    const index = articles.value.findIndex(a => a.id === id)
    if (index !== -1) {
      articles.value[index] = result
    }
    if (currentArticle.value?.id === id) {
      currentArticle.value = result
    }
    return result
  }

  // 删除文章
  const deleteArticle = async (id: number) => {
    await articleApi.delete(id)
    articles.value = articles.value.filter(a => a.id !== id)
    total.value--
  }

  // 点赞文章
  const likeArticle = async (id: number) => {
    await articleApi.like(id)
    const article = articles.value.find(a => a.id === id)
    if (article) {
      article.likeCount++
    }
    if (currentArticle.value?.id === id) {
      currentArticle.value.likeCount++
    }
  }

  // 设置筛选条件
  const setFilters = (newFilters: ArticleFilters) => {
    filters.value = { ...filters.value, ...newFilters }
    currentPage.value = 1
    articles.value = []
  }

  // 重置筛选
  const resetFilters = () => {
    filters.value = {}
    currentPage.value = 1
    articles.value = []
  }

  return {
    // State
    articles,
    currentArticle,
    total,
    currentPage,
    pageSize,
    loading,
    filters,
    // Getters
    featuredArticles,
    topArticles,
    hasMore,
    // Actions
    fetchArticles,
    loadMore,
    fetchArticle,
    createArticle,
    updateArticle,
    deleteArticle,
    likeArticle,
    setFilters,
    resetFilters,
  }
})
```

### 2.3 Category Store

```typescript
// src/stores/category.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { categoryApi } from '@/api/modules/category'
import type { Category, CategoryTree } from '@/types/category'
import { storage } from '@/utils/storage'
import { STORAGE_KEYS } from '@/constants/storage'

export const useCategoryStore = defineStore('category', () => {
  // ==================== State ====================
  const categories = ref<Category[]>([])
  const categoryTree = ref<CategoryTree[]>([])
  const loading = ref(false)
  const lastFetchTime = ref<number>(storage.get(STORAGE_KEYS.CATEGORY_TIME) || 0)

  // ==================== Getters ====================
  const categoryMap = computed(() => {
    const map = new Map<number, Category>()
    categories.value.forEach(c => map.set(c.id, c))
    return map
  })

  const hasCategories = computed(() => categories.value.length > 0)

  // ==================== Actions ====================
  
  // 获取分类列表（带缓存）
  const fetchCategories = async (force = false) => {
    const now = Date.now()
    const cacheExpired = now - lastFetchTime.value > 5 * 60 * 1000 // 5 分钟缓存
    
    if (!force && !cacheExpired && hasCategories.value) {
      return categories.value
    }

    loading.value = true
    try {
      const { data } = await categoryApi.getList()
      categories.value = data
      lastFetchTime.value = now
      storage.set(STORAGE_KEYS.CATEGORY_TIME, now)
      return data
    } finally {
      loading.value = false
    }
  }

  // 获取分类树
  const fetchCategoryTree = async () => {
    const { data } = await categoryApi.getTree()
    categoryTree.value = data
    return data
  }

  // 创建分类
  const createCategory = async (data: Partial<Category>) => {
    const result = await categoryApi.create(data)
    categories.value.push(result)
    await fetchCategoryTree()
    return result
  }

  // 更新分类
  const updateCategory = async (id: number, data: Partial<Category>) => {
    const result = await categoryApi.update(id, data)
    const index = categories.value.findIndex(c => c.id === id)
    if (index !== -1) {
      categories.value[index] = result
    }
    await fetchCategoryTree()
    return result
  }

  // 删除分类
  const deleteCategory = async (id: number) => {
    await categoryApi.delete(id)
    categories.value = categories.value.filter(c => c.id !== id)
    await fetchCategoryTree()
  }

  // 清除缓存
  const clearCache = () => {
    lastFetchTime.value = 0
    storage.remove(STORAGE_KEYS.CATEGORY_TIME)
  }

  return {
    // State
    categories,
    categoryTree,
    loading,
    lastFetchTime,
    // Getters
    categoryMap,
    hasCategories,
    // Actions
    fetchCategories,
    fetchCategoryTree,
    createCategory,
    updateCategory,
    deleteCategory,
    clearCache,
  }
})
```

### 2.4 Tag Store

```typescript
// src/stores/tag.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { tagApi } from '@/api/modules/tag'
import type { Tag, TagCloud } from '@/types/tag'
import { storage } from '@/utils/storage'
import { STORAGE_KEYS } from '@/constants/storage'

export const useTagStore = defineStore('tag', () => {
  // ==================== State ====================
  const tags = ref<Tag[]>([])
  const tagCloud = ref<TagCloud[]>([])
  const loading = ref(false)
  const lastFetchTime = ref<number>(storage.get(STORAGE_KEYS.TAG_TIME) || 0)

  // ==================== Getters ====================
  const popularTags = computed(() => 
    tagCloud.value.slice(0, 20)
  )

  const hasTags = computed(() => tags.value.length > 0)

  // ==================== Actions ====================
  
  // 获取标签列表
  const fetchTags = async (force = false) => {
    const now = Date.now()
    const cacheExpired = now - lastFetchTime.value > 5 * 60 * 1000

    if (!force && !cacheExpired && hasTags.value) {
      return tags.value
    }

    loading.value = true
    try {
      const { data } = await tagApi.getList()
      tags.value = data
      lastFetchTime.value = now
      storage.set(STORAGE_KEYS.TAG_TIME, now)
      return data
    } finally {
      loading.value = false
    }
  }

  // 获取标签云
  const fetchTagCloud = async () => {
    const { data } = await tagApi.getCloud()
    tagCloud.value = data
    return data
  }

  // 创建标签
  const createTag = async (data: Partial<Tag>) => {
    const result = await tagApi.create(data)
    tags.value.push(result)
    return result
  }

  // 更新标签
  const updateTag = async (id: number, data: Partial<Tag>) => {
    const result = await tagApi.update(id, data)
    const index = tags.value.findIndex(t => t.id === id)
    if (index !== -1) {
      tags.value[index] = result
    }
    return result
  }

  // 删除标签
  const deleteTag = async (id: number) => {
    await tagApi.delete(id)
    tags.value = tags.value.filter(t => t.id !== id)
  }

  return {
    // State
    tags,
    tagCloud,
    loading,
    lastFetchTime,
    // Getters
    popularTags,
    hasTags,
    // Actions
    fetchTags,
    fetchTagCloud,
    createTag,
    updateTag,
    deleteTag,
  }
})
```

### 2.5 Settings Store

```typescript
// src/stores/settings.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { settingsApi } from '@/api/modules/settings'
import type { Settings } from '@/types/settings'
import { storage } from '@/utils/storage'
import { STORAGE_KEYS } from '@/constants/storage'

export const useSettingsStore = defineStore('settings', () => {
  // ==================== State ====================
  const settings = ref<Settings>(storage.get(STORAGE_KEYS.SETTINGS) || {})
  const loading = ref(false)

  // ==================== Getters ====================
  const siteName = computed(() => settings.value.site?.name || '个人博客')
  const siteDescription = computed(() => settings.value.site?.description || '')
  const siteLogo = computed(() => settings.value.site?.logo || '')
  const siteFooter = computed(() => settings.value.site?.footer || '')
  const socialLinks = computed(() => settings.value.social || {})
  const seoConfig = computed(() => settings.value.seo || {})

  // ==================== Actions ====================
  
  // 获取系统设置
  const fetchSettings = async () => {
    loading.value = true
    try {
      const { data } = await settingsApi.getAll()
      settings.value = data
      storage.set(STORAGE_KEYS.SETTINGS, data)
    } finally {
      loading.value = false
    }
  }

  // 更新系统设置
  const updateSettings = async (key: string, value: any) => {
    await settingsApi.update(key, value)
    settings.value = {
      ...settings.value,
      [key]: value,
    }
    storage.set(STORAGE_KEYS.SETTINGS, settings.value)
  }

  // 批量更新
  const updateBatch = async (data: Partial<Settings>) => {
    await settingsApi.updateBatch(data)
    settings.value = {
      ...settings.value,
      ...data,
    }
    storage.set(STORAGE_KEYS.SETTINGS, settings.value)
  }

  // 清除缓存
  const clearCache = () => {
    settings.value = {}
    storage.remove(STORAGE_KEYS.SETTINGS)
  }

  return {
    // State
    settings,
    loading,
    // Getters
    siteName,
    siteDescription,
    siteLogo,
    siteFooter,
    socialLinks,
    seoConfig,
    // Actions
    fetchSettings,
    updateSettings,
    updateBatch,
    clearCache,
  }
})
```

### 2.6 App Store

```typescript
// src/stores/app.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
import { storage } from '@/utils/storage'
import { STORAGE_KEYS } from '@/constants/storage'

export const useAppStore = defineStore('app', () => {
  // ==================== State ====================
  const sidebarCollapsed = ref(storage.get(STORAGE_KEYS.SIDEBAR_COLLAPSED) || false)
  const theme = ref<'light' | 'dark'>(storage.get(STORAGE_KEYS.THEME) || 'light')
  const loading = ref(false)
  const breadcrumbs = ref<Array<{ title: string; path?: string }>>([])

  // ==================== Getters ====================
  const isDark = computed(() => theme.value === 'dark')

  // ==================== Actions ====================
  
  // 切换侧边栏
  const toggleSidebar = () => {
    sidebarCollapsed.value = !sidebarCollapsed.value
    storage.set(STORAGE_KEYS.SIDEBAR_COLLAPSED, sidebarCollapsed.value)
  }

  // 设置侧边栏状态
  const setSidebarCollapsed = (collapsed: boolean) => {
    sidebarCollapsed.value = collapsed
    storage.set(STORAGE_KEYS.SIDEBAR_COLLAPSED, collapsed)
  }

  // 切换主题
  const toggleTheme = () => {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
    storage.set(STORAGE_KEYS.THEME, theme.value)
    updateThemeClass()
  }

  // 设置主题
  const setTheme = (newTheme: 'light' | 'dark') => {
    theme.value = newTheme
    storage.set(STORAGE_KEYS.THEME, newTheme)
    updateThemeClass()
  }

  // 更新主题类名
  const updateThemeClass = () => {
    if (theme.value === 'dark') {
      document.documentElement.classList.add('dark')
    } else {
      document.documentElement.classList.remove('dark')
    }
  }

  // 设置面包屑
  const setBreadcrumbs = (items: Array<{ title: string; path?: string }>) => {
    breadcrumbs.value = items
  }

  // 添加面包屑
  const addBreadcrumb = (item: { title: string; path?: string }) => {
    breadcrumbs.value.push(item)
  }

  // 清除面包屑
  const clearBreadcrumbs = () => {
    breadcrumbs.value = []
  }

  // 设置全局加载
  const setLoading = (value: boolean) => {
    loading.value = value
  }

  // 初始化
  const init = () => {
    updateThemeClass()
  }

  return {
    // State
    sidebarCollapsed,
    theme,
    loading,
    breadcrumbs,
    // Getters
    isDark,
    // Actions
    toggleSidebar,
    setSidebarCollapsed,
    toggleTheme,
    setTheme,
    setBreadcrumbs,
    addBreadcrumb,
    clearBreadcrumbs,
    setLoading,
    init,
  }
})
```

---

## 三、Store 使用规范

### 3.1 在组件中使用

```vue
<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useUserStore } from '@/stores/user'
import { useArticleStore } from '@/stores/article'

// 获取 Store 实例
const userStore = useUserStore()
const articleStore = useArticleStore()

// 访问 State
console.log(userStore.isLoggedIn)

// 访问 Getters
const isAdmin = computed(() => userStore.isAdmin)

// 调用 Actions
const handleLogin = async () => {
  await userStore.login({ username: 'admin', password: '123456' })
}

// 监听状态变化
onMounted(async () => {
  await userStore.init()
  await articleStore.fetchArticles()
})
</script>
```

### 3.2 在路由守卫中使用

```typescript
// src/router/guards.ts
import { useUserStore } from '@/stores/user'

export function setupRouterGuards(router: Router) {
  router.beforeEach(async (to, from) => {
    const userStore = useUserStore()

    if (to.meta.requiresAuth && !userStore.isLoggedIn) {
      return { name: 'AdminLogin', query: { redirect: to.fullPath } }
    }

    return true
  })
}
```

### 3.3 组合式函数中使用

```typescript
// src/composables/useArticles.ts
import { computed } from 'vue'
import { useArticleStore } from '@/stores/article'

export function useArticles() {
  const articleStore = useArticleStore()

  const articles = computed(() => articleStore.articles)
  const loading = computed(() => articleStore.loading)
  const hasMore = computed(() => articleStore.hasMore)

  const loadMore = () => articleStore.loadMore()
  const fetchArticles = (params: any) => articleStore.fetchArticles(params)

  return {
    articles,
    loading,
    hasMore,
    loadMore,
    fetchArticles,
  }
}
```

---

## 四、持久化配置

### 4.1 存储键名常量

```typescript
// src/constants/storage.ts
export const STORAGE_KEYS = {
  // 认证
  TOKEN: 'blog_token',
  
  // 设置
  SETTINGS: 'blog_settings',
  
  // 主题
  THEME: 'blog_theme',
  SIDEBAR_COLLAPSED: 'blog_sidebar_collapsed',
  
  // 缓存时间戳
  CATEGORY_TIME: 'blog_category_time',
  TAG_TIME: 'blog_tag_time',
} as const
```

### 4.2 存储工具

```typescript
// src/utils/storage.ts
export const storage = {
  get<T>(key: string): T | null {
    try {
      const item = localStorage.getItem(key)
      return item ? JSON.parse(item) : null
    } catch {
      return null
    }
  },

  set<T>(key: string, value: T): void {
    try {
      localStorage.setItem(key, JSON.stringify(value))
    } catch (error) {
      console.error('Storage set error:', error)
    }
  },

  remove(key: string): void {
    try {
      localStorage.removeItem(key)
    } catch (error) {
      console.error('Storage remove error:', error)
    }
  },

  clear(): void {
    try {
      localStorage.clear()
    } catch (error) {
      console.error('Storage clear error:', error)
    }
  },
}
```

---

## 五、检查清单

- [ ] Store 划分合理
- [ ] State/Getters/Actions 定义清晰
- [ ] 持久化配置正确
- [ ] 类型定义完整
- [ ] 使用规范统一
