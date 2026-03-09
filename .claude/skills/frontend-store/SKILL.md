---
name: frontend-store
description: 前端数据状态流转规范，包含 Pinia Setup Store 与 Options Store 两种范式、持久化配置、状态边界约束。适用于基于 vue-vben-admin v5 + Pinia 的项目。当需要新建或修改 Store、管理全局状态、配置状态持久化时使用此技能。
---

# 前端数据状态流转规范

本项目的前端层使用 **Vue 3 新基建 Pinia** 做跨级状态管理。禁止引入和使用其它状态管理库（如 Vuex）。

## Store 的组织体系

系统的状态空间划分为两类模块：

1. **核心公共状态（Framework Store）**：位于 `packages/stores/src/modules/` 及相关目录内。（如 `access`, `user`, `tabbar`, `dict`）。
2. **应用业务状态（App Store）**：位于 `apps/web-antd/src/store/` 内。（如 `auth.ts` 等有关应用具体的登录校验、系统级交互流转状态）。

## 编写规范与设计模式

### 1. 采用 Setup Store 组合式范式（推荐）
若要写复杂的业务逻辑 store 优先考虑该方式：

```ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useAuthStore = defineStore('auth', () => {
  // state 对应 ref/reactive
  const loginLoading = ref(false);

  // getters 对应 computed
  const isCurrentlyLoading = computed(() => loginLoading.value);

  // actions 对应 普通函数
  async function authLogin(...) {
     loginLoading.value = true;
     // ...
     loginLoading.value = false;
  }

  // reset: Setup store 原生不带 $reset, 可手动导出 $reset 供外部清理
  function $reset() {
     loginLoading.value = false;
  }

  return { loginLoading, isCurrentlyLoading, authLogin, $reset };
});
```

### 2. Options Store 范式应用场景
纯粹做对象/键值保存而不涉及过量复杂联动的仓库基建，采用 Options API：
```ts
import { defineStore } from 'pinia';

export const useUserStore = defineStore('core-user', {
  state: (): AccessState => ({
    userInfo: null,
    userRoles: [],
  }),
  actions: {
    setUserInfo(userInfo: BasicUserInfo | null) {
      this.userInfo = userInfo;
    },
  },
  // 持久化方案：依靠 persistedstate 进行特定字段选择性持久缓存
  persist: {
    pick: ['accessToken', 'refreshToken', 'tenantId', 'isLockScreen'],
  },
});

// 在 HMR 或打包时保证正常响应
const hot = import.meta.hot;
if (hot) {
  hot.accept(acceptHMRUpdate(useUserStore, hot));
}
```

## 状态管理边界约束

1. **明确 Store 与局部组件状态的边界**：只供单一组件模块生命周期内流转的状态（如列表加载中状态、弹窗显隐控制）**请使用局部 `ref/reactive` 维护**。Store 仅服务于：用户信息、菜单路由、主题模式、全局字典以及多个独立模块需要频繁高耦合共享的元数据。
2. **持久化考量**：对于重要权限标记或者 Token，一定要采用 `persist` 策略保留；对于敏感安全状态（不应在重启后保留的私有数据），不应配置持久化。
3. **安全更新流则**：Store 中的 State 禁止在组件内直接被 `store.targetItem = newValue` 强行更改。必须要提供显式的 Actions `setTargetItem`，以保障可溯源。
