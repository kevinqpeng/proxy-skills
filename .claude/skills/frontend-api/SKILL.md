---
name: frontend-api
description: 前端 API 对接规范，包含文件存放结构、requestClient 请求封装、namespace 类型定义、接口方法命名约定。适用于基于 vue-vben-admin v5 + @vben/request 的项目。当需要新建或修改 API 文件、定义接口类型、调用后端接口时使用此技能。
---

# 前端 API 对接规范

## API 组织结构

API 按照功能模块分组收敛于 `apps/web-antd/src/api/` 目录结构下：
```text
api/
├── core/                   # 核心鉴权相关API
├── request.ts              # requestClient 的网络请求实例配置与拦截器
└── {模块}/                 # 跟随业务划分的模块 (如 system, bpm, member 等)
    ├── {业务域}/
    │   └── index.ts        # 该域下的具体网络请求定义
```
示例：用户模块的 API 文件应存放在 `apps/web-antd/src/api/system/user/index.ts`。

## 请求封装与使用

所有 HTTP 请求均必须使用基于 `@vben/request` 封装提供的 `requestClient`（在 `#/api/request` 中定义）。

- 它集成了：Token 发送、自动重试登出刷新、解密/加密头、全局错误提示拦截、租户 ID 透传等功能。
- **禁止** 在组件业务代码中直接使用底层的 `axios` 实例。

## 接口定义格式

### 1. 命名空间与类型定义
对于同一业务域的接口，强制使用 `namespace` 来组织及其关联数据类型接口：

```ts
import type { PageParam, PageResult } from '@vben/request';
import { requestClient } from '#/api/request';

export namespace SystemUserApi {
  export interface User {
    id?: number;
    username: string;
    nickname: string;
  }
}
```

### 2. 接口方法命名

| 操作 | 命名范式 | 示例 |
|------|-----------|------|
| 单页/列表拉取 | `get{Entity}Page` / `get{Entity}List` | `getUserPage`, `getSimplePostList` |
| 单个详情获取 | `get{Entity}` | `getUser` |
| 创建/新增 | `create{Entity}` | `createUser` |
| 更新/修改 | `update{Entity}` | `updateUser` |
| 删除/批量删除 | `delete{Entity}` / `delete{Entity}List` | `deleteUser`, `deleteUserList` |
| 导出 | `export{Entity}` | `exportUser` |

### 3. API 请求调用示例

```ts
/** 查询用户管理列表 */
export function getUserPage(params: PageParam) {
  return requestClient.get<PageResult<SystemUserApi.User>>(
    '/system/user/page',
    { params },
  );
}

/** 修改用户 */
export function updateUser(data: SystemUserApi.User) {
  return requestClient.put('/system/user/update', data);
}

/** 批量删除用户 */
export function deleteUserList(ids: number[]) {
  return requestClient.delete(`/system/user/delete-list?ids=${ids.join(',')}`);
}
```

## 注意事项

1. **类型安全**：任何返回值的 HTTP Method 调用中，必须声明泛型，对应后台返回的 `data` 节点内的实际载荷。
2. **RESTful 契约**：使用正确的 HTTP Method（GET 去读，POST 新增/复杂交互，PUT 彻底更新，DELETE 删除）。
3. **接口异常处理**：`requestClient` 内部已配置全局错误提示，业务层无需主动包裹 `try-catch`，除非需特殊控制特定错误的交互分支。
4. **加密与安全**：对于涉及到参数加解密的特殊接口，通过 headers 中传入 `isEncrypt: true` 触发拦截器内的解密逻辑。
