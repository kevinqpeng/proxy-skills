# 多端适配

## 条件编译

uni-app 使用条件编译处理平台差异，格式为 `#ifdef` / `#ifndef` / `#endif`。

### 模板中的条件编译

```vue
<template>
  <view>
    <!-- 仅在微信小程序中显示 -->
    <!-- #ifdef MP-WEIXIN -->
    <button open-type="getUserInfo">微信授权</button>
    <!-- #endif -->

    <!-- 仅在 H5 中显示 -->
    <!-- #ifdef H5 -->
    <div class="h5-only">H5 专属内容</div>
    <!-- #endif -->

    <!-- 在 App 和 H5 中显示，不在小程序中显示 -->
    <!-- #ifndef MP -->
    <view>非小程序内容</view>
    <!-- #endif -->
  </view>
</template>
```

### JS 中的条件编译

```javascript
// 微信小程序专属逻辑
// #ifdef MP-WEIXIN
wx.login({
  success: (res) => {
    console.log('微信登录', res.code);
  },
});
// #endif

// H5 专属逻辑
// #ifdef H5
window.location.href = '/login';
// #endif

// App 专属逻辑
// #ifdef APP-PLUS
plus.runtime.openURL('https://example.com');
// #endif
```

### CSS 中的条件编译

```scss
/* #ifdef MP-WEIXIN */
.wx-only {
  color: #07c160;
}
/* #endif */

/* #ifdef H5 */
.h5-only {
  font-size: 16px;
}
/* #endif */
```

## 平台标识符

| 标识符 | 平台 |
|--------|------|
| `H5` | H5 |
| `APP-PLUS` | App（Android + iOS） |
| `APP-ANDROID` | Android App |
| `APP-IOS` | iOS App |
| `MP-WEIXIN` | 微信小程序 |
| `MP-ALIPAY` | 支付宝小程序 |
| `MP` | 所有小程序 |

## 平台能力封装

使用 `sheep/platform/` 封装平台差异，避免在业务代码中直接使用条件编译。

### 支付封装

```javascript
// sheep/platform/pay.js
const pay = {
  // 发起支付
  async doPay(payInfo) {
    // #ifdef MP-WEIXIN
    return await wxPay(payInfo);
    // #endif

    // #ifdef APP-PLUS
    return await appPay(payInfo);
    // #endif

    // #ifdef H5
    return await h5Pay(payInfo);
    // #endif
  },
};

export default pay;
```

### 分享封装

```javascript
// sheep/platform/share.js
const share = {
  // 分享到微信
  shareToWeixin(data) {
    // #ifdef MP-WEIXIN
    wx.updateShareMenu({
      withShareTicket: true,
    });
    // #endif

    // #ifdef APP-PLUS
    plus.share.sendWithSystem({
      type: 'text',
      content: data.title,
    });
    // #endif
  },
};

export default share;
```

### 在业务代码中使用

```javascript
import $platform from '@/sheep/platform';

// 发起支付（无需关心平台差异）
const handlePay = async () => {
  await $platform.pay.doPay(payInfo);
};

// 分享（无需关心平台差异）
const handleShare = () => {
  $platform.share.shareToWeixin(shareData);
};
```

## 样式适配

### 安全区域适配

```scss
// 底部安全区域
.footer {
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}

// 顶部安全区域（刘海屏）
.header {
  padding-top: constant(safe-area-inset-top);
  padding-top: env(safe-area-inset-top);
}
```

### 单位使用

- 使用 `rpx` 作为响应式单位（750rpx = 屏幕宽度）
- 字体大小使用 `rpx` 或 `px`
- 避免使用 `vw`/`vh`（小程序不支持）

```scss
.goods-item {
  width: 340rpx;
  height: 200rpx;
  font-size: 28rpx;
  padding: 20rpx;
}
```

### 导航栏高度

```javascript
// 获取状态栏高度
const statusBarHeight = uni.getSystemInfoSync().statusBarHeight;

// 导航栏总高度 = 状态栏高度 + 44px（导航栏内容高度）
const navBarHeight = statusBarHeight + 44;
```

## 路由跳转

### 页面跳转方式

```javascript
// 普通跳转（保留当前页面）
uni.navigateTo({
  url: '/pages/goods/detail?id=123',
});

// 重定向（关闭当前页面）
uni.redirectTo({
  url: '/pages/index/index',
});

// 返回上一页
uni.navigateBack({
  delta: 1,
});

// 跳转到 tabBar 页面
uni.switchTab({
  url: '/pages/index/index',
});

// 关闭所有页面，打开新页面
uni.reLaunch({
  url: '/pages/index/login',
});
```

### 使用 sheep 路由封装

```javascript
import sheep from '@/sheep';

// 跳转到商品详情
sheep.$router.go('/pages/goods/detail', { id: 123 });

// 返回
sheep.$router.back();
```

## 存储适配

### 使用 uni.storage

```javascript
// 存储数据
uni.setStorageSync('key', value);

// 读取数据
const value = uni.getStorageSync('key');

// 删除数据
uni.removeStorageSync('key');

// 清空所有数据
uni.clearStorageSync();
```

### 异步存储

```javascript
// 异步存储（推荐用于大数据）
uni.setStorage({
  key: 'key',
  data: value,
  success: () => {
    console.log('存储成功');
  },
});

uni.getStorage({
  key: 'key',
  success: (res) => {
    console.log('读取成功', res.data);
  },
});
```

## 网络状态

```javascript
// 获取网络状态
uni.getNetworkType({
  success: (res) => {
    console.log('网络类型', res.networkType); // wifi/2g/3g/4g/5g/none
  },
});

// 监听网络状态变化
uni.onNetworkStatusChange((res) => {
  console.log('网络状态变化', res.isConnected, res.networkType);
});
```

## 小程序特有注意事项

### 包大小限制
- 主包：2MB
- 单个分包：2MB
- 所有分包总大小：20MB

### 域名白名单
- 所有网络请求域名需在微信公众平台配置白名单
- 开发时可在微信开发者工具中关闭域名校验

### 隐私协议
- 微信小程序需要在 `app.json` 中配置隐私协议
- 首次使用需要用户授权

### 审核注意事项
- 不能有虚拟支付（iOS 需走苹果内购）
- 不能有诱导分享行为
- 不能有违规内容
