# 项目结构详解

## 目录结构

```
proxy2.0-app/src/
├── pages.json            # 页面路由与分包配置
├── manifest.json         # 应用 manifest（appid、多端配置）
├── App.vue              # 应用入口
├── main.js              # 主入口文件
├── pages/               # 页面目录
│   ├── index/           # 主包页面
│   │   ├── index.vue    # 首页
│   │   ├── user.vue     # 个人中心
│   │   ├── category.vue # 分类
│   │   ├── cart.vue     # 购物车
│   │   ├── login.vue    # 登录
│   │   └── ...
│   └── public/          # 分包页面
│       ├── setting.vue  # 设置
│       ├── richtext.vue # 富文本
│       └── ...
├── sheep/               # 业务框架层
│   ├── api/             # 接口封装
│   │   ├── member/      # 会员相关接口
│   │   ├── trade/       # 交易相关接口
│   │   ├── promotion/   # 营销相关接口
│   │   └── ...
│   ├── store/           # Pinia 状态管理
│   │   ├── index.js     # Store 入口
│   │   ├── app.js       # 应用状态
│   │   ├── user.js      # 用户状态
│   │   ├── cart.js      # 购物车状态
│   │   └── ...
│   ├── router/          # 路由封装
│   │   └── index.js
│   ├── hooks/           # 组合式 API
│   │   ├── useModal.js  # 弹窗 hooks
│   │   └── ...
│   ├── helper/          # 常量与辅助函数
│   │   ├── const.js     # 常量定义
│   │   ├── utils.js     # 工具函数
│   │   └── ...
│   ├── components/      # 业务通用组件（s- 前缀）
│   │   ├── s-layout/
│   │   ├── s-block/
│   │   └── ...
│   ├── ui/              # UI 组件（su- 前缀）
│   │   ├── su-button/
│   │   ├── su-input/
│   │   └── ...
│   ├── config/          # 应用配置
│   │   └── index.js
│   ├── request/         # 请求封装
│   │   └── index.js
│   └── platform/        # 平台能力
│       ├── pay.js       # 支付
│       ├── share.js     # 分享
│       └── ...
├── utils/               # 工具函数
│   └── textUtils.js
└── uni_modules/         # uni-app 插件
    ├── z-paging/
    ├── uni-transition/
    └── ...
```

## 目录职责

### pages/
存放所有页面文件，与 `pages.json` 配置对应。

- **主包** (`pages/index/`): 放置常用页面（首页、个人中心、分类、购物车、登录等）
- **分包** (`pages/public/` 等): 按业务模块划分，减少主包体积

### sheep/
业务框架层，封装业务逻辑和通用能力。

#### sheep/api/
按业务模块组织接口文件，与后端 API 路径对应。

示例：
- `sheep/api/member/auth.js` - 会员认证接口
- `sheep/api/trade/order.js` - 订单接口
- `sheep/api/promotion/coupon.js` - 优惠券接口

#### sheep/store/
Pinia 状态管理模块，每个文件对应一个 store。

示例：
- `app.js` - 应用全局状态（主题、模板配置等）
- `user.js` - 用户状态（登录信息、用户资料等）
- `cart.js` - 购物车状态

#### sheep/components/
业务通用组件，使用 `s-` 前缀，支持 easycom 自动导入。

示例：
- `s-layout/` - 布局组件
- `s-block/` - 区块组件
- `s-goods-item/` - 商品项组件

#### sheep/ui/
UI 基础组件，使用 `su-` 前缀，支持 easycom 自动导入。

示例：
- `su-button/` - 按钮组件
- `su-input/` - 输入框组件
- `su-popup/` - 弹窗组件

#### sheep/request/
网络请求封装，基于 luch-request。

- 统一配置 baseURL、超时、拦截器
- 封装 loading、错误处理、token 管理

#### sheep/platform/
平台能力封装，处理多端差异。

示例：
- `pay.js` - 支付能力（微信支付、支付宝支付等）
- `share.js` - 分享能力（微信分享、朋友圈分享等）

### utils/
通用工具函数，不依赖业务逻辑。

### uni_modules/
uni-app 插件目录，一般不修改。

## easycom 自动导入

在 `pages.json` 中配置：

```json
{
  "easycom": {
    "autoscan": true,
    "custom": {
      "^s-(.*)": "@/sheep/components/s-$1/s-$1.vue",
      "^su-(.*)": "@/sheep/ui/su-$1/su-$1.vue"
    }
  }
}
```

使用时无需 import：

```vue
<template>
  <s-layout>
    <su-button>点击</su-button>
  </s-layout>
</template>
```

## 分包策略

### 主包
放置高频访问页面，控制在 2MB 以内：
- 首页、个人中心、分类、购物车、登录

### 分包
按业务模块划分：
- `pages/public/` - 公共页面（设置、富文本、错误页等）
- `pages/order/` - 订单相关页面
- `pages/goods/` - 商品相关页面

在 `pages.json` 中配置：

```json
{
  "subPackages": [
    {
      "root": "pages/public",
      "pages": [
        {
          "path": "setting",
          "style": {
            "navigationBarTitleText": "设置"
          }
        }
      ]
    }
  ]
}
```
