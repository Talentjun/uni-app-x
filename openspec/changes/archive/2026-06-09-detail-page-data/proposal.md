## Why

详情页目前是静态内容，需要根据接口返回的详情数据进行动态渲染，提升用户体验和页面内容的丰富性。

## What Changes

- 将详情页从 tabBar 移除，改为普通页面，支持通过 navigateTo 跳转并传递参数
- 首页文章列表添加点击事件，点击后跳转到详情页并传递文章 ID
- 详情页调用 `getHomeData()` 获取列表数据，根据传入的 ID 过滤出对应文章详情
- 详情页展示文章的全部字段（id, userId, title, body）

## Capabilities

### New Capabilities

- `detail-page-data`: 详情页数据获取与渲染功能

### Modified Capabilities

- `page-navigation`: 修改页面导航配置，详情页从 tabBar 移除

## Impact

- `pages.json`: 移除详情页的 tabBar 配置
- `pages/index/index.uvue`: 添加文章点击跳转逻辑
- `pages/detail/detail.uvue`: 添加数据获取和渲染逻辑
- `utils/home.uts`: 接口调用逻辑保持不变
