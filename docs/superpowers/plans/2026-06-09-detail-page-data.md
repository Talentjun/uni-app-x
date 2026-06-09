---
change: detail-page-data
design-doc: docs/superpowers/specs/2026-06-09-detail-page-data-design.md
base-ref: 9ab847c15d499333681851615f596cf0442159fe
archived-with: 2026-06-09-detail-page-data
---

# 详情页数据获取与渲染 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在详情页根据接口返回详情数据，并渲染详情数据

**Architecture:** 将详情页从 tabBar 移除，使用 navigateTo 跳转并传递文章 ID，复用 getHomeData() 列表接口获取数据并根据 ID 过滤出对应文章详情

**Tech Stack:** uni-app x, UTS, Vue 3 Composition API

## archived-with: 2026-06-09-detail-page-data

## File Structure

- `pages.json` - 移除详情页的 tabBar 配置
- `pages/index/index.uvue` - 添加文章点击跳转逻辑
- `pages/detail/detail.uvue` - 添加数据获取和渲染逻辑

## archived-with: 2026-06-09-detail-page-data

### Task 1: 修改 pages.json 配置

**Files:**

- Modify: `pages.json`

- [ ] **Step 1: 移除详情页的 tabBar 配置**

从 `tabBar.list` 数组中移除详情页的配置项：

```json
{
  "tabBar": {
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页",
        "iconPath": "static/tabbar/home.png",
        "selectedIconPath": "static/tabbar/home.png"
      },
      {
        "pagePath": "pages/mine/mine",
        "text": "我的",
        "iconPath": "static/tabbar/mine.png",
        "selectedIconPath": "static/tabbar/mine.png"
      }
    ]
  }
}
```

- [ ] **Step 2: 提交代码**

```bash
git add pages.json
git commit -m "feat: 移除详情页的 tabBar 配置"
```

## archived-with: 2026-06-09-detail-page-data

### Task 2: 修改首页添加跳转逻辑

**Files:**

- Modify: `pages/index/index.uvue`

- [ ] **Step 1: 添加文章点击跳转函数**

在 `<script setup>` 中添加跳转函数：

```typescript
// 跳转到详情页
const goToDetail = (id: number) => {
  uni.navigateTo({
    url: `/pages/detail/detail?id=${id}`,
  })
}
```

- [ ] **Step 2: 为文章列表项添加点击事件**

修改模板中的文章列表项，添加 `@click` 事件：

```html
<view
  v-else
  class="post-item"
  v-for="(item, index) in posts"
  :key="index"
  @click="goToDetail(item.id)"
>
  <text class="post-title">{{ item.title }}</text>
  <text class="post-body">{{ item.body }}</text>
</view>
```

- [ ] **Step 3: 提交代码**

```bash
git add pages/index/index.uvue
git commit -m "feat: 首页文章列表添加点击跳转详情页"
```

## archived-with: 2026-06-09-detail-page-data

### Task 3: 修改详情页获取数据

**Files:**

- Modify: `pages/detail/detail.uvue`

- [ ] **Step 1: 添加数据获取逻辑**

在 `<script setup>` 中添加数据获取逻辑：

```typescript
import { getHomeData } from '@/utils/home'

const article = ref<any>(null)
const loading = ref(true)

// 获取文章详情
const fetchData = async (id: number) => {
  try {
    loading.value = true
    const res = await getHomeData()
    const posts = res || []
    article.value = posts.find((item: any) => item.id === id)
  } catch (error) {
    console.error('获取数据失败:', error)
    uni.showToast({ title: '获取数据失败', icon: 'none' })
  } finally {
    loading.value = false
  }
}

// 页面加载时获取参数并请求数据
onLoad((options) => {
  const id = options?.id
  if (id) {
    fetchData(Number(id))
  } else {
    loading.value = false
    console.error('缺少文章 ID 参数')
  }
})
```

- [ ] **Step 2: 提交代码**

```bash
git add pages/detail/detail.uvue
git commit -m "feat: 详情页添加数据获取逻辑"
```

## archived-with: 2026-06-09-detail-page-data

### Task 4: 修改详情页模板渲染

**Files:**

- Modify: `pages/detail/detail.uvue`

- [ ] **Step 1: 修改模板渲染文章详情**

替换原有的静态内容为动态渲染：

```html
<template>
  <view class="container">
    <view v-if="loading" class="loading">
      <text>加载中...</text>
    </view>
    <view v-else-if="article" class="content">
      <text class="id">ID: {{ article.id }}</text>
      <text class="userId">用户ID: {{ article.userId }}</text>
      <text class="title">{{ article.title }}</text>
      <text class="body">{{ article.body }}</text>
    </view>
    <view v-else class="empty">
      <text>暂无数据</text>
    </view>
  </view>
</template>
```

- [ ] **Step 2: 添加详情页样式**

更新样式部分：

```css
.loading {
  padding: 20px;
  text-align: center;
}

.content {
  width: 90%;
  background-color: #ffffff;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.id {
  font-size: 14px;
  color: #999999;
  margin-bottom: 10px;
}

.userId {
  font-size: 14px;
  color: #999999;
  margin-bottom: 10px;
}

.title {
  font-size: 20px;
  font-weight: bold;
  color: #333333;
  margin-bottom: 15px;
}

.body {
  font-size: 16px;
  color: #666666;
  line-height: 1.6;
}

.empty {
  padding: 20px;
  text-align: center;
}
```

- [ ] **Step 3: 提交代码**

```bash
git add pages/detail/detail.uvue
git commit -m "feat: 详情页添加模板渲染和样式"
```
