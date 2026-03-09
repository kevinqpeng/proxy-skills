---
name: frontend-vben-page
description: vue-vben-admin v5 页面开发规范，包含 SFC 单文件组件组织、data.ts 配置分离、Vben 表格表单规范（useVbenVxeGrid、useVbenModal）、Hooks 实例化、国际化与权限指令。专门用于基于 vue-vben-admin v5 框架的项目。当需要新建或修改 Vben 页面、配置 Vben 表格表单、编写 CRUD 页面时使用此技能。
---

# vue-vben-admin 页面开发规范

本项目遵循 `vue-vben-admin` v5 的设计哲学，提倡**配置化与模块化分离**的设计理念，将"纯视图"与"表格/表单配置层"区分开。

## 页面组织结构

典型的复杂列表 CRUD 页面应分为如下形式放置在 `apps/web-antd/src/views/{模块}/{子模块}/`下：
```text
user/
├── index.vue           # 列表主视图 (页面主入口，引用表格)
├── data.ts             # 包含静态和动态的 FormSchema 与 Column 配置
└── modules/            # 对话框、Drawer 或拆分出的业务子组件
    ├── form.vue                 # 新增/编辑弹窗组件
    ├── assign-role-form.vue     # 分配角色弹窗
    └── import-form.vue          # 数据导入弹窗
```

## 数据分离与 `data.ts`
不要将包含大量具体字段声明（Schema、Columns）硬编码在 `.vue` 的 script 块中。

**`data.ts`：**用于集中存放页面内 Vxe-Table（如 `useGridColumns`）、VbenForm（如 `useFormSchema`、`useGridFormSchema` 的查询条件配置）的定义：
```ts
// data.ts
import type { VbenFormSchema } from '#/adapter/form';
import type { VxeTableGridOptions } from '#/adapter/vxe-table';
import { z } from '#/adapter/form';

export function useGridFormSchema(): VbenFormSchema[] {
  return [
    { fieldName: 'username', label: '用户名称', component: 'Input' },
  ]
}

export function useGridColumns(): VxeTableGridOptions['columns'] {
  return [
    { type: 'checkbox', width: 40 },
    { field: 'username', title: '用户名称', minWidth: 120 },
  ]
}
```

## `index.vue` SFC 单文件组件规范

1. 使用 `<script lang="ts" setup>` 组合式写法。
2. 组件采用 `<Page>` 对整体页面结构包覆，提供可自适应的内容安全区域。
3. 利用 Vben 结合 Vxe-table/form 提供的统一使用方式。

### 基本引入与 Hooks 实例
```vue
<script lang="ts" setup>
import { Page, useVbenModal } from '@vben/common-ui';
import { TableAction, useVbenVxeGrid, ACTION_ICON } from '#/adapter/vxe-table';
import { message } from 'ant-design-vue';

import { useGridColumns, useGridFormSchema } from './data';
import FormDialog from './modules/form.vue';

const [FormModal, formModalApi] = useVbenModal({
  connectedComponent: FormDialog,
  destroyOnClose: true,
});

const [Grid, gridApi] = useVbenVxeGrid({
  formOptions: {
    schema: useGridFormSchema(),
  },
  gridOptions: {
    columns: useGridColumns(),
    keepSource: true,
    proxyConfig: {
      ajax: {
        query: async ({ page }, formValues) => {
           return await getUserPage({ pageNo: page.currentPage, pageSize: page.pageSize, ...formValues });
        },
      },
    },
  },
});
</script>

<template>
  <Page auto-content-height>
    <FormModal @success="gridApi.query()" />
    <Grid table-title="用户列表">
      <template #toolbar-tools>
        <TableAction :actions="[/*...*/]" />
      </template>
      <template #actions="{ row }">
         <TableAction :actions="[/*编辑/删除等*/]" />
      </template>
    </Grid>
  </Page>
</template>
```

## Vue 代码顺序规范

1. `<script setup>` 放置在最顶部。
2. `<template>` 放置在其中间。
3. `<style scoped>` 放置在最后，仅保留特需局部覆盖 CSS 时使用。

## 国际化与权限指令
- 需要国际化的文字包裹在 `$t('...')` 中引用。
- 权限控制使用内置封装配置如 TableAction 中的 `auth: ['system:user:create']` 或 v-access 等系统指令。
