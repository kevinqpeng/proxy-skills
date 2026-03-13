# 编码模式

## 页面开发

### 页面文件组织

每个页面是一个 `.vue` 单文件组件（SFC），使用 `<script setup>` 语法。

```vue
<template>
  <view class="page">
    <s-layout>
      <!-- 页面内容 -->
    </s-layout>
  </view>
</template>

<script setup>
import { ref, computed } from 'vue';
import { onLoad, onShow } from '@dcloudio/uni-app';
import sheep from '@/sheep';

// 页面逻辑
const data = ref([]);

onLoad((options) => {
  // 页面加载时执行
});

onShow(() => {
  // 页面显示时执行
});
</script>

<style lang="scss" scoped>
.page {
  // 样式
}
</style>
```

### 页面路由注册

在 `pages.json` 中注册页面：

```json
{
  "pages": [
    {
      "path": "pages/goods/detail",
      "style": {
        "navigationBarTitleText": "商品详情",
        "enablePullDownRefresh": true
      },
      "meta": {
        "auth": false,
        "sync": true,
        "title": "商品详情",
        "group": "商品"
      }
    }
  ]
}
```

配置说明：
- `path`: 页面路径（相对于 src/）
- `style.navigationBarTitleText`: 导航栏标题
- `style.enablePullDownRefresh`: 是否启用下拉刷新
- `meta.auth`: 是否需要登录
- `meta.sync`: 是否同步到后端
- `meta.title`: 页面标题
- `meta.group`: 页面分组

### 页面生命周期

使用 uni-app 生命周期钩子：

```javascript
import { onLoad, onShow, onHide, onUnload, onPullDownRefresh } from '@dcloudio/uni-app';

onLoad((options) => {
  // 页面加载时触发，options 为页面参数
});

onShow(() => {
  // 页面显示时触发
});

onHide(() => {
  // 页面隐藏时触发
});

onUnload(() => {
  // 页面卸载时触发
});

onPullDownRefresh(() => {
  // 下拉刷新时触发
  // 完成后调用 uni.stopPullDownRefresh()
});
```

## API 接口封装

### API 文件组织

按业务模块在 `sheep/api/` 下创建文件：

```
sheep/api/
├── member/
│   ├── auth.js      # 认证接口
│   └── user.js      # 用户接口
├── trade/
│   ├── order.js     # 订单接口
│   └── cart.js      # 购物车接口
└── promotion/
    ├── coupon.js    # 优惠券接口
    └── diy.js       # DIY 接口
```

### API 接口定义

使用 `request` 封装请求：

```javascript
import request from '@/sheep/request';

const AuthApi = {
  // 登录
  login: (data) => {
    return request({
      url: '/member/auth/login',
      method: 'POST',
      data,
      custom: {
        showSuccess: true,
        successMsg: '登录成功',
        auth: false, // 不需要 token
      },
    });
  },

  // 获取用户信息
  getUserInfo: () => {
    return request({
      url: '/member/user/get',
      method: 'GET',
      custom: {
        showLoading: true,
        auth: true, // 需要 token
      },
    });
  },

  // 退出登录
  logout: () => {
    return request({
      url: '/member/auth/logout',
      method: 'POST',
      custom: {
        showSuccess: true,
        successMsg: '退出成功',
      },
    });
  },
};

export default AuthApi;
```

### 请求配置选项

`custom` 配置项：

- `showSuccess`: 是否显示成功提示（默认 false）
- `successMsg`: 成功提示文字（默认使用后端返回）
- `showError`: 是否显示错误提示（默认 true）
- `errorMsg`: 错误提示文字（默认使用后端返回）
- `showLoading`: 是否显示 loading（默认 true）
- `loadingMsg`: loading 提示文字（默认"加载中"）
- `auth`: 是否需要登录（默认 false）
- `isToken`: 是否传递 token（默认 true）

### 在页面中使用 API

```vue
<script setup>
import AuthApi from '@/sheep/api/member/auth';
import { ref } from 'vue';

const loading = ref(false);

const handleLogin = async () => {
  loading.value = true;
  try {
    const { data } = await AuthApi.login({
      mobile: '13800138000',
      password: '123456',
    });
    console.log('登录成功', data);
  } catch (error) {
    console.error('登录失败', error);
  } finally {
    loading.value = false;
  }
};
</script>
```

## Store 状态管理

### Store 文件组织

在 `sheep/store/` 下创建 Pinia 模块：

```
sheep/store/
├── index.js    # Store 入口
├── app.js      # 应用状态
├── user.js     # 用户状态
├── cart.js     # 购物车状态
└── modal.js    # 弹窗状态
```

### Store 定义

使用 `defineStore` 定义状态：

```javascript
import { defineStore } from 'pinia';
import { clone } from 'lodash-es';
import UserApi from '@/sheep/api/member/user';

// 默认用户信息
const defaultUserInfo = {
  avatar: '',
  nickname: '',
  gender: 0,
  mobile: '',
  point: 0,
};

const user = defineStore({
  id: 'user',
  state: () => ({
    userInfo: clone(defaultUserInfo),
    isLogin: !!uni.getStorageSync('token'),
    lastUpdateTime: 0,
  }),

  getters: {
    // 是否已登录
    isLoggedIn: (state) => state.isLogin,
    // 用户昵称
    userName: (state) => state.userInfo.nickname || '未登录',
  },

  actions: {
    // 获取用户信息
    async getUserInfo() {
      const { data } = await UserApi.getUserInfo();
      this.userInfo = data;
      this.lastUpdateTime = Date.now();
      return data;
    },

    // 更新用户信息
    setUserInfo(userInfo) {
      this.userInfo = { ...this.userInfo, ...userInfo };
    },

    // 登录
    setLogin(token) {
      this.isLogin = true;
      uni.setStorageSync('token', token);
    },

    // 退出登录
    logout() {
      this.isLogin = false;
      this.userInfo = clone(defaultUserInfo);
      uni.removeStorageSync('token');
    },
  },

  // 持久化配置
  persist: {
    enabled: true,
    strategies: [
      {
        key: 'user-store',
        storage: {
          getItem: (key) => uni.getStorageSync(key),
          setItem: (key, value) => uni.setStorageSync(key, value),
        },
      },
    ],
  },
});

export default user;
```

### 在页面中使用 Store

```vue
<script setup>
import { computed } from 'vue';
import sheep from '@/sheep';

// 获取 store
const userStore = sheep.$store('user');

// 使用 state
const userInfo = computed(() => userStore.userInfo);
const isLogin = computed(() => userStore.isLogin);

// 使用 getters
const userName = computed(() => userStore.userName);

// 调用 actions
const handleLogout = () => {
  userStore.logout();
};
</script>

<template>
  <view>
    <text>{{ userName }}</text>
    <button v-if="isLogin" @click="handleLogout">退出登录</button>
  </view>
</template>
```

## 组件开发

### 组件分类

- **业务组件** (`sheep/components/`): 使用 `s-` 前缀，包含业务逻辑
- **UI 组件** (`sheep/ui/`): 使用 `su-` 前缀，纯 UI 展示

### 组件目录结构

每个组件一个目录，目录名与组件名一致：

```
sheep/components/
└── s-goods-item/
    └── s-goods-item.vue

sheep/ui/
└── su-button/
    └── su-button.vue
```

### 组件定义

```vue
<!-- sheep/ui/su-button/su-button.vue -->
<template>
  <button
    class="su-button"
    :class="[`su-button--${type}`, `su-button--${size}`]"
    @click="handleClick"
  >
    <slot></slot>
  </button>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  type: {
    type: String,
    default: 'primary',
    validator: (value) => ['primary', 'default', 'danger'].includes(value),
  },
  size: {
    type: String,
    default: 'medium',
    validator: (value) => ['small', 'medium', 'large'].includes(value),
  },
});

const emit = defineEmits(['click']);

const handleClick = (e) => {
  emit('click', e);
};
</script>

<style lang="scss" scoped>
.su-button {
  // 样式
}
</style>
```

### 组件使用

由于配置了 easycom，无需手动 import：

```vue
<template>
  <view>
    <s-goods-item :data="goods" />
    <su-button type="primary" @click="handleClick">点击</su-button>
  </view>
</template>

<script setup>
const handleClick = () => {
  console.log('按钮点击');
};
</script>
```

## 工具函数

### 工具函数组织

- **通用工具**: 放在 `utils/` 目录
- **业务辅助**: 放在 `sheep/helper/` 目录

### 工具函数示例

```javascript
// utils/textUtils.js
export const formatPrice = (price) => {
  return `¥${(price / 100).toFixed(2)}`;
};

export const formatDate = (timestamp) => {
  const date = new Date(timestamp);
  return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()}`;
};
```

### 常量定义

```javascript
// sheep/helper/const.js
export const ORDER_STATUS = {
  UNPAID: 0,
  PAID: 1,
  DELIVERED: 2,
  COMPLETED: 3,
  CANCELLED: 4,
};

export const ORDER_STATUS_TEXT = {
  [ORDER_STATUS.UNPAID]: '待支付',
  [ORDER_STATUS.PAID]: '待发货',
  [ORDER_STATUS.DELIVERED]: '待收货',
  [ORDER_STATUS.COMPLETED]: '已完成',
  [ORDER_STATUS.CANCELLED]: '已取消',
};
```
