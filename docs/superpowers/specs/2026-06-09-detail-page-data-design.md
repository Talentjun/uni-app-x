---
comet_change: detail-page-data
role: technical-design
canonical_spec: openspec
archived-with: 2026-06-09-detail-page-data
status: final
---

# 详情页数据获取与渲染 - 技术设计文档

## 1. 概述

详情页目前是静态内容，需要根据接口返回的详情数据进行动态渲染。

### 目标

- 将详情页从 tabBar 移除，改为普通页面
- 支持通过 navigateTo 跳转并传递文章 ID
- 复用 getHomeData() 接口获取列表数据，根据 ID 过滤出对应文章
- 展示文章的全部字段（id, userId, title, body）

## 2. 架构设计

### 2.1 页面导航配置

**pages.json 变更：**

- 从 `tabBar.list` 中移除详情页配置
- 保留详情页在 `pages` 数组中的路径配置

### 2.2 数据流

```
┌─────────────────────────────────────────────────────────────┐
│                        数据流                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   首页 (index)                    详情页 (detail)           │
│   ┌─────────────┐                ┌─────────────┐           │
│   │  getHomeData │   id           │  getHomeData │           │
│   │     ↓       │ ──────────────▶│     ↓       │           │
│   │  文章列表    │                │  根据ID过滤  │           │
│   │     ↓       │                │     ↓       │           │
│   │  渲染列表    │                │  渲染详情    │           │
│   └─────────────┘                └─────────────┘           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## 3. 实现细节

### 3.1 首页跳转逻辑

```typescript
// index.uvue
const goToDetail = (id: number) => {
  uni.navigateTo({
    url: `/pages/detail/detail?id=${id}`,
  })
}
```

### 3.2 详情页数据获取

```typescript
// detail.uvue
const article = ref(null)
const loading = ref(true)

onLoad((options) => {
  const id = options?.id
  if (id) {
    fetchData(Number(id))
  }
})

const fetchData = async (id: number) => {
  try {
    loading.value = true
    const res = await getHomeData()
    const posts = res || []
    article.value = posts.find((item: any) => item.id === id)
  } catch (error) {
    console.error('获取数据失败:', error)
  } finally {
    loading.value = false
  }
}
```

### 3.3 详情页模板渲染

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

## 4. 技术风险与缓解

| 风险         | 影响     | 缓解措施             |
| ------------ | -------- | -------------------- |
| 列表数据量大 | 性能问题 | 当前数据量小，可接受 |
| id 参数缺失  | 页面异常 | 添加参数校验         |
| 接口请求失败 | 页面空白 | 添加错误处理和提示   |

## 5. 测试策略

- 验证页面跳转和参数传递
- 验证数据获取和过滤逻辑
- 验证加载状态和错误处理
- 验证页面渲染和样式显示
