---
name: proxy-backend-global
description: >-
  Proxy2.0 后端全局编码规范：命名、注释、分层架构、代码风格、依赖注入、复杂度限制等全局约束。
  Use when writing, reviewing, or modifying any Java code in the proxy2.0-api project.
  Triggers on: (1) Creating new Java classes, (2) Code review, (3) Refactoring,
  (4) Any backend development task in this project.
---

# 后端全局编码规范

## 技术栈

- **JDK 17** + **Spring Boot 3.5** + **MyBatis Plus** + **MySQL**
- 基础包：`cn.xm.partner`
- 工具库：Hutool、Lombok、MapStruct、Swagger (SpringDoc OpenAPI 3)
- 多模块 Maven：`xm-framework`(框架层) / `xm-module-*`(业务模块) / `xm-server`(启动入口)

## 分层架构

```
xm-module-{模块}/src/main/java/cn/xm/partner/module/{模块}/
├── api/                    # 跨模块 API 接口
├── controller/
│   ├── admin/              # 管理后台（/admin-api/）
│   └── app/                # 用户端（/app-api/）
├── convert/                # MapStruct 转换器（仅复杂转换）
├── dal/
│   ├── dataobject/         # DO 数据对象
│   └── mysql/              # Mapper 接口
├── enums/                  # 枚举常量（含 ErrorCodeConstants）
├── service/                # Service 接口 + 实现
├── job/                    # 定时任务
└── mq/                     # 消息队列
```

### 层间调用规则

- Controller -> Service -> Mapper（严格单向）
- Controller 禁止直接调用 Mapper
- Service 入参用 VO，返回 DO（Controller 负责转 VO）
- 跨模块通过 `api/` 包接口，禁止直接引用其他模块 Service

## 类命名

| 类型 | 后缀 | 示例 |
|------|------|------|
| 控制器 | `Controller` | `DictTypeController` |
| 服务接口 | `Service` | `DictTypeService` |
| 服务实现 | `ServiceImpl` | `DictTypeServiceImpl` |
| Mapper | `Mapper` | `DictTypeMapper` |
| 数据对象 | `DO` | `DictTypeDO` |
| 分页请求 | `PageReqVO` | `DictTypePageReqVO` |
| 保存请求 | `SaveReqVO` | `DictTypeSaveReqVO` |
| 响应 | `RespVO` | `DictTypeRespVO` |
| 精简响应 | `SimpleRespVO` | `DictTypeSimpleRespVO` |
| 错误码 | `ErrorCodeConstants` | — |

## 方法命名

| 操作 | Controller/Service | Mapper |
|------|-------------------|--------|
| 创建 | `create{Entity}` | `insert` / `insertBatch` |
| 更新 | `update{Entity}` | `updateById` |
| 删除 | `delete{Entity}` | `deleteById` |
| 单条查询 | `get{Entity}` | `selectById` / `selectOne` |
| 分页查询 | `get{Entity}Page` | `selectPage` |
| 列表查询 | `get{Entity}List` | `selectList` |
| 校验存在 | `validate{Entity}Exists` | — |

## 依赖注入

```java
// 推荐：@Resource（项目既有风格）
@Resource
private DictTypeMapper dictTypeMapper;

// 禁止：@Autowired 字段注入
```

## 通用工具

- 对象转换（简单）：`BeanUtils.toBean(source, Target.class)`
- 对象转换（复杂）：MapStruct `@Mapper`
- 集合判空：`CollUtil.isEmpty(list)`
- 字符串判空：`StrUtil.isBlank(str)`
- 异常抛出：`throw exception(ErrorCodeConstants.XXX)`
- 返回值：`CommonResult.success(data)` / `CommonResult<PageResult<RespVO>>`

## 复杂度限制

- 方法 <=80 行，类 <=500 行，嵌套 <=3 层，参数 <=5 个，圈复杂度 <=10
- 用 Guard Clauses 提前返回减少嵌套

## 禁止事项

- 禁止 `new Thread()`，用线程池
- 禁止 `System.out.println`，用 `log.xxx()`
- 禁止空 catch 块
- 禁止循环内调用远程服务或数据库
- 禁止硬编码魔法值
- 禁止 `SELECT *`
