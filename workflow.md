# Workflow 自动规则

## 会话启动
- 新项目：复制项目级 CLAUDE.md 模板到项目目录
- 每个项目运行 `openspec init --tools claude`（如果 `openspec/` 目录不存在）
- 已有项目：检查 `openspec/` 目录是否存在，不存在则初始化

## 工作流

所有任务统一路径：**propose 先发散 → brainstorming 再收敛 → 执行**。

### 标准任务
```
/opsx:propose → /superpowers:brainstorming → /superpowers:writing-plans → 编码 → /opsx:verify → /opsx:archive
```

### 大型任务
```
/opsx:propose → /superpowers:brainstorming → /superpowers:writing-plans
→ /superpowers:test-driven-development → /superpowers:subagent-driven-development
→ /superpowers:requesting-code-review → /superpowers:finishing-a-development-branch
→ /opsx:verify → /opsx:archive
```

### 编码阶段分流
- 后端 / 逻辑 / 数据处理 → `/superpowers:test-driven-development`
- 前端 / UI / 可视化 → `/frontend-design`
- 混合任务 → 先 TDD 后端逻辑，再 frontend-design 前端界面
- **所有编码必须遵守 `~/.claude/rules/karpathy.md`**（先想再写、简单至上、手术式修改、目标驱动）

## 职责分工

| 阶段 | 谁负责 | 做什么 |
|------|--------|--------|
| 发散 | `/opsx:propose` | 基于描述生成初始产物（proposal / specs / design / tasks） |
| 收敛 | `/superpowers:brainstorming` | 审阅产物、提出具体问题、修正方案 |
| 实施方案 | `/superpowers:writing-plans` | 基于 tasks.md 生成详细执行计划 |
| 编码（后端） | `/superpowers:test-driven-development` | 按 tasks.md 逐项实现后端逻辑，遵守 karpathy.md |
| 编码（前端） | `/frontend-design` | 设计并实现高质量前端界面，遵守 karpathy.md |
| 验证 | `/opsx:verify` | 检查实现是否符合 specs |
| 归档 | `/opsx:archive` | 归档变更、同步主规格 |

## 关卡硬性要求
1. propose 完成后才可运行 brainstorming
2. brainstorming 审阅并确认后才可进入编码
3. 编码前：design.md 获批准（标准级及以上任务）
4. 编码中：遵守 karpathy.md 四条原则（先想再写、简单至上、手术式修改、目标驱动）
5. 执行中：tasks.md 每完成一步打勾 [x]
6. 提交前：测试全绿 + lint 无错误 + review 通过
7. 归档前：`/opsx:verify` 无 CRITICAL 问题

## 文件约定
- OpenSpec 产物：`openspec/changes/<name>/` 下自动管理
- brainstorming 修正直接更新 OpenSpec 产物（不另建文件）
- 主规格：`openspec/specs/`（通过 `/opsx:sync` 或 `/opsx:archive` 合并）
- 归档：`openspec/changes/archive/YYYY-MM-DD-<name>/`

## 禁令
- 不可以在未获批准的情况下开始大规模重构
- 不可以跳过测试直接交付功能
- 不可以交付"框架搭好了你自己完善"的半成品
- 不可以重复踩同一个坑
- 不可以跳过 brainstorming 直接编码（除非用户明确说"直接做"）
