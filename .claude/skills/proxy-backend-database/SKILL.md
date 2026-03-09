---
name: proxy-backend-database
description: >-
  Proxy2.0 数据库设计与操作规范：表设计命名、字段类型、索引规则、DO 对象、Mapper 接口、
  SQL 安全、性能优化、MyBatis Plus 使用。Use when: (1) Creating database tables or SQL,
  (2) Writing DO classes, (3) Creating Mapper interfaces, (4) Reviewing database code,
  (5) Optimizing queries, (6) Working with MyBatis Plus.
---

# 数据库规范

## 表命名

| 对象 | 规则 | 示例 |
|------|------|------|
| 表名 | `{模块}_{实体}` snake_case | `system_dict_type` |
| 关联表 | `{主表}_{从表}` | `system_role_menu` |
| 字段 | snake_case | `user_id`、`create_time` |
| 主键 | `id` | — |
| 外键 | `{关联实体}_id` | `user_id` |
| 唯一索引 | `uk_{表名}_{字段}` | `uk_system_dict_type_type` |
| 普通索引 | `idx_{表名}_{字段}` | `idx_pay_order_merchant_id` |

## 必备字段（BaseDO）

```sql
id          BIGINT       NOT NULL AUTO_INCREMENT COMMENT '主键',
creator     VARCHAR(64)  DEFAULT '' COMMENT '创建者',
create_time DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
updater     VARCHAR(64)  DEFAULT '' COMMENT '更新者',
update_time DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
deleted     BIT(1)       NOT NULL DEFAULT b'0' COMMENT '是否删除',
PRIMARY KEY (id)
```

可选：`tenant_id BIGINT NOT NULL COMMENT '租户编号'`

## 字段类型

| 用途 | MySQL 类型 | 说明 |
|------|-----------|------|
| 主键 | `BIGINT AUTO_INCREMENT` | — |
| 金额 | `INT` | 单位：分 |
| 精确小数 | `DECIMAL(18,2)` | 非金额 |
| 布尔 | `BIT(1)` | — |
| 枚举/状态 | `TINYINT` / `INT` | 对应 Java 枚举 code |
| 时间 | `DATETIME` | 对应 `LocalDateTime` |
| JSON | `JSON` / `VARCHAR(4096)` | 配合 `JacksonTypeHandler` |

## 索引规则

- 外键字段必须建索引
- 单表索引 <=6 个，联合索引字段 <=4 个
- 联合索引：等值在前，范围在后
- 唯一索引必须包含 `deleted`/`deleted_time`

## DO 模板

```java
@TableName("{模块}_{实体}")
@KeySequence("{模块}_{实体}_seq")
@Data
@EqualsAndHashCode(callSuper = true)
@ToString(callSuper = true)
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class {Entity}DO extends BaseDO {
    @TableId
    private Long id;
    // 业务字段...
}
```

- 必须继承 `BaseDO`、用 `@TableName`、用 `@TableId`
- 枚举字段 Javadoc 用 `@link` 引用枚举类
- JSON 字段用 `@TableField(typeHandler = JacksonTypeHandler.class)`

## Mapper 模板

```java
@Mapper
public interface {Entity}Mapper extends BaseMapperX<{Entity}DO> {
    default PageResult<{Entity}DO> selectPage({Entity}PageReqVO reqVO) {
        return selectPage(reqVO, new LambdaQueryWrapperX<{Entity}DO>()
                .likeIfPresent({Entity}DO::getName, reqVO.getName())
                .eqIfPresent({Entity}DO::getStatus, reqVO.getStatus())
                .betweenIfPresent({Entity}DO::getCreateTime, reqVO.getCreateTime())
                .orderByDesc({Entity}DO::getId));
    }
}
```

- 继承 `BaseMapperX<T>`（非原生 `BaseMapper`）
- 条件用 `xxxIfPresent`（自动跳过空值）
- 排序默认 `orderByDesc(DO::getId)`

## SQL 安全

- MyBatis 用 `#{}` 不用 `${}`
- 多租户自动注入 `tenant_id`（框架透明处理）

## 性能优化

- 禁止 `SELECT *`，用 `LambdaQueryWrapper.select()` 指定字段
- 批量操作代替循环：`insertBatch` / `updateBatch`
- 大数据分页用游标：`WHERE id > #{lastId} LIMIT n`
- 只读查询用 `@Transactional(readOnly = true)`
- 事务范围尽量小，不包裹远程调用

## 建表 SQL 模板

```sql
CREATE TABLE {模块}_{实体} (
    id          BIGINT       NOT NULL AUTO_INCREMENT COMMENT '主键',
    name        VARCHAR(100) NOT NULL DEFAULT '' COMMENT '名称',
    status      TINYINT      NOT NULL DEFAULT 0 COMMENT '状态：0-开启，1-关闭',
    creator     VARCHAR(64)  DEFAULT '' COMMENT '创建者',
    create_time DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    updater     VARCHAR(64)  DEFAULT '' COMMENT '更新者',
    update_time DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    deleted     BIT(1)       NOT NULL DEFAULT b'0' COMMENT '是否删除',
    tenant_id   BIGINT       NOT NULL DEFAULT 0 COMMENT '租户编号',
    PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='{实体中文名}表';
```
