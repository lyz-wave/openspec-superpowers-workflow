# OpenSpec + Superpowers 工作流搭建指南

本文档是完整的搭建手册，交给任意 AI 编码助手即可复现整套工作流。

## 1. 概述

三层工作流架构：

- **OpenSpec**（规划层）：管理需求规格、设计文档、任务清单，负责"做什么"
- **Superpowers**（执行层）：管理探索、方案设计、编码、测试、审查，负责"怎么做"
- **Karpathy 准则**（约束层）：编码行为底线，贯穿所有编码阶段

```
propose（发散）→ brainstorming（收敛）→ writing-plans → 编码（TDD / frontend-design）→ verify → archive
                                                                 ↑ 全程遵守 karpathy.md
```

## 2. 前置依赖

| 依赖 | 版本要求 | 安装命令 |
|------|---------|---------|
| Node.js | ≥ 20.19.0 | https://nodejs.org |
| OpenSpec | latest | `npm install -g @fission-ai/openspec@latest` |
| Superpowers | — | 见 §3.2 |
| Karpathy 准则 | — | 见 §3.3 |

## 3. 安装步骤

### 3.1 安装 OpenSpec

```bash
npm install -g @fission-ai/openspec@latest
```

验证：

```bash
openspec --version
```

### 3.2 安装 Superpowers + frontend-design

Superpowers 是 Claude Code 的 skill 集合。手动安装：

```bash
# 创建 skill 目录
mkdir -p ~/.claude/skills/superpowers
mkdir -p ~/.claude/skills/frontend-design
```

**Superpowers 子技能**（放入 `~/.claude/skills/superpowers/`）：

| 子技能 | 用途 |
|--------|------|
| `brainstorming/` | 探索需求、对比方案、确定方向 |
| `writing-plans/` | 基于 tasks.md 生成详细执行计划 |
| `test-driven-development/` | 后端/逻辑代码的 TDD 实现 |
| `subagent-driven-development/` | 大型任务的并行子代理开发 |
| `requesting-code-review/` | 发起代码审查 |
| `finishing-a-development-branch/` | 完成开发分支 |
| `verification-before-completion/` | 完成前的验证检查 |
| `dispatching-parallel-agents/` | 并行任务分发 |
| `using-superpowers/` | Superpowers 使用指南 |

每个子技能目录下包含 `SKILL.md`，从 https://github.com/superpowers-ai/superpowers 或你的私有源获取。

**frontend-design 技能**（放入 `~/.claude/skills/frontend-design/SKILL.md`）：

用于前端/UI/可视化场景，生成高质量、有设计感的前端代码，避免通用 AI 美学。从 https://github.com/anthropics/claude-code-skills 或你的私有源获取。

验证：

```bash
ls ~/.claude/skills/superpowers/
# 应列出 brainstorming/ writing-plans/ test-driven-development/ 等

ls ~/.claude/skills/frontend-design/
# 应列出 SKILL.md
```

### 3.3 安装 Karpathy 编码准则

创建 `~/.claude/rules/karpathy.md`，内容如下：

```markdown
# Karpathy 编码准则（行为约束，永远激活）

来源：Andrej Karpathy 对 LLM 编码常见错误的观察。
参考：https://github.com/multica-ai/andrej-karpathy-skills

> 这些准则偏向谨慎而非速度。简单任务（typo 修复、单行改动）自行判断。

## 1. 先想再写（Think Before Coding）

**不假设、不隐藏困惑、暴露权衡。**

实现前必须：
- 明确列出假设，不确定时提问
- 有多个解读时全部列出，不默默选一个
- 有更简单方案时说出来，必要时反驳用户
- 某处不明确时停下来，指出哪里困惑，请求澄清

## 2. 简单至上（Simplicity First）

**解决问题的最少代码，不做投机性设计。**

- 不写被要求之外的功能
- 单次使用的代码不抽象
- 不为未要求的"灵活性"或"可配置性"设计
- 不处理不可能发生的错误
- 200 行能缩到 50 行 → 重写

自测：一个资深工程师会说这段代码过度设计吗？如果是，简化。

## 3. 手术式修改（Surgical Changes）

**只动必须动的，只清理自己造成的垃圾。**

编辑已有代码时：
- 不顺手改相邻代码、注释、格式
- 不重构没坏的东西
- 匹配现有代码风格（即使你觉得它不好）
- 发现无关死代码 → 提一嘴，不要删

改动造成 orphan 时：
- 删除**你自己**的改动导致未使用的 import/变量/函数
- 不删除改动前就存在的死代码（除非用户要求）

自测：每一行变更都能直接追溯到用户的请求吗？

## 4. 目标驱动（Goal-Driven Execution）

**定义成功标准，循环直到验证通过。**

把模糊任务转成可验证目标：

| 模糊指令 | 转化为 |
|---------|-------|
| "加验证" | "先写测试覆盖非法输入，再让测试通过" |
| "修 bug" | "先写复现测试，再让测试通过" |
| "重构 X" | "确保重构前后测试都通过" |

多步骤任务写明计划：
```
1. [步骤] → verify: [检查项]
2. [步骤] → verify: [检查项]
3. [步骤] → verify: [检查项]
```

强成功标准让 LLM 独立循环；弱标准（"让它能跑"）需要反复确认。

---

**这些准则生效的标志：** diff 中不必要的变更更少、因过度设计导致的重写更少、澄清性提问出现在实现之前而非出错之后。
```

### 3.4 项目级初始化

每个项目首次使用时：

```bash
cd your-project
openspec init --tools claude
```

生成内容：
- `.claude/skills/openspec-*/SKILL.md` — OpenSpec 技能
- `.claude/commands/opsx/*.md` — 斜杠命令
- `openspec/changes/` — 变更目录
- `openspec/specs/` — 主规格目录

可选：启用 expanded profile（获得更多命令）：

```bash
openspec config profile   # 选择 expanded
openspec update
```

## 4. 工作流详解

### 4.1 标准任务

```
/opsx:propose → /superpowers:brainstorming → /superpowers:writing-plans → 编码 → /opsx:verify → /opsx:archive
```

### 4.2 大型任务

```
/opsx:propose → /superpowers:brainstorming → /superpowers:writing-plans
→ /superpowers:test-driven-development → /superpowers:subagent-driven-development
→ /superpowers:requesting-code-review → /superpowers:finishing-a-development-branch
→ /opsx:verify → /opsx:archive
```

### 4.3 编码阶段分流

- 后端 / 逻辑 / 数据处理 → `/superpowers:test-driven-development`
- 前端 / UI / 可视化 → `/superpowers:frontend-design`
- 混合任务 → 先 TDD 后端逻辑，再 frontend-design 前端界面
- **所有编码必须遵守 karpathy.md**（先想再写、简单至上、手术式修改、目标驱动）

### 4.4 各阶段职责

| 阶段 | 工具 | 做什么 | 产物 |
|------|------|--------|------|
| 发散 | `/opsx:propose` | 基于描述生成初始规格 | proposal.md, specs/, design.md, tasks.md |
| 收敛 | `/superpowers:brainstorming` | 审阅产物、提出精准问题、修正方案 | 更新后的 OpenSpec 产物 |
| 方案 | `/superpowers:writing-plans` | 基于 tasks.md 生成详细执行计划 | 实施方案文档 |
| 编码（后端） | `/superpowers:test-driven-development` | 按 tasks.md 逐项实现后端逻辑 | 代码 |
| 编码（前端） | `/superpowers:frontend-design` | 设计并实现高质量前端界面 | 代码 |
| 验证 | `/opsx:verify` | 检查实现是否符合 specs | 验证报告 |
| 归档 | `/opsx:archive` | 归档变更、同步主规格 | 归档记录 |

### 4.5 工作流图

```
用户描述需求
     │
     ▼
┌─────────────┐
│ /opsx:propose│  ← 发散：生成 proposal / specs / design / tasks
└──────┬──────┘
       │
       ▼
┌──────────────────────────┐
│ /superpowers:brainstorming│  ← 收敛：审阅产物，提出具体问题，修正
└──────┬───────────────────┘
       │
       ▼
┌─────────────────────────┐
│ /superpowers:writing-plans│  ← 基于 tasks.md 生成执行计划
└──────┬──────────────────┘
       │
       ▼
       ├────────────────────────┐
       │ 后端/逻辑              │ 前端/UI
       ▼                        ▼
┌──────────────────────┐  ┌─────────────────────┐
│ test-driven-development│  │ frontend-design      │
│ （遵守 karpathy.md）   │  │ （遵守 karpathy.md）  │
└──────┬───────────────┘  └──────┬──────────────┘
       │                         │
       └────────────┬────────────┘
                    │
                    ▼
            ┌──────────────┐
            │ /opsx:verify │  ← 验证：实现是否符合 specs
            └──────┬───────┘
                   │
                   ▼
            ┌───────────────┐
            │ /opsx:archive │  ← 归档：完成变更，同步主规格
            └───────────────┘
```

## 5. 产物结构

```
项目根目录/
├── openspec/
│   ├── changes/                    # 活跃变更
│   │   └── <change-name>/
│   │       ├── .openspec.yaml      # 变更元数据
│   │       ├── proposal.md         # Why + What + Capabilities
│   │       ├── design.md           # Context + Decisions + Risks
│   │       ├── tasks.md            # 实施清单（checkbox）
│   │       └── specs/              # 需求规格
│   │           └── <capability>/
│   │               └── spec.md     # Requirements + Scenarios
│   ├── specs/                      # 主规格（归档后合并）
│   └── changes/archive/            # 已归档变更
├── .claude/
│   ├── skills/
│   │   ├── superpowers/            # Superpowers 技能（全局）
│   │   ├── frontend-design/        # 前端设计技能（全局）
│   │   └── openspec-*/             # OpenSpec 技能（项目级）
│   ├── commands/
│   │   └── opsx/                   # OpenSpec 斜杠命令
│   └── rules/
│       ├── karpathy.md             # Karpathy 编码准则（全局）
│       └── workflow.md             # 工作流规则（全局）
└── docs/
    └── superpowers/
        └── specs/                  # brainstorming 过程记录
```

## 6. 配置文件

### 6.1 全局规则（~/.claude/rules/workflow.md）

```markdown
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
- 前端 / UI / 可视化 → `/superpowers:frontend-design`
- 混合任务 → 先 TDD 后端逻辑，再 frontend-design 前端界面
- **所有编码必须遵守 `~/.claude/rules/karpathy.md`**（先想再写、简单至上、手术式修改、目标驱动）

## 职责分工

| 阶段 | 谁负责 | 做什么 |
|------|--------|--------|
| 发散 | `/opsx:propose` | 基于描述生成初始产物（proposal / specs / design / tasks） |
| 收敛 | `/superpowers:brainstorming` | 审阅产物、提出具体问题、修正方案 |
| 实施方案 | `/superpowers:writing-plans` | 基于 tasks.md 生成详细执行计划 |
| 编码（后端） | `/superpowers:test-driven-development` | 按 tasks.md 逐项实现后端逻辑，遵守 karpathy.md |
| 编码（前端） | `/superpowers:frontend-design` | 设计并实现高质量前端界面，遵守 karpathy.md |
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
```

### 6.2 全局 CLAUDE.md（~/.claude/CLAUDE.md）相关段落

```markdown
## 会话恢复

进入项目目录时，如果存在 `openspec/changes/` 目录，运行 `openspec list` 查看活跃变更，了解当前进度后再开始工作。
```

## 7. 日常使用速查

### 开始新功能

```
你：/opsx:propose "给系统加个数据导出功能"
AI：→ 生成 proposal.md / specs / design.md / tasks.md

你：/superpowers:brainstorming
AI：→ 审阅产物，提出具体问题：
    "propose 选了 CSV 格式，但你的数据有嵌套结构，
     JSON 更合适？还是两种都支持？"
你：→ 回答问题，AI 修正产物

你：/superpowers:writing-plans
AI：→ 生成详细执行计划

你：/opsx:apply（或手动按 tasks.md 编码）
AI：→ 逐项实现，全程遵守 karpathy.md

你：/opsx:verify
AI：→ 检查实现是否符合 specs

你：/opsx:archive
AI：→ 归档变更
```

### 查看当前状态

```bash
openspec list          # 列出活跃变更
openspec status        # 查看产物完成度
openspec specs --list  # 查看主规格
```

### 多任务并行

```bash
openspec new change "feature-a"    # 同时有多个变更
openspec new change "bugfix-b"
# 切换时指定名称：/opsx:apply feature-a
```

## 8. OpenSpec CLI 速查

| 命令 | 用途 |
|------|------|
| `openspec init --tools claude` | 初始化项目 |
| `openspec new change "name"` | 创建新变更 |
| `openspec status --change "name"` | 查看变更状态 |
| `openspec instructions <artifact> --change "name"` | 获取产物创建指令 |
| `openspec validate "name"` | 验证变更 |
| `openspec archive "name"` | 归档变更 |
| `openspec list` | 列出所有变更 |
| `openspec specs --list` | 列出主规格 |
| `openspec update` | 更新技能和命令文件 |
| `openspec config profile` | 切换工作流 profile |

## 9. 注意事项

1. **OpenSpec 初始化是项目级的**：每个项目目录都需要运行 `openspec init`
2. **Superpowers 和 frontend-design 是全局的**：装一次，所有项目共享
3. **karpathy.md 是全局的**：放在 `~/.claude/rules/` 下，永远激活
4. **brainstorming 修正直接写回 OpenSpec 产物**：不另建文件，避免两套产物冲突
5. **propose 先发散再收敛**：propose 生成"草稿"作为 brainstorming 的靶子，问题更精准
6. **编码阶段按类型分流**：后端走 TDD，前端走 frontend-design，混合任务两者兼用
7. **expanded profile 可选**：默认 core profile 够用，expanded 多了 `new` / `continue` / `ff` / `verify` / `bulk-archive` / `onboard`
