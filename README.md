# OpenSpec + Superpowers Workflow

AI 编码工作流：OpenSpec（规划层）+ Superpowers（执行层）+ Karpathy 准则（约束层）。

## 使用方式

把这个仓库地址发给你的 AI 编码助手：

```
请按照 https://github.com/lyz-wave/openspec-superpowers-workflow 搭建工作流
```

AI 会自动完成安装和配置。

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

## 详细文档

→ [OpenSpec-Superpowers-Workflow-Guide.md](./OpenSpec-Superpowers-Workflow-Guide.md)

## License

MIT
