# 新 Skill 脚手架模板

> 根据执行模式选择对应档位的模板。**默认从 Lite 开始，按需升级。**

---

## 档位选择决策树

```
你的 Skill 需要多阶段工作流吗？
├── 否 → 只是行为准则/规范/指南？
│   └── 是 → 【Lite 档】
├── 是 → 需要脚本执行或状态管理吗？
│   ├── 否 → 【Standard 档】
│   └── 是 → 【Heavy 档】
```

---

## Lite 档：单文件 Skill

适用：行为准则、编码规范、样式指南

```
{skill-name}/
└── SKILL.md              # 唯一文件，包含全部内容
```

### SKILL.md 模板

```markdown
---
name: {skill-name}
description: |
  {一句话描述}。
  适用场景：{场景列表}。
allowed-tools:
  - Read
---

# {Skill 标题}

{2-3 句话介绍}

## 核心规则

### 1. {规则名}

**{一句话总结}**

- {具体要求 1}
- {具体要求 2}

### 2. {规则名}

**{一句话总结}**

- {具体要求 1}
- {具体要求 2}
```

---

## Standard 档：多模块 Skill

适用：Code Review、Spec 生成、设计指南

```
{skill-name}/
├── SKILL.md              # 入口：核心原则 + 模块索引（≤ 2KB）
├── modules/              # 按职责拆分的子模块
│   ├── {module-1}.md
│   ├── {module-2}.md
│   └── {module-N}.md
└── assets/               # 模板、检查清单、速查表
    ├── {template}.md
    └── {checklist}.md
```

### SKILL.md 模板

```markdown
---
name: {skill-name}
description: |
  {一句话描述}。
  {适用场景说明}。
allowed-tools:
  - Read
  - Grep
  - Glob
---

# {Skill 标题}

{2-3 句话介绍}

## 适用场景

- {场景 1}
- {场景 2}

## 核心原则

> ⚠️ **{原则 1}**：{具体规则}

> ⚠️ **{原则 2}**：{具体规则}

## 模块索引

| 模块 | 路径 | 职责 |
|------|------|------|
| {模块 1} | [modules/{name}.md](modules/{name}.md) | {一句话} |
| {模块 2} | [modules/{name}.md](modules/{name}.md) | {一句话} |

## 快速开始

1. {第一步}
2. {第二步}
3. {第三步}
```

### 模块文件模板（modules/{name}.md）

```markdown
# {模块名}

<!-- depth: detail -->

## 职责

{该模块负责什么}

## 规则

### R1: {规则名}

- **检测方式**：{grep 模式 / 代码模式 / 人工判断}
- **判定标准**：{什么情况算违规}
- **修复方案**：{怎么修}

### R2: {规则名}

...
```

---

## Heavy 档：Pipeline 型 Skill

适用：数据分析、故障诊断、自动化报告

```
{skill-name}/
├── SKILL.md              # 入口（≤ 2KB）
├── phases/               # 阶段指引
│   ├── 01_{phase}.md
│   ├── 02_{phase}.md
│   └── 0N_examples/     # Few-shot 示例
├── scripts/              # 确定性脚本
│   ├── state_manager.py  # 状态管理
│   ├── {collector}.py    # 数据采集
│   └── auditor.py        # 审计校验
├── schemas/              # JSON Schema 约束
│   └── state.schema.json
├── assets/               # 模板、检查清单
├── knowledge/            # SSOT 知识库
└── .learnings/           # 自反哺日志
    ├── CORRECTIONS.md
    └── LEARNINGS.md
```

### SKILL.md 模板

```markdown
---
name: {skill-name}
description: |
  {一句话描述}。
  {适用场景说明}。
allowed-tools:
  - Read
  - Grep
  - Glob
  - Terminal
---

# {Skill 标题}

{2-3 句话介绍}

## 适用场景

- {场景 1}
- {场景 2}

## 核心原则

> ⚠️ **State-First**：每个阶段开始前读取 state.json，结束后写回

> ⚠️ **确定性优先**：能用脚本做的绝不交给 LLM

> ⚠️ **{原则 3}**：{具体规则}

## Pipeline 概览

| 阶段 | 执行方式 | 输入 | 输出 |
|------|---------|------|------|
| P1 {名称} | 脚本 | {输入} | {输出} |
| P2 {名称} | LLM | {输入} | {输出} |
| P3 {名称} | 脚本 | {输入} | {输出} |

## 子文档索引

| 文档 | 路径 | 说明 |
|------|------|------|
| {阶段 1} | [phases/01_xxx.md](phases/01_xxx.md) | {一句话} |
| {阶段 2} | [phases/02_xxx.md](phases/02_xxx.md) | {一句话} |
```

### .learnings/CORRECTIONS.md 模板

```markdown
# 用户纠正记录

| 日期 | 场景 | 原始输出 | 用户纠正 | 根因 | 已固化 |
|------|------|---------|---------|------|--------|
| {YYYY-MM-DD} | {场景} | {原始} | {纠正} | {根因} | ❌ |
```

---

## 升级路径

| 从 | 到 | 触发条件 | 操作 |
|----|----|---------|------|
| Lite | Standard | 单文件超过 3KB 或规则超过 10 条 | 拆分为 modules/ |
| Standard | Heavy | 需要脚本执行或阶段间状态传递 | 添加 scripts/ + schemas/ |

**铁律**：不要为了"完整"而创建空目录。每个目录必须有实际内容才创建。
