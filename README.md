# uni-app-x

基于 uni-app-x 框架开发的跨端应用，支持微信小程序。

## 项目简介

这是一个使用 uni-app-x 框架搭建的基础项目模板，集成了常用的功能模块，适合作为新项目的起始模板。

## 技术栈

- **框架**: uni-app-x
- **状态管理**: Pinia
- **工具库**: number-precision (精确计算)
- **包管理**: pnpm

## 项目结构

```
uni-app-x/
├── pages/                    # 页面目录
│   ├── index/               # 首页
│   │   └── index.uvue
│   ├── detail/              # 详情页
│   │   └── detail.uvue
│   └── mine/                # 我的页面
│       └── mine.uvue
├── stores/                  # Pinia 状态管理
│   └── counter.uts          # 计数器 Store
├── utils/                   # 工具函数
│   ├── env.uts              # 环境配置
│   ├── home.uts             # 首页 API
│   └── request.uts          # 网络请求封装
├── static/                  # 静态资源
│   ├── tabbar/              # TabBar 图标
│   └── logo.png
├── App.uvue                 # 应用入口
├── main.uts                 # 主入口文件
├── pages.json               # 页面配置
├── manifest.json            # 应用配置
├── uni.scss                 # 全局样式
└── package.json             # 依赖配置
```

## 功能特性

### 已集成

- **Pinia 状态管理** - 全局状态管理解决方案
- **网络请求封装** - 基于 uni.request 的请求工具，支持拦截器
- **精确计算** - 使用 number-precision 解决浮点数精度问题
- **TabBar 导航** - 底部导航栏（首页、详情、我的）

### 页面说明

| 页面   | 路径                | 功能                         |
| ------ | ------------------- | ---------------------------- |
| 首页   | pages/index/index   | 数据列表展示、Pinia 使用示例 |
| 详情页 | pages/detail/detail | 详情内容展示                 |
| 我的   | pages/mine/mine     | 个人中心、菜单列表           |

## 快速开始

### 环境要求

- HBuilderX 最新版本
- 微信开发者工具

### 运行项目

1. 使用 HBuilderX 打开项目
2. 点击菜单 `运行` → `运行到小程序模拟器` → `微信开发者工具`
3. 或点击 `运行` → `运行到浏览器` → Chrome

### 发布项目

1. 点击菜单 `发行` → `小程序-微信`
2. 填写小程序 AppID
3. 在微信开发者工具中上传代码

## 常用 API

### 网络请求

```typescript
import { get, post } from '@/utils/request'

// GET 请求
const res = await get('/api/users', { page: 1 })

// POST 请求
const res = await post('/api/users', { name: 'test' })
```

### 状态管理

```typescript
import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()
store.increment()
console.log(store.count)
```

### 精确计算

```typescript
import NP from 'number-precision'

NP.plus(0.1, 0.2) // 0.3
NP.minus(0.3, 0.1) // 0.2
NP.times(1.005, 100) // 100.5
```

## 配置说明

### 环境配置

编辑 `utils/env.uts` 修改环境相关配置：

```typescript
const config = {
  development: {
    apiBaseUrl: 'https://dev-api.example.com',
  },
  production: {
    apiBaseUrl: 'https://api.example.com',
  },
}
```

### 页面配置

编辑 `pages.json` 添加新页面或修改 TabBar：

```json
{
  "pages": [{ "path": "pages/new/new" }],
  "tabBar": {
    "list": [{ "pagePath": "pages/new/new", "text": "新页面" }]
  }
}
```

## 注意事项

- 本项目使用 uni-app-x 框架，需要 HBuilderX 进行开发
- 微信小程序需要在微信公众平台注册账号并获取 AppID
- 发布前请确保已配置好小程序的服务器域名

## License

ISC
