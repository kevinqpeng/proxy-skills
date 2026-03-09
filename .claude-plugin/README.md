# Proxy Skills Plugin

这是 Proxy Skills 的 Claude Code 插件配置目录。

## 结构

- `plugin.json` - 插件元数据和技能列表
- `README.md` - 插件说明文档

## 技能列表

本插件包含以下技能：

1. **proxy-backend-global** - 后端全局编码规范
2. **proxy-backend-database** - 数据库设计与操作规范
3. **proxy-backend-api-design** - API 接口设计规范
4. **proxy-backend-impl-workflow** - Java CRUD 接口实现工作流

## 安装

### 通过 Claude Code Plugin Marketplace

```bash
/plugin marketplace add proxy-skills
```

### 手动安装

```bash
# 克隆仓库
git clone https://github.com/kevinqpeng/proxy-skills.git

# 复制到项目目录
cp -r proxy-skills/.claude your-project/

# 或复制到全局目录
cp -r proxy-skills/.claude/skills/* ~/.claude/skills/
```

## 使用

安装后，这些技能会自动在相关场景下被触发，或者你可以在对话中明确引用它们。

## 许可证

Apache 2.0
