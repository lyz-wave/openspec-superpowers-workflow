# Workflow 可用性测试

搭建完成后，用以下步骤验证工作流是否正常工作。

## 测试 1: OpenSpec CLI

```bash
openspec --version
# 预期：输出版本号（如 1.4.0）

openspec init --tools claude
# 预期：生成 .claude/skills/openspec-* 和 .claude/commands/opsx/
```

## 测试 2: 创建变更

```bash
openspec new change "test-feature"
# 预期：创建 openspec/changes/test-feature/

openspec status --change "test-feature"
# 预期：显示 4 个产物，proposal 状态为 ready
```

## 测试 3: 生成产物

```bash
openspec instructions proposal --change "test-feature"
# 预期：返回 proposal 模板和指令
```

按模板创建 `proposal.md`、`design.md`、`specs/`、`tasks.md` 后：

```bash
openspec status --change "test-feature"
# 预期：4/4 artifacts complete

openspec validate test-feature
# 预期：Change is valid
```

## 测试 4: 归档

```bash
openspec archive test-feature -y
# 预期：归档成功，specs 合并到 openspec/specs/

openspec list
# 预期：No active changes found

ls openspec/changes/archive/
# 预期：包含 2026-XX-XX-test-feature/
```

## 测试 5: Superpowers 技能

```bash
ls ~/.claude/skills/superpowers/
# 预期：列出 brainstorming/ writing-plans/ test-driven-development/ 等子目录

ls ~/.claude/skills/frontend-design/
# 预期：列出 SKILL.md
```

## 测试 6: Karpathy 准则

```bash
cat ~/.claude/rules/karpathy.md | head -5
# 预期：显示 Karpathy 编码准则，包含"先想再写"等内容
```

## 测试 7: Workflow 规则

```bash
cat ~/.claude/rules/workflow.md | grep "propose"
# 预期：包含 /opsx:propose → /superpowers:brainstorming 的工作流定义
```

## 测试 8: 端到端流程

在 Claude Code 中执行：

```
你：/opsx:propose "add-test-feature"
AI：→ 生成 proposal.md / specs / design.md / tasks.md

你：/superpowers:brainstorming
AI：→ 审阅产物，提出问题
你：→ 回答问题

你：/superpowers:writing-plans
AI：→ 生成执行计划

你：（按计划编码）

你：/opsx:verify
AI：→ 检查实现是否符合 specs

你：/opsx:archive
AI：→ 归档变更
```

全部通过即为搭建成功。

## 清理测试数据

```bash
rm -rf openspec/changes/archive/2026-XX-XX-test-feature
rm -rf openspec/specs/test-*
```
