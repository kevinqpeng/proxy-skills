---
name: proxy-backend-api-design
description: >-
  Proxy2.0 API 接口设计规范：RESTful URL 约定、Controller 模板、VO 设计、Swagger 文档注解、
  错误码设计、响应格式。Use when: (1) Creating new Controller/API endpoints,
  (2) Designing VO classes (PageReqVO/SaveReqVO/RespVO), (3) Writing Swagger annotations,
  (4) Defining error codes, (5) Reviewing API design compliance.
---

# API 设计规范

## URL 约定

```
管理后台：/admin-api/{模块}/{实体}/{操作}
用户端：  /app-api/{模块}/{实体}/{操作}
```

- 实体名用 **kebab-case**（如 `dict-type`、`order-item`）
- 不使用复数形式

## HTTP 方法映射

| 操作 | 方法 | URL | 说明 |
|------|------|-----|------|
| 创建 | `POST` | `/create` | `@RequestBody` |
| 修改 | `PUT` | `/update` | `@RequestBody` |
| 删除 | `DELETE` | `/delete?id=1` | `@RequestParam` |
| 批量删除 | `DELETE` | `/delete-list?ids=1,2` | `@RequestParam` |
| 单条查询 | `GET` | `/get?id=1` | `@RequestParam` |
| 分页 | `GET` | `/page` | Query String |
| 列表 | `GET` | `/list` / `/list-all-simple` | Query String |
| 导出 | `GET` | `/export-excel` | 无 `CommonResult` |

## Controller 模板

```java
@Tag(name = "管理后台 - {实体名}")
@RestController
@RequestMapping("/{模块}/{实体-kebab}")
@Validated
public class {Entity}Controller {

    @Resource
    private {Entity}Service {entity}Service;

    @PostMapping("/create")
    @Operation(summary = "创建{实体名}")
    @PreAuthorize("@ss.hasPermission('{模块}:{实体}:create')")
    public CommonResult<Long> create{Entity}(@Valid @RequestBody {Entity}SaveReqVO createReqVO) {
        return success({entity}Service.create{Entity}(createReqVO));
    }

    @PutMapping("/update")
    @Operation(summary = "修改{实体名}")
    @PreAuthorize("@ss.hasPermission('{模块}:{实体}:update')")
    public CommonResult<Boolean> update{Entity}(@Valid @RequestBody {Entity}SaveReqVO updateReqVO) {
        {entity}Service.update{Entity}(updateReqVO);
        return success(true);
    }

    @DeleteMapping("/delete")
    @Operation(summary = "删除{实体名}")
    @Parameter(name = "id", description = "编号", required = true, example = "1024")
    @PreAuthorize("@ss.hasPermission('{模块}:{实体}:delete')")
    public CommonResult<Boolean> delete{Entity}(@RequestParam("id") Long id) {
        {entity}Service.delete{Entity}(id);
        return success(true);
    }

    @GetMapping("/get")
    @Operation(summary = "获得{实体名}详情")
    @Parameter(name = "id", description = "编号", required = true, example = "1024")
    @PreAuthorize("@ss.hasPermission('{模块}:{实体}:query')")
    public CommonResult<{Entity}RespVO> get{Entity}(@RequestParam("id") Long id) {
        return success(BeanUtils.toBean({entity}Service.get{Entity}(id), {Entity}RespVO.class));
    }

    @GetMapping("/page")
    @Operation(summary = "获得{实体名}分页")
    @PreAuthorize("@ss.hasPermission('{模块}:{实体}:query')")
    public CommonResult<PageResult<{Entity}RespVO>> get{Entity}Page(@Valid {Entity}PageReqVO pageReqVO) {
        return success(BeanUtils.toBean({entity}Service.get{Entity}Page(pageReqVO), {Entity}RespVO.class));
    }
}
```

## VO 设计

| VO 类型 | 后缀 | 用途 |
|---------|------|------|
| 分页请求 | `PageReqVO` | 继承 `PageParam`，分页查询入参 |
| 保存请求 | `SaveReqVO` | 创建+更新共用（`id` 更新时必传） |
| 响应 | `RespVO` | 详情/列表/分页返回 |
| 精简响应 | `SimpleRespVO` | 下拉选项等轻量返回 |

### VO 规则

- 创建和更新共用 `SaveReqVO`
- 必填字段用 `@NotNull`/`@NotBlank` + `message`
- 所有字段必须有 `@Schema` 注解
- 入参必须 `@Valid` 校验
- 禁止 Controller 直接接收 DO
- DO -> VO 用 `BeanUtils.toBean()`

## 响应格式

```json
{ "code": 0, "msg": "", "data": { ... } }
```

分页：`{ "code": 0, "data": { "total": 100, "list": [...] } }`

## Swagger 注解

- Controller: `@Tag(name = "管理后台 - xxx")`
- 方法: `@Operation(summary = "xxx")`
- 参数: `@Parameter(name, description, required, example)`
- VO 字段: `@Schema(description, requiredMode, example)`
- description 用中文，必填标注 `REQUIRED`，提供 `example`

## 错误码

```java
// 格式：1-{模块编号}-{实体编号}-{序号}
ErrorCode {ENTITY}_NOT_EXISTS = new ErrorCode(1_0XX_0YY_000, "当前{实体}不存在");
```

## 权限码格式

`{模块}:{实体}:{操作}`（create/update/delete/query/export）
