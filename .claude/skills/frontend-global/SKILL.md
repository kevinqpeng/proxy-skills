---
name: frontend-global
description: Vue 3 + Vite + TypeScript + Pinia 前端全局编码规范，包含命名规范、注释规范、代码风格、架构依赖等。适用于基于 vue-vben-admin v5 架构的项目。当需要遵循前端编码标准、命名约定、代码组织结构时使用此技能。
---

# 前端全局编码规范

## 技术栈

- **Vue 3.5** + **Vite 7** + **TypeScript** + **Pinia** + **Vue Router**
- **UI 框架**：主要使用 Ant Design Vue (由于应用入口为 `web-antd`)，基于最新的 `vue-vben-admin` v5 架构。
- **全局工具**：`@vueuse/core`、`Tailwind CSS`
- **Monorepo 管理**：使用 pnpm workspaces 和 Turborepo。

## 命名规范

### 目录与文件命名

- **目录名**：统一使用 kebab-case（小写连字符），如 `components/user-avatar`。
- **页面/组件文件**：统一使用 kebab-case，如 `user-detail.vue`, `index.vue`, `data.ts`。对组件应保证其语义一致。
- **配置文件/工具库**：统一使用 kebab-case。

### 代码层面命名

- **组件名（Script 内部/导出）**：统一使用 PascalCase，如 `UserDetail`、`RoleSelect`。
- **函数与变量**：统一使用 camelCase，如 `getUserList`、`isVisible`。
- **常量**：统一使用 UPPER_SNAKE_CASE 大写字母加下划线，如 `MAX_RETRY_COUNT`、`DEFAULT_HOME_PATH`。
- **类型定义（TS Interfaces/Types/Enums）**：统一使用 PascalCase，如 `UserInfo`、`SystemUserApi`。枚举类型常以 `Enum` 结尾，如 `CommonStatusEnum`。

## 注释规范

### 类与文件顶部注释
对于复杂的组件或工具类，在文件首部或类定义前增加简明扼要的说明：
```ts
/**
 * @description: 用户信息相关操作工具类
 */
```

### 函数注释
- **核心业务函数**：必须写 Javadoc（说明用途、参数、返回值）。
```ts
/**
 * 异步处理登录操作
 * @param type 登录类型
 * @param params 登录表单数据
 * @param onSuccess 登录成功后的回调函数
 */
async function authLogin(...)
```
- **简单 getter/setter 或基础事件处理器**：不需要冗余注释。

### 注释语言
- 所有注释统一使用中文（UTF-8）。
- 技术专有名词保留英文（如 Vue、Vben、API、Token）。

## 代码风格

- **缩进与格式**：严格使用 Prettier 与 ESLint 插件进行格式化（双空格、单引号、尾随逗号等，具体参考项目根目录 `.prettierrc.mjs` 和 `eslint.config.mjs`）。
- **导入顺序**：
  1. Vue / Vue Router 等核心生态
  2. Vben 核心库 (`@vben/*`)
  3. 第三方包 / UI 组件库 (如 `ant-design-vue`)
  4. 应用级别别名包 (`#/api/*`, `#/locales/*`)
  5. 当前目录本地模块/组件
- **导出方式**：推荐在文件末尾集中导出（`export { a, b }`）。

## 架构依赖与路径约束

- **Monorepo** 环境下：跨包引用请遵循工作空间约定 (`workspace:*` 或 `catalog:`)。
- **App 内部应用** (`apps/web-antd/src/`) 中引用：建议通过路径配置 `#/*` 代表当前 app 的 `src` 目录进行导入。禁止使用过于深层的相对路径如 `../../../../utils/helper`。

## 禁止事项

- 禁止在 Vue `template` 中写入复杂的业务逻辑，超过 2-3 个条件的判断必须抽取到 computed 属性或函数中。
- 禁止修改已有的、验证过运行正常的底层核心规范与库文件，除非经过充分调研。
- 禁止使用 `any` 滥用，尽量使用明确的 TypeScript 类型。实在无法确定的情况下，可考虑使用 `unknown` 或定义 `Record`。
