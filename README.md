# Proxy Skills

精选的 Claude Code 技能集合，涵盖后端、前端、数据库、API 设计等领域的最佳实践和编码规范。

## 📦 技能列表

### Backend Development

- **proxy-backend-global** - 后端全局编码规范
- **proxy-backend-database** - 数据库设计与操作规范
- **proxy-backend-api-design** - API 接口设计规范
- **proxy-backend-impl-workflow** - Java CRUD 接口实现工作流

### Frontend Development

- **proxy-frontend-global** - 前端全局编码规范
- **proxy-frontend-api** - 前端 API 对接规范
- **proxy-frontend-router** - 前端路由规范
- **proxy-frontend-store** - 前端状态管理规范
- **proxy-frontend-page** - 前端页面规范

### 即将推出

- **DevOps & CI/CD** - 部署和持续集成
- **Testing** - 测试策略和实践
- **Architecture** - 系统架构设计

## 🚀 安装方式

### 方式 1: 使用 Claude Code Plugin Marketplace

```bash
/plugin marketplace add
# 搜索并安装 proxy-skills
```

### 方式 2: 手动安装

```bash
# 克隆仓库
git clone https://github.com/kevinqpeng/proxy-skills.git

# 复制技能到 Claude 技能目录
cp -r proxy-skills/.claude/skills/* ~/.claude/skills/
```

### 方式 3: 项目级安装

在项目根目录创建 `.claude/skills/` 目录并复制所需技能：

```bash
mkdir -p .claude/skills
cp -r /path/to/proxy-skills/.claude/skills/proxy-backend-* .claude/skills/
```

## 📖 技能说明

### Backend Development

#### proxy-backend-global
后端全局编码规范，包含命名、注释、分层架构、代码风格、依赖注入等全局约束。

**触发场景：**
- 创建新 Java 类
- 代码审查
- 重构
- 任何后端开发任务

#### proxy-backend-database
数据库设计与操作规范，包含表设计命名、字段类型、索引规则、DO 对象、Mapper 接口等。

**触发场景：**
- 创建数据库表或 SQL
- 编写 DO 类
- 创建 Mapper 接口
- 数据库代码审查
- 查询优化

#### proxy-backend-api-design
API 接口设计规范，包含 RESTful URL 约定、Controller 模板、VO 设计、Swagger 文档注解等。

**触发场景：**
- 创建新 Controller/API 端点
- 设计 VO 类
- 编写 Swagger 注解
- 定义错误码
- API 设计合规性审查

#### proxy-backend-impl-workflow
Java CRUD 接口实现工作流，从需求到完整交付的 10 步标准流程。

**触发场景：**
- 实现新实体的 CRUD 功能
- 添加完整技术栈的新 API 端点
- 遵循标准实现工作流

### Frontend Development

#### proxy-frontend-global
Vue 3 + Vite + TypeScript + Pinia 前端全局编码规范，包含命名规范、注释规范、代码风格、架构依赖等。

**技术栈：** Vue 3.5, Vite 7, TypeScript, Pinia, vue-vben-admin v5

**触发场景：**
- 遵循前端编码标准
- 命名约定
- 代码组织结构
- 任何前端开发任务

#### proxy-frontend-api
前端 API 对接规范，包含文件存放结构、requestClient 请求封装、namespace 类型定义、接口方法命名约定。

**触发场景：**
- 新建或修改 API 文件
- 定义接口类型
- 调用后端接口
- API 层代码审查

#### proxy-frontend-router
前端路由规范，包含路由文件组织架构、动态路由生成、静态辅助路由编写、RouteMeta 元信息字段、路由守卫逻辑。

**触发场景：**
- 新增路由
- 配置路由权限
- 编写路由守卫
- 修改路由相关文件

#### proxy-frontend-store
前端数据状态流转规范，包含 Pinia Setup Store 与 Options Store 两种范式、持久化配置、状态边界约束。

**触发场景：**
- 新建或修改 Store
- 管理全局状态
- 配置状态持久化
- 状态管理代码审查

#### proxy-frontend-page
前端页面规范，包含 SFC 单文件组件组织、data.ts 配置分离、Vben 表格表单规范、Hooks 实例化、国际化与权限指令。

**触发场景：**
- 新建或修改 Vue 页面
- 配置表格表单
- 编写 CRUD 页面
- 页面组件代码审查

## 🔧 技能结构

每个技能遵循标准的 Claude Code 技能格式：

```
.claude/skills/
└── skill-name/
    ├── SKILL.md          # 主技能文件（包含 frontmatter 和内容）
    └── references/       # 可选：参考文档目录
        └── *.md
```

### SKILL.md 格式

```markdown
---
name: skill-name
description: >-
  技能描述。Use when: (1) 场景1, (2) 场景2
---

# 技能内容

...
```

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

Apache 2.0
