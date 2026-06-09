# Comet Design Handoff

- Change: detail-page-data
- Phase: design
- Mode: compact
- Context hash: e2230131c00e74552d08e90646c5d74ca5ee4713541b7ff994f91f367ec9630f

Generated-by: comet-handoff.sh

OpenSpec remains the canonical capability spec. This handoff is a deterministic, source-traceable context pack, not an agent-authored summary.

## openspec/changes/detail-page-data/proposal.md

- Source: openspec/changes/detail-page-data/proposal.md
- Lines: 1-27
- SHA256: 1ab21f4c1450f2cadda3686f005c6a04045d1036e0060f7a31b0f81a52189a08

```md
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
```

## openspec/changes/detail-page-data/design.md

- Source: openspec/changes/detail-page-data/design.md
- Lines: 1-72
- SHA256: 3ae3bec9ccd04d8932ff2b72db9ebaf3ad33abb55a647b78f579f6baadeb48fc

```md
## 架构决策

### 1. 页面导航方式

**决策**: 将详情页从 tabBar 移除，使用 `navigateTo` 跳转并传递文章 ID。

**原因**:

- Tab 页面不支持通过 `navigateTo` 传递参数
- 详情页是二级页面，不适合作为 Tab 页面
- 使用 `navigateTo` 可以支持返回上一页

### 2. 数据获取策略

**决策**: 复用 `getHomeData()` 列表接口，在前端根据 ID 过滤出对应文章。

**原因**:

- 项目已存在 `getHomeData()` 接口封装
- JSONPlaceholder API 的 `/posts/:id` 需要新增接口方法
- 列表数据量较小，前端过滤性能可接受

### 3. 数据流
```

┌─────────────────────────────────────────────────────────────┐
│ 数据流 │
├─────────────────────────────────────────────────────────────┤
│ │
│ 首页 (index) 详情页 (detail) │
│ ┌─────────────┐ ┌─────────────┐ │
│ │ getHomeData │ id │ getHomeData │ │
│ │ ↓ │ ──────────────▶│ ↓ │ │
│ │ 文章列表 │ │ 根据ID过滤 │ │
│ │ ↓ │ │ ↓ │ │
│ │ 渲染列表 │ │ 渲染详情 │ │
│ └─────────────┘ └─────────────┘ │
│ │
└─────────────────────────────────────────────────────────────┘

```

## 方案选型

### 方案 A: 复用列表接口 + 前端过滤 ✓

- 优点: 无需新增接口，实现简单
- 缺点: 列表数据量大时可能有性能问题
- 适用: 当前项目数据量小，适合此方案

### 方案 B: 新增详情接口

- 优点: 数据精准，性能好
- 缺点: 需要新增接口方法
- 适用: 数据量大或需要更多详情字段时

## 关键实现

1. **pages.json 配置**
   - 移除详情页的 tabBar 配置
   - 保留详情页的页面路径配置

2. **首页跳转逻辑**
   - 添加文章点击事件
   - 使用 `uni.navigateTo` 跳转并传递 id 参数

3. **详情页数据获取**
   - 在 `onLoad` 生命周期获取页面参数 id
   - 调用 `getHomeData()` 获取列表
   - 根据 id 过滤出对应文章
   - 使用 `ref` 保存文章数据

4. **详情页模板渲染**
   - 展示文章的全部字段
   - 添加加载状态和错误处理
```

## openspec/changes/detail-page-data/tasks.md

- Source: openspec/changes/detail-page-data/tasks.md
- Lines: 1-21
- SHA256: 63210829fe21403241a2eec0e3b5de0b1d26e03bf816711f0d1aa05da428e4b7

```md
## Tasks

- [ ] 1. 修改 pages.json 配置
  - 移除详情页的 tabBar 配置
  - 保留详情页的页面路径配置

- [ ] 2. 修改首页添加跳转逻辑
  - 为文章列表项添加点击事件
  - 使用 `uni.navigateTo` 跳转到详情页
  - 传递文章 ID 作为参数

- [ ] 3. 修改详情页获取数据
  - 在 `onLoad` 生命周期获取页面参数 id
  - 调用 `getHomeData()` 获取列表数据
  - 根据 id 过滤出对应文章
  - 使用 `ref` 保存文章数据
  - 添加加载状态和错误处理

- [ ] 4. 修改详情页模板渲染
  - 展示文章的全部字段（id, userId, title, body）
  - 优化页面布局和样式
```
