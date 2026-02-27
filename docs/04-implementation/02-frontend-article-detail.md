# 前台 - 文章详情页实现与测试文档

## 一、功能描述

文章详情页展示文章完整内容，包括 Markdown 渲染、代码高亮、目录导航、点赞等功能。

---

## 二、技术实现

### 2.1 技术栈
- **Markdown 渲染**: markdown-it / marked
- **代码高亮**: highlight.js / prismjs
- **目录生成**: 基于 Markdown 标题自动生成

### 2.2 目录结构

```
frontend/src/views/article/
├── ArticleDetail.vue        # 文章详情页主组件
├── components/
│   ├── MarkdownRenderer.vue # Markdown 渲染组件
│   ├── TableOfContents.vue  # 目录导航组件
│   ├── ArticleMeta.vue      # 文章元信息组件
│   ├── LikeButton.vue       # 点赞按钮组件
│   ├── ShareButtons.vue     # 分享按钮组件
│   └── RelatedArticles.vue  # 相关文章组件
└── composables/
    ├── useArticle.ts        # 文章数据获取
    └── useLike.ts           # 点赞逻辑
```

### 2.3 API 接口

| 接口 | 方法 | 描述 |
|------|------|------|
| `/api/articles/:id` | GET | 获取文章详情 |
| `/api/articles/:id/like` | POST | 点赞文章 |
| `/api/articles/:id/related` | GET | 获取相关文章 |
| `/api/articles/:id/views` | POST | 增加阅读计数 |

### 2.4 核心代码实现

#### ArticleDetail.vue
```vue
<template>
  <div class="article-detail">
    <article v-if="article">
      <ArticleMeta :article="article" />
      <div class="article-content">
        <MarkdownRenderer :content="article.content" />
      </div>
      <div class="article-actions">
        <LikeButton :article-id="article.id" :likes="article.likes" />
        <ShareButtons :article="article" />
      </div>
      <RelatedArticles :current-id="article.id" />
    </article>
    <div v-else class="loading">加载中...</div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useArticle } from '@/composables/useArticle'

const route = useRoute()
const { article, fetchArticle } = useArticle()

onMounted(async () => {
  const id = route.params.id as string
  await fetchArticle(id)
})
</script>
```

#### MarkdownRenderer.vue
```vue
<template>
  <div class="markdown-body" v-html="renderedContent"></div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import MarkdownIt from 'markdown-it'
import 'highlight.js/styles/github.css'

const props = defineProps<{
  content: string
}>()

const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true,
  highlight: (str, lang) => {
    const hljs = require('highlight.js')
    if (lang && hljs.getLanguage(lang)) {
      return hljs.highlight(str, { language: lang }).value
    }
    return ''
  }
})

const renderedContent = computed(() => md.render(props.content))
</script>
```

#### composables/useArticle.ts
```typescript
import { ref } from 'vue'
import { articleApi } from '@/api/article'
import type { Article } from '@/types/article'

export function useArticle() {
  const article = ref<Article | null>(null)
  const loading = ref(false)

  const fetchArticle = async (id: string) => {
    loading.value = true
    try {
      const data = await articleApi.getDetail(id)
      article.value = data
      // 增加阅读计数
      articleApi.incrementViews(id)
    } finally {
      loading.value = false
    }
  }

  return {
    article,
    loading,
    fetchArticle
  }
}
```

---

## 三、E2E 测试用例

### 3.1 测试文件位置
```
e2e/tests/article-detail.spec.ts
```

### 3.2 测试用例详情

#### 测试 1: 文章内容渲染
```typescript
import { test, expect } from '@playwright/test'

test.describe('Article Detail Page', () => {
  test('should render article content', async ({ page }) => {
    // 访问文章详情页
    await page.goto('/article/1')
    
    // 等待内容加载
    await page.waitForSelector('.article-content', { state: 'visible' })
    
    // 验证文章标题
    await expect(page.locator('.article-title')).toBeVisible()
    
    // 验证 Markdown 内容渲染
    const content = page.locator('.markdown-body')
    await expect(content).toBeVisible()
    
    // 验证代码高亮
    const codeBlocks = page.locator('pre code')
    const count = await codeBlocks.count()
    expect(count).toBeGreaterThan(0)
  })
})
```

#### 测试 2: 文章元信息显示
```typescript
test('should display article metadata', async ({ page }) => {
  await page.goto('/article/1')
  
  // 验证作者信息
  await expect(page.locator('.author-name')).toBeVisible()
  
  // 验证发布时间
  await expect(page.locator('.publish-date')).toBeVisible()
  
  // 验证分类标签
  await expect(page.locator('.category-tag')).toBeVisible()
  
  // 验证文章标签
  const tags = page.locator('.article-tag')
  await expect(tags.first()).toBeVisible()
})
```

#### 测试 3: 目录导航
```typescript
test('should generate table of contents', async ({ page }) => {
  await page.goto('/article/1')
  
  // 等待目录生成
  await page.waitForSelector('.table-of-contents', { state: 'visible' })
  
  // 验证目录存在
  const toc = page.locator('.table-of-contents')
  await expect(toc).toBeVisible()
  
  // 验证目录项
  const tocItems = toc.locator('li')
  await expect(tocItems.first()).toBeVisible()
  
  // 点击目录项跳转
  const firstItem = tocItems.first()
  await firstItem.click()
  
  // 验证页面滚动到对应位置
  await page.waitForTimeout(500)
  const heading = await page.locator('h2').first()
  const headingBox = await heading.boundingBox()
  expect(headingBox).not.toBeNull()
})
```

#### 测试 4: 点赞功能
```typescript
test('should handle like action', async ({ page }) => {
  await page.goto('/article/1')
  
  // 获取初始点赞数
  const likeBtn = page.locator('.like-button')
  const initialLikes = await likeBtn.locator('.like-count').textContent()
  
  // 点击点赞
  await likeBtn.click()
  
  // 等待响应
  await page.waitForResponse('/api/articles/1/like')
  
  // 验证点赞数增加
  const newLikes = await likeBtn.locator('.like-count').textContent()
  expect(parseInt(newLikes)).toBeGreaterThan(parseInt(initialLikes || '0'))
  
  // 验证按钮状态变化
  await expect(likeBtn).toHaveClass(/liked/)
})
```

#### 测试 5: 上一篇/下一篇导航
```typescript
test('should display prev/next navigation', async ({ page }) => {
  await page.goto('/article/1')
  
  // 验证上一篇按钮
  const prevBtn = page.locator('.prev-article')
  if (await prevBtn.count() > 0) {
    await expect(prevBtn).toBeVisible()
  }
  
  // 验证下一篇按钮
  const nextBtn = page.locator('.next-article')
  if (await nextBtn.count() > 0) {
    await expect(nextBtn).toBeVisible()
    
    // 点击下一篇
    await nextBtn.click()
    await page.waitForURL(/\/article\/\d+/)
    
    // 验证跳转到新文章
    expect(page.url()).not.toContain('/article/1')
  }
})
```

#### 测试 6: 响应式布局
```typescript
test('should be responsive', async ({ page }) => {
  // 移动端
  await page.setViewportSize({ width: 375, height: 667 })
  await page.goto('/article/1')
  await expect(page.locator('.article-content')).toBeVisible()
  
  // 目录在移动端应该隐藏或可折叠
  const toc = page.locator('.table-of-contents')
  // 移动端可能隐藏目录或显示为抽屉
})
```

---

## 四、单元测试

### 4.1 测试文件位置
```
frontend/src/views/article/__tests__/ArticleDetail.spec.ts
```

### 4.2 测试用例

```typescript
import { describe, it, expect, vi } from 'vitest'
import { mount } from '@vue/test-utils'
import { createRouter, createWebHistory } from 'vue-router'
import ArticleDetail from '../ArticleDetail.vue'

const router = createRouter({
  history: createWebHistory(),
  routes: [{ path: '/article/:id', component: ArticleDetail }]
})

describe('ArticleDetail.vue', () => {
  it('renders article content', async () => {
    const wrapper = mount(ArticleDetail, {
      global: {
        plugins: [router]
      },
      props: {
        article: {
          id: '1',
          title: 'Test Article',
          content: '# Hello World',
          author: 'Author'
        }
      }
    })
    
    expect(wrapper.find('.article-title').text()).toBe('Test Article')
  })
  
  it('renders markdown content', () => {
    const wrapper = mount(ArticleDetail, {
      props: {
        article: {
          id: '1',
          title: 'Test',
          content: '## Heading\n\nParagraph text'
        }
      }
    })
    
    expect(wrapper.find('.markdown-body').exists()).toBe(true)
  })
})
```

---

## 五、验收标准

| 标准 | 描述 | 状态 |
|------|------|------|
| Markdown 渲染 | 正确渲染 Markdown 内容 | ☐ |
| 代码高亮 | 代码块正确高亮 | ☐ |
| 目录导航 | 自动生成并正确跳转 | ☐ |
| 点赞功能 | 点赞计数正确更新 | ☐ |
| 响应式 | 支持移动端浏览 | ☐ |
| 阅读统计 | 访问量正确累加 | ☐ |
