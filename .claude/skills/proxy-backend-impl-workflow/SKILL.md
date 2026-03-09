---
name: proxy-backend-impl-workflow
description: >-
  Proxy2.0 Java CRUD 接口实现工作流：从需求到完整交付的 10 步标准流程。
  Use when: (1) Implementing new CRUD features for an entity, (2) Adding new API endpoints
  with full stack (DO/Mapper/VO/Service/Controller/SQL), (3) Following the standard
  implementation workflow for new business entities.
---

# Java CRUD 接口实现工作流

为实体实现完整 CRUD 接口时，按以下 10 步逐步执行。

## 前置准备

1. 确认实体名、模块名、表名
2. 确认字段列表（类型、必填、校验规则）
3. 确认功能范围（增/删/改/查/导入/导出/批量删除）
4. 确认是否需要多租户隔离（`tenant_id`）

## 实施步骤

### 第 1 步：定义错误码

**文件**：`xm-module-{模块}/.../enums/ErrorCodeConstants.java`

```java
ErrorCode {ENTITY}_NOT_EXISTS = new ErrorCode(1_0XX_0YY_000, "当前{实体中文名}不存在");
```

错误码编号在已有编号段之后顺延，消息用中文。

### 第 2 步：创建 DO

**文件**：`dal/dataobject/{entity}/{Entity}DO.java`

继承 `BaseDO`，使用 `@TableName` + `@TableId` + `@Builder` 等注解。每个字段写 Javadoc。

### 第 3 步：创建 Mapper

**文件**：`dal/mysql/{entity}/{Entity}Mapper.java`

继承 `BaseMapperX<{Entity}DO>`，用 `default` 方法 + `LambdaQueryWrapperX` 实现分页。

### 第 4 步：创建 VO

**目录**：`controller/admin/{entity}/vo/`

| 文件 | 用途 |
|------|------|
| `{Entity}PageReqVO` | 分页请求，继承 `PageParam` |
| `{Entity}SaveReqVO` | 创建/更新共用 |
| `{Entity}RespVO` | 响应（含导出注解） |

所有字段加 `@Schema`，必填加 `@NotNull`/`@NotBlank`。

### 第 5 步：创建 Service 接口

**文件**：`service/{entity}/{Entity}Service.java`

每个方法必须写 Javadoc。返回 DO（Controller 层转 VO）。

标准方法：`create{Entity}` / `update{Entity}` / `delete{Entity}` / `get{Entity}` / `get{Entity}Page`

### 第 6 步：实现 Service

**文件**：`service/{entity}/{Entity}ServiceImpl.java`

- `@Resource` 注入 Mapper
- 写入前做业务校验（存在性、唯一性）
- 校验方法以 `validate` 开头
- 异常用 `throw exception(ErrorCode)`
- VO -> DO 用 `BeanUtils.toBean()`

### 第 7 步：创建 Controller

参照 `proxy-backend-api-design` skill 的 Controller 模板。

`@Tag` + `@Operation` + `@Parameter` + `@PreAuthorize` + `@Valid` + `CommonResult<T>`

### 第 8 步：编写 SQL

1. 建表 SQL（参照 `proxy-backend-database` skill 模板）
2. 菜单 SQL（目录菜单 + 按钮权限）
3. 字典 SQL（如有枚举字段需要字典展示）

### 第 9 步：自检清单

- [ ] ErrorCodeConstants 已定义
- [ ] DO 继承 BaseDO，字段有注释
- [ ] Mapper 继承 BaseMapperX，分页已实现
- [ ] VO 分类正确，字段有 `@Schema`
- [ ] Service 接口方法有 Javadoc
- [ ] ServiceImpl 校验完整
- [ ] Controller 文档注解完整，权限码正确
- [ ] 建表 SQL 包含必备字段和注释
- [ ] 菜单 SQL 权限码与 Controller 一致
- [ ] 无 TODO/FIXME，无硬编码，批量代替循环

### 第 10 步：输出前端对接清单

完成后输出接口列表、权限码、字典类型、生成文件清单供前端使用。

详细模板见 [references/frontend-checklist.md](references/frontend-checklist.md)。
