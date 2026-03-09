# Proxy Skills

统一管理的 Claude Code 技能集合，专注于 Proxy 项目后端开发的最佳实践。

## 📦 技能列表

### Backend Development

- **proxy-backend-global** - 后端全局编码规范
- **proxy-backend-database** - 数据库设计与操作规范
- **proxy-backend-api-design** - API 接口设计规范
- **proxy-backend-impl-workflow** - Java CRUD 接口实现工作流

## 🚀 安装方式

### 方式 1: 使用 Claude Code Plugin Marketplace

```bash
/plugin marketplace add
# 搜索并安装 proxy-skills
```

### 方式 2: 手动安装

```bash
# 克隆仓库
git clone https://github.com/YOUR_USERNAME/proxy-skills.git

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

### proxy-backend-global
后端全局编码规范，包含命名、注释、分层架构、代码风格、依赖注入等全局约束。

**触发场景：**
- 创建新 Java 类
- 代码审查
- 重构
- 任何后端开发任务

### proxy-backend-database
数据库设计与操作规范，包含表设计命名、字段类型、索引规则、DO 对象、Mapper 接口等。

**触发场景：**
- 创建数据库表或 SQL
- 编写 DO 类
- 创建 Mapper 接口
- 数据库代码审查
- 查询优化

### proxy-backend-api-design
API 接口设计规范，包含 RESTful URL 约定、Controller 模板、VO 设计、Swagger 文档注解等。

**触发场景：**
- 创建新 Controller/API 端点
- 设计 VO 类
- 编写 Swagger 注解
- 定义错误码
- API 设计合规性审查

### proxy-backend-impl-workflow
Java CRUD 接口实现工作流，从需求到完整交付的 10 步标准流程。

**触发场景：**
- 实现新实体的 CRUD 功能
- 添加完整技术栈的新 API 端点
- 遵循标准实现工作流

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
