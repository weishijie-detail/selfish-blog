# 前台 - 首页实现与测试文档

## 一、功能描述

首页是博客的门户，展示最新文章列表、分页功能、精选文章等。

---

## 二、技术实现

### 2.1 技术栈
- **框架**: Vue 3 + TypeScript
- **构建工具**: Vite
- **UI 组件**: Element Plus / Naive UI
- **状态管理**: Pinia
- **路由**: Vue Router 4
- **HTTP 客户端**: Axios
- **样式**: Tailwind CSS

### 2.2 目录结构

```
frontend/src/views/home/
├── Home.vue              # 首页主组件
├── components/
│   ├── ArticleList.vue   # 文章列表组件
│   ├── ArticleCard.vue   # 文章卡片组件
│   ├── Pagination.vue    # 分页组件
│   ├── Sidebar.vue       # 侧边栏组件
│   ├── FeaturedPosts.vue # 精选文章
│   ├── LatestPosts.vue   # 最新文章
│   └── PopularPosts.vue  # 热门文章
└── composables/
    └── useArticles.ts    # 文章数据获取逻辑
```

### 2.3 API 接口

| 接口 | 方法 | 描述 |
|------|------|------|
| `/api/articles` | GET | 获取文章列表（支持分页、筛选） |
| `/api/articles/featured` | GET | 获取精选文章 |
| `/api/articles/latest` | GET | 获取最新文章 |
| `/api/articles/popular` | GET | 获取热门文章 |

### 2.4 核心代码实现

#### ArticleList.vue
```vue
<template>
  <div class="article-list">
    <ArticleCard 
      v-for="article in articles" 
      :key="article.id" 
      :article="article" 
    />
    <Pagination 
      :current-page="currentPage" 
      :total="total" 
      :page-size="pageSize"
      @change="handlePageChange"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useArticles } from '../composables/useArticles'

const { articles, total, currentPage, pageSize, fetchArticles } = useArticles()

const handlePageChange = (page: number) => {
  fetchArticles(page)
}

onMounted(() => {
  fetchArticles(1)
})
</script>
```

#### composables/useArticles.ts
```typescript
import { ref } from 'vue'
import { articleApi } from '@/api/article'
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
      const { data, total: count } = await articleApi.getList({
        page,
        pageSize: pageSize.value,
        ...params
      })
      articles.value = data
      total.value = count
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

## 三、E2E 测试用例

### 3.1 测试文件位置
```
e2e/tests/home.spec.ts
```

### 3.2 测试用例详情

#### 测试 1: 页面加载正常
```typescript
import { test, expect } from '@playwright/test'

test.describe('Home Page', () => {
  test('should load home page successfully', async ({ page }) => {
    // 访问首页
    await page.goto('/')
    
    // 验证页面标题存在
    await expect(page.locator('h1')).toContainText('博客')
    
    // 验证页面完全加载
    await expect(page).toHaveTitle(/.*博客.*/)
    
    // 验证文章列表区域存在
    await expect(page.locator('.article-list')).toBeVisible()
  })
})
```

#### 测试 2: 文章列表展示
```typescript
test('should display article list', async ({ page }) => {
  await page.goto('/')
  
  // 等待文章列表加载
  await page.waitForSelector('.article-card', { state: 'visible' })
  
  // 验证至少有一篇文章
  const articleCards = page.locator('.article-card')
  await expect(articleCards).toHaveCount({ min: 1 })
  
  // 验证文章卡片内容
  const firstCard = articleCards.first()
  await expect(firstCard.locator('.article-title')).toBeVisible()
  await expect(firstCard.locator('.article-excerpt')).toBeVisible()
  await expect(firstCard.locator('.article-date')).toBeVisible()
})
```

#### 测试 3: 分页功能
```typescript
test('should handle pagination', async ({ page }) => {
  await page.goto('/')
  
  // 等待文章列表加载
  await page.waitForSelector('.pagination', { state: 'visible' })
  
  // 验证分页控件存在
  const pagination = page.locator('.pagination')
  await expect(pagination).toBeVisible()
  
  // 点击下一页
  const nextPageBtn = pagination.locator('.next-page')
  await nextPageBtn.click()
  
  // 等待新数据加载
  await page.waitForResponse('/api/articles')
  
  // 验证页码更新
  const currentPage = pagination.locator('.current-page')
  await expect(currentPage).toHaveText('2')
})
```

#### 测试 4: 精选文章展示
```typescript
test('should display featured articles', async ({ page }) => {
  await page.goto('/')
  
  // 验证精选文章区域存在
  const featuredSection = page.locator('.featured-articles')
  await expect(featureedSection).toBeVisible()
  
  // 验证精选文章标记
  const featuredBadge = featuredSection.locator('.featured-badge').first()
  await expect(featuredBadge).toBeVisible()
})
```

#### 测试 5: 响应式布局
```typescript
test('should be responsive on different screen sizes', async ({ page }) => {
  // 移动端视图
  await page.setViewportSize({ width: 375, height: 667 })
  await page.goto('/')
  await expect(page.locator('.article-list')).toBeVisible()
  
  // 平板视图
  await page.setViewportSize({ width: 768, height: 1024 })
  await page.goto('/')
  await expect(page.locator('.sidebar')).toBeVisible()
  
  // 桌面视图
  await page.setViewportSize({ width: 1920, height: 1080 })
  await page.goto('/')
  await expect(page.locator('.main-content')).toBeVisible()
})
```

---

## 四、单元测试

### 4.1 测试文件位置
```
frontend/src/views/home/__tests__/Home.spec.ts
```

### 4.2 测试用例

```typescript
import { describe, it, expect, vi } from 'vitest'
import { mount } from '@vue/test-utils'
import Home from '../Home.vue'
import { useArticles } from '../composables/useArticles'

vi.mock('../composables/useArticles')

describe('Home.vue', () => {
  it('renders article list', () => {
    vi.mocked(useArticles).mockReturnValue({
      articles: [{ id: 1, title: 'Test Article' }],
      total: 1,
      currentPage: 1,
      pageSize: 10,
      loading: false,
      fetchArticles: vi.fn()
    })
    
    const wrapper = mount(Home)
    expect(wrapper.find('.article-list').exists()).toBe(true)
  })
})
```

---

## 五、验收标准

| 标准 | 描述 | 状态 |
|------|------|------|
| 页面加载 | 首屏加载时间 < 2 秒 | ☐ |
| 文章展示 | 正确显示文章列表 | ☐ |
| 分页功能 | 分页正常工作 | ☐ |
| 响应式 | 支持移动端/平板/桌面 | ☐ |
| 无控制台错误 | 浏览器控制台无错误 | ☐ |
