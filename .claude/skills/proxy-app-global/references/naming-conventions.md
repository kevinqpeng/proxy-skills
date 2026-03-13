# 命名规范

## 文件命名

### 页面文件
使用 kebab-case（短横线命名）：

```
pages/goods/detail.vue          ✓
pages/goods/goodsDetail.vue     ✗
pages/goods/GoodsDetail.vue     ✗
```

### 组件文件
组件目录和文件名使用 kebab-case，带前缀：

```
sheep/components/s-goods-item/s-goods-item.vue    ✓
sheep/ui/su-button/su-button.vue                  ✓
sheep/components/GoodsItem/GoodsItem.vue          ✗
```

### API 文件
使用 kebab-case：

```
sheep/api/member/auth.js        ✓
sheep/api/trade/order.js        ✓
sheep/api/memberAuth.js         ✗
```

### Store 文件
使用 kebab-case：

```
sheep/store/user.js             ✓
sheep/store/cart.js             ✓
sheep/store/userInfo.js         ✗
```

## 变量命名

### 普通变量
使用 camelCase（小驼峰）：

```javascript
const userInfo = {};            ✓
const user_info = {};           ✗
const UserInfo = {};            ✗
```

### 常量
使用 UPPER_SNAKE_CASE（大写下划线）：

```javascript
const API_BASE_URL = 'https://api.example.com';    ✓
const MAX_RETRY_COUNT = 3;                          ✓
const apiBaseUrl = 'https://api.example.com';       ✗
```

### 布尔值
使用 is/has/can 前缀：

```javascript
const isLogin = true;           ✓
const hasPermission = false;    ✓
const canEdit = true;           ✓
const login = true;             ✗
```

### 私有变量
使用下划线前缀：

```javascript
const _privateVar = 'private';  ✓
const privateVar = 'private';   ✗
```

## 函数命名

### 普通函数
使用 camelCase，动词开头：

```javascript
function getUserInfo() {}       ✓
function handleClick() {}       ✓
function userInfo() {}          ✗
function get_user_info() {}     ✗
```

### 事件处理函数
使用 handle 前缀：

```javascript
const handleClick = () => {};           ✓
const handleSubmit = () => {};          ✓
const onClick = () => {};               ✗
```

### API 函数
使用动词 + 名词：

```javascript
const getUserInfo = () => {};           ✓
const createOrder = () => {};           ✓
const deleteCart = () => {};            ✓
const user = () => {};                  ✗
```

## 组件命名

### 组件前缀
- 业务组件：`s-` 前缀
- UI 组件：`su-` 前缀

```vue
<!-- 业务组件 -->
<s-goods-item />
<s-layout />
<s-block />

<!-- UI 组件 -->
<su-button />
<su-input />
<su-popup />
```

### Props 命名
使用 camelCase：

```javascript
defineProps({
  userName: String,         ✓
  isVisible: Boolean,       ✓
  user_name: String,        ✗
  'user-name': String,      ✗
});
```

### Emits 命名
使用 kebab-case：

```javascript
defineEmits(['update:modelValue', 'change', 'click']);  ✓
defineEmits(['updateModelValue', 'onChange']);          ✗
```

## CSS 类名

### BEM 命名
使用 BEM（Block Element Modifier）规范：

```scss
.goods-item {}                          // Block
.goods-item__title {}                   // Element
.goods-item__title--highlight {}        // Modifier
.goods-item--disabled {}                // Modifier

// 错误示例
.goodsItem {}                           ✗
.goods_item {}                          ✗
.goods-item-title {}                    ✗
```

### 组件类名
使用组件名作为前缀：

```scss
// s-goods-item 组件
.s-goods-item {}
.s-goods-item__image {}
.s-goods-item__title {}
.s-goods-item__price {}
```

## 注释规范

### 文件注释
在文件顶部添加注释：

```javascript
/**
 * 用户认证 API
 * @description 登录、注册、退出等接口
 */
```

### 函数注释
使用 JSDoc 格式：

```javascript
/**
 * 获取用户信息
 * @param {string} userId - 用户 ID
 * @returns {Promise<Object>} 用户信息
 */
async function getUserInfo(userId) {
  // ...
}
```

### 复杂逻辑注释
在复杂逻辑前添加注释：

```javascript
// 计算优惠后的价格
// 1. 先应用优惠券
// 2. 再应用会员折扣
// 3. 最后应用满减活动
const finalPrice = calculatePrice(originalPrice, coupon, discount, promotion);
```

## 目录命名

### 业务模块目录
使用 kebab-case：

```
sheep/api/member/           ✓
sheep/api/trade/            ✓
sheep/api/memberModule/     ✗
```

### 页面目录
使用 kebab-case：

```
pages/goods/                ✓
pages/order/                ✓
pages/goodsModule/          ✗
```

## 特殊约定

### uni-app 页面路径
在 `pages.json` 中使用相对路径，不带 `src/` 前缀：

```json
{
  "path": "pages/goods/detail"      ✓
}
```

### 组件引用路径
使用 `@/` 别名：

```javascript
import UserApi from '@/sheep/api/member/user';      ✓
import UserApi from '../../../sheep/api/member/user';  ✗
```

### 图片资源命名
使用 kebab-case：

```
static/images/logo.png              ✓
static/images/icon-home.png         ✓
static/images/logoImage.png         ✗
```
