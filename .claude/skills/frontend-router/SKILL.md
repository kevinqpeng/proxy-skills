---
name: frontend-router
description: 前端路由规范，包含路由文件组织架构、动态路由生成、静态辅助路由编写、RouteMeta 元信息字段、路由守卫逻辑。适用于基于 vue-vben-admin v5 的项目。当需要新增路由、配置路由权限、编写路由守卫、或修改路由相关文件时使用此技能。
---

# 前端路由规范

## 路由组织架构

路由模块主要位于 `apps/web-antd/src/router/` 目录下：

```text
apps/web-antd/src/router/
├── access.ts        # 菜单权限、动态路由生成逻辑，与后端交互生成可访问菜单
├── guard.ts         # 路由守卫（加载进度、权限校验、NProgress 等）
├── index.ts         # Router 实例创建及初始化入口
├── routes/          # 路由配置目录
│   ├── core.ts      # 基础/核心路由（登录、404兜底、Root重定向，无权限校验）
│   ├── index.ts     # 路由聚合与扁平化输出
│   └── modules/     # 页面模块化的静态/动态辅助路由配置文件
└── tongji.ts        # 统计配置
```

## 路由定义规范

### 1. 自动与动态解析结合
本项目使用 `vue-vben-admin` 内部的动态路由生成能力：
- 通过后端 API（`getAuthPermissionInfoApi` 读取菜单树）拉取前端具有权限的菜单项。
- 根据 `apps/web-antd/src/views/**/*.vue` 提供的 `componentKeys` 自动映射页面组件。
- 如果特定页面需要配置前端静态路由，将其置于 `routes/modules/` 下。

### 2. 前端静态辅助路由编写 (`routes/modules/*.ts`)
在某些场景（如图表大盘、个人中心、隐藏在菜单外但确实需要的前端路由）下，可以在 `modules` 中编写：
```ts
import type { RouteRecordRaw } from 'vue-router';
import { $t } from '#/locales';

const routes: RouteRecordRaw[] = [
  {
    path: '/system/notify-message',
    component: () => import('#/views/system/notify/my/index.vue'),
    name: 'MyNotifyMessage',
    meta: {
      title: '我的站内信',
      icon: 'ant-design:message-filled',
      hideInMenu: true,
    },
  },
];

export default routes;
```

### 3. Route Meta 元信息规范
使用定制的 `RouteMeta`，主要字段必须规范填写：
- `title`: 页面标题，尽量使用 `$t('page.xxx')` 国际化，或是业务可读中文。
- `icon`: 菜单图标（使用 Iconify 支持的图标集）。
- `hideInMenu`: `boolean`，是否在侧边栏/顶部菜单中隐藏该页面。
- `hideInBreadcrumb`: `boolean`，是否在面包屑中隐藏。
- `hideInTab`: `boolean`，是否不在多标签页中显示。
- `affixTab`: `boolean`，是否在多标签页中固定（比如 Dashboard）。
- `ignoreAccess`: `boolean`，是否忽略权限控制。

## 路由守卫逻辑 (`guard.ts`)
- **进度条**：`loadedPaths` 处理，已加载页面不在切换时二次触发长时间 NProgress。
- **登录校验**：非白名单的路由强制校验 `accessStore.accessToken`，未登录跳转至 `/auth/login`。
- **权限拉取与动态挂载**：若识别已登录但 `isAccessChecked` 为 `false`，则触发 `fetchUserInfo()`，并经过 `generateAccess` 将动态解析后的路由写入 `vue-router` 中。

## 约束提示
- **禁止在 `router/routes/core.ts` 中随意添加业务页面**，所有的业务功能页面都必须走权限校验，或作为单独模块存入 `routes/modules/` 中。
- **路径命名风格**：尽量保持小写，多单词用连字符拼接。例如 `/system/user-role`。
- **视图关联**：不要手动写很深的层级 import 组件，依赖 `() => import('#/views/...')` 实现懒加载路由视图组件。
