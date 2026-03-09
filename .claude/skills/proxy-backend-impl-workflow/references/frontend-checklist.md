# 前端对接清单模板

完成 CRUD 接口实现后，使用此模板生成供前端对接的清单。

## 📋 接口清单

| # | 方法 | 路径 | 说明 |
|----|------|------|------|
| 1 | POST | `/admin-api/{模块}/{实体-kebab}/create` | 创建 |
| 2 | PUT | `/admin-api/{模块}/{实体-kebab}/update` | 修改 |
| 3 | DELETE | `/admin-api/{模块}/{实体-kebab}/delete` | 删除 |
| 4 | DELETE | `/admin-api/{模块}/{实体-kebab}/delete-list` | 批量删除 |
| 5 | GET | `/admin-api/{模块}/{实体-kebab}/get` | 获取详情 |
| 6 | GET | `/admin-api/{模块}/{实体-kebab}/page` | 分页查询 |
| 7 | GET | `/admin-api/{模块}/{实体-kebab}/list` | 列表查询（可选） |
| 8 | GET | `/admin-api/{模块}/{实体-kebab}/export-excel` | 导出 Excel（可选） |

## 🔐 权限码

```
{模块}:{实体}:create    # 创建权限
{模块}:{实体}:update    # 修改权限
{模块}:{实体}:delete    # 删除权限
{模块}:{实体}:query     # 查询权限
{模块}:{实体}:export    # 导出权限
```

## 📚 数据字典类型

| 字段 | 字典类型 | 说明 |
|------|---------|------|
| status | COMMON_STATUS | 通用状态（0-开启，1-关闭） |
| ... | ... | ... |

## 📂 生成的代码文件

### 错误码
- `xm-module-{模块}/src/main/java/cn/xm/partner/module/{模块}/enums/ErrorCodeConstants.java`

### 数据层
- `dal/dataobject/{entity}/{Entity}DO.java`
- `dal/mysql/{entity}/{Entity}Mapper.java`

### 业务层
- `service/{entity}/{Entity}Service.java`
- `service/{entity}/{Entity}ServiceImpl.java`

### 表现层
- `controller/admin/{entity}/{Entity}Controller.java`
- `controller/admin/{entity}/vo/{Entity}PageReqVO.java`
- `controller/admin/{entity}/vo/{Entity}SaveReqVO.java`
- `controller/admin/{entity}/vo/{Entity}RespVO.java`

### 数据库脚本
- `sql/ddl/{模块}_{实体}.sql`（建表）
- `sql/dml/system_menu.sql`（菜单权限）
- `sql/dml/system_dict_data.sql`（数据字典 - 如需要）

## ✅ 前端实现清单

- [ ] 根据接口清单实现创建、修改、删除、查询等页面
- [ ] 在权限检查时使用提供的权限码
- [ ] 在下拉框、状态展示时使用对应数据字典
- [ ] 验证导入/导出功能（如实现）
- [ ] 测试分页、排序、搜索功能
- [ ] 联调所有错误码，确保错误提示清晰

## 📝 VO 字段说明

### 创建/修改请求 (SaveReqVO)

```json
{
  "id": "1024",  // 修改时必传，创建时不传
  "name": "xxx",
  "status": 0,
  // ... 其他字段
}
```

### 响应 (RespVO)

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "id": "1024",
    "name": "xxx",
    "status": 0,
    "createTime": "2024-01-01 10:00:00",
    "creator": "admin",
    "updateTime": "2024-01-01 10:00:00",
    "updater": "admin"
  }
}
```

### 分页响应 (PageResult)

```json
{
  "code": 0,
  "msg": "",
  "data": {
    "total": 100,
    "list": [
      { "id": 1, "name": "xxx", ... },
      { "id": 2, "name": "yyy", ... }
    ]
  }
}
```

## 🔗 相关 Skills

- **proxy-backend-api-design**: API 设计规范（Controller、VO、Swagger 注解）
- **proxy-backend-database**: 数据库规范（DO、Mapper、SQL）
- **proxy-backend-global**: 全局编码规范（命名、依赖注入、工具使用）
