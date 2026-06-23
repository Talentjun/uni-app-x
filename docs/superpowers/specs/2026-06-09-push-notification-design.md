---
comet_change: push-notification
role: technical-design
canonical_spec: openspec
---

# App 推送功能 - 技术设计文档

## 1. 概述

为 uni-app x 应用添加 UniPush 推送功能，支持前台、后台、离线推送，以及系统通知和应用内弹窗两种展示形式。

### 目标

- 集成 UniPush 推送服务
- 支持前台、后台、离线推送
- 支持系统通知栏展示
- 支持应用内弹窗展示
- 支持点击推送消息跳转到指定页面

## 2. 架构设计

### 2.1 推送流程

```
┌─────────────────────────────────────────────────────────────┐
│                    推送功能架构                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌─────────────┐      ┌─────────────┐      ┌───────────┐ │
│   │  UniPush    │ ──── │  推送服务    │ ──── │  客户端    │ │
│   │  服务器     │      │  (云端)     │      │  SDK      │ │
│   └─────────────┘      └─────────────┘      └─────┬─────┘ │
│                                                    │       │
│                               ┌────────────────────┼────┐  │
│                               ▼                    ▼    │  │
│                         ┌──────────┐         ┌────────┐ │  │
│                         │ 系统通知  │         │ 应用内  │ │  │
│                         │ 栏展示   │         │ 弹窗    │ │  │
│                         └──────────┘         └────────┘ │  │
│                               │                    │    │  │
│                               ▼                    ▼    │  │
│                         ┌─────────────────────────────┐ │  │
│                         │     点击跳转到指定页面       │ │  │
│                         └─────────────────────────────┘ │  │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 文件结构

- `manifest.json` - 配置 UniPush 模块
- `utils/push.uts` - 推送服务封装
- `components/push-popup.uvue` - 应用内弹窗组件
- `pages/index/index.uvue` - 首页集成推送功能

## 3. 实现细节

### 3.1 manifest.json 配置

启用 UniPush 模块，配置推送参数。

### 3.2 推送服务封装

```typescript
// utils/push.uts

// 初始化推送
export function initPush() {
  // 获取推送权限
  // 监听推送消息
  // 监听推送点击事件
}

// 获取 ClientId
export function getClientId(): Promise<string> {
  return new Promise((resolve, reject) => {
    uni.getPushClientId({
      success: (res) => {
        resolve(res.cid)
      },
      fail: (err) => {
        reject(err)
      },
    })
  })
}

// 监听推送消息
export function onPushMessage(callback: (message: any) => void) {
  uni.onPushMessage(callback)
}

// 监听推送点击
export function onPushClick(callback: (message: any) => void) {
  uni.onPushClick(callback)
}
```

### 3.3 应用内弹窗组件

```html
<!-- components/push-popup.uvue -->
<template>
  <view v-if="visible" class="popup-overlay" @click="close">
    <view class="popup-content" @click.stop>
      <text class="popup-title">{{ title }}</text>
      <text class="popup-body">{{ body }}</text>
      <button class="popup-btn" @click="handleClick">查看</button>
    </view>
  </view>
</template>
```

### 3.4 首页集成

在首页初始化推送服务，监听前台推送消息，显示应用内弹窗。

## 4. 技术风险与缓解

| 风险             | 影响         | 缓解措施               |
| ---------------- | ------------ | ---------------------- |
| 推送权限被拒绝   | 无法接收推送 | 引导用户开启通知权限   |
| 设备不支持推送   | 功能不可用   | 降级处理，显示提示信息 |
| 推送消息格式错误 | 解析失败     | 添加错误处理和默认值   |

## 5. 测试策略

- 验证推送初始化和 ClientId 获取
- 验证前台推送消息接收和展示
- 验证后台/离线推送消息接收
- 验证系统通知栏展示
- 验证应用内弹窗展示
- 验证点击推送跳转功能
