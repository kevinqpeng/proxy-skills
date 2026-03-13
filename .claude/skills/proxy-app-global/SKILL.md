---
name: proxy-app-global
description: "Proxy2.0 移动端全局编码规范：uni-app + Vue 3 项目结构、命名规范、组件规范、API 封装、Store 管理、页面路由、多端适配。Use when: (1) Creating or modifying any code in proxy2.0-app project, (2) Writing pages, components, API files, or store modules, (3) Configuring pages.json or manifest.json, (4) Working with uni-app multi-platform features (H5/小程序/App), (5) Following project structure and coding standards."
---

# Proxy2.0 移动端全局编码规范

适用于 **proxy2.0-app** 项目（uni-app + Vue 3 + Vite + Pinia）。

## 项目技术栈

- **框架**: uni-app、Vue 3、Vite
- **状态管理**: Pinia + pinia-plugin-persist-uni
- **网络请求**: luch-request
- **工具库**: dayjs、lodash、vue-i18n
- **多端支持**: H5、微信小程序、App（Android/iOS）

## 项目根路径

所有路径基于 **proxy2.0-app/** 根目录，源码位于 **proxy2.0-app/src/**。

## 核心规范概览

1. **项目结构**: 遵循 sheep 框架分层架构（详见 [references/project-structure.md](references/project-structure.md)）
2. **代码组织**: 页面、组件、API、Store 按业务模块划分（详见 [references/coding-patterns.md](references/coding-patterns.md)）
3. **命名约定**: 组件前缀 s-/su-、文件命名 kebab-case（详见 [references/naming-conventions.md](references/naming-conventions.md)）
4. **多端适配**: 条件编译、平台能力封装（详见 [references/multi-platform.md](references/multi-platform.md)）

## 快速导航

### 新建页面
1. 在 `pages/` 下创建目录和 `.vue` 文件
2. 在 `pages.json` 中注册路由
3. 详见 [references/coding-patterns.md#页面开发](references/coding-patterns.md)

### 新建 API 接口
1. 在 `sheep/api/` 下按业务模块创建文件
2. 使用 `request` 封装请求
3. 详见 [references/coding-patterns.md#API接口封装](references/coding-patterns.md)

### 新建 Store 模块
1. 在 `sheep/store/` 下创建 Pinia 模块
2. 使用 `defineStore` 定义状态
3. 详见 [references/coding-patterns.md#Store状态管理](references/coding-patterns.md)

### 新建组件
1. 业务组件放 `sheep/components/`（s- 前缀）
2. UI 组件放 `sheep/ui/`（su- 前缀）
3. 详见 [references/coding-patterns.md#组件开发](references/coding-patterns.md)

## 关键约束

- **easycom 自动导入**: `s-*` 和 `su-*` 组件无需手动 import
- **分包策略**: 主包放常用页面，其他按业务分包
- **请求配置**: 统一使用 `sheep/request/index.js` 封装
- **多端兼容**: 使用条件编译处理平台差异

## 详细规范文档

- [项目结构详解](references/project-structure.md) - 目录组织、文件职责
- [编码模式](references/coding-patterns.md) - 页面、API、Store、组件开发模式
- [命名规范](references/naming-conventions.md) - 文件、变量、组件命名约定
- [多端适配](references/multi-platform.md) - 条件编译、平台能力使用
