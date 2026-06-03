# OpenSpec + Superpowers Workflow

AI 编码工作流：OpenSpec（规划层）+ Superpowers（执行层）+ Karpathy 准则（约束层）。

## 快速启动

### 1. 安装依赖

```bash
# Node.js ≥ 20.19.0
# https://nodejs.org

# OpenSpec
npm install -g @fission-ai/openspec@latest

# Superpowers（Claude Code skill）
# 将 superpowers/ 目录放入 ~/.claude/skills/

# Karpathy 编码准则
# 将 karpathy.md 放入 ~/.claude/rules/
```

### 2. 项目初始化

```bash
cd your-project
openspec init --tools claude
```

### 3. 开始使用

在 Claude Code 中输入：

```
/opsx:propose "你的需求描述"
```

## 工作流

```
/opsx:propose → /superpowers:brainstorming → /superpowers:writing-plans → 编码 → /opsx:verify → /opsx:archive
```

| 阶段 | 工具 | 做什么 |
|------|------|--------|
| 发散 | `/opsx:propose` | 基于描述生成 proposal / specs / design / tasks |
| 收敛 | `/superpowers:brainstorming` | 审阅产物、提出精准问题、修正方案 |
| 方案 | `/superpowers:writing-plans` | 基于 tasks.md 生成执行计划 |
| 编码（后端） | `/superpowers:test-driven-development` | TDD 实现后端逻辑 |
| 编码（前端） | `/superpowers:frontend-design` | 设计并实现高质量前端界面 |
| 验证 | `/opsx:verify` | 检查实现是否符合 specs |
| 归档 | `/opsx:archive` | 归档变更、同步主规格 |

所有编码阶段必须遵守 `karpathy.md` 四条原则：先想再写、简单至上、手术式修改、目标驱动。

## 文件结构

```
~/.claude/
├── rules/
│   ├── workflow.md      # 工作流规则（本仓库提供）
│   └── karpathy.md      # Karpathy 编码准则（本仓库提供）
└── skills/
    ├── superpowers/      # Superpowers 技能（需手动安装）
    └── frontend-design/  # 前端设计技能（需手动安装）
```

## 详细文档

→ [OpenSpec-Superpowers-Workflow-Guide.md](./OpenSpec-Superpowers-Workflow-Guide.md) — 完整搭建手册，交给任意 AI 编码助手即可复现。

## License

MIT
