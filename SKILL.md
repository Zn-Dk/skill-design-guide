---
name: skill-design-guide
description: |
  Agent Skill 设计与审计指南。
  覆盖目录结构、DSL、性能预算与索引写法规范。
  适用场景：新建 Skill、重构 Skill、评审 Skill 架构。
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Agent Skill 设计范式

本技能是一个"元技能"——用于创建和审计其他 Skill。

## 适用场景

- 从零新建一个 Skill
- 对已有 Skill 进行架构升级或质量审计
- 评审 Skill 的目录结构和内容组织是否合理

## 核心设计原则（铁律）

> ⚠️ **最小必要原则**：不要为了"完整"而创建空目录或冗余文档。每个文件必须有实际内容才创建。

> ⚠️ **确定性优先**：能用 grep 模式 / 代码模板 / 脚本做的，绝不交给 LLM 自然语言理解。

> ⚠️ **Token 预算意识**：frontmatter `description` ≤ 250 字符，入口 `SKILL.md` ≤ 2KB。Agent 在 Level 1 应能完成 80% 决策，仅 20% 边界 case 需深入子文档。

## 执行模式分档

Skill 的目录结构由**执行模式**决定，而非复杂度：

| 档位 | 执行模式 | 典型场景 | 默认结构 |
|------|---------|---------|---------|
| **Lite** | 纯指引 | 行为准则、编码规范、样式指南 | 单文件 `SKILL.md` |
| **Standard** | 审查/生成 | Code Review、Spec 生成、设计指南 | `SKILL.md` + `phases/` or `modules/` + `assets/` |
| **Heavy** | Pipeline | 数据分析、故障诊断、自动化报告 | 完整结构 + `scripts/` + `schemas/` + `state.json` |

> 默认选择 **Lite**，只有当单文件无法承载时才升级到 Standard，只有需要脚本执行和状态管理时才升级到 Heavy。

## 子文档索引

本技能采用 **分层架构**，按主题拆分为独立文档，按需加载。

| 章节 | 文件 | 职责 | 何时加载 |
|------|------|------|---------|
| 核心设计哲学 | [phases/01_philosophy.md](phases/01_philosophy.md) | 最小必要、确定性优先、Token 预算 | 新建或审计 Skill 前必读 |
| 目录结构规范 | [phases/02_structure.md](phases/02_structure.md) | 三档模型、目录职责与升档条件 | 判断 Lite / Standard / Heavy 时 |
| Pipeline 设计模式 | [phases/03_pipeline.md](phases/03_pipeline.md) | Heavy 档阶段划分与状态管理 | 需要 Pipeline / 状态传递时 |
| 全局约束设计 | [phases/04_constraints.md](phases/04_constraints.md) | 约束分类、层级与表达方式 | 需要定义全局约束时 |
| 知识管理模式 | [phases/05_knowledge.md](phases/05_knowledge.md) | SSOT、知识分层与引用策略 | 设计知识库结构时 |
| 自反哺闭环 | [phases/06_feedback-loop.md](phases/06_feedback-loop.md) | 纠错记录与经验固化机制 | 需要建立反馈闭环时 |
| 多实例并行 | [phases/07_team-mode.md](phases/07_team-mode.md) | Team 模式与协作协议 | 需要多 Agent 并行时 |
| 质量保障 | [phases/08_quality.md](phases/08_quality.md) | 质量原则、审计策略与护栏 | 需要建立自检与审计时 |
| 结构化语言 DSL | [phases/09_dsl.md](phases/09_dsl.md) | Frontmatter、规则 DSL 与表头规范 | 设计文档写法时必读 |
| 性能与披露策略 | [phases/10_performance.md](phases/10_performance.md) | Token 预算、渐进式披露、信息密度 | 需要压缩入口上下文时必读 |

## 资源文件

| 文件 | 用途 | 何时加载 |
|------|------|---------|
| [assets/new-skill-skeleton.md](assets/new-skill-skeleton.md) | 新 Skill 脚手架模板（三档均有） | 新建 Skill 或重构目录结构时 |
| [assets/new-skill-checklist.md](assets/new-skill-checklist.md) | 新建 Skill 时的自检清单 | 设计完成后做结构自检时 |

## 查阅路径

渐进式披露，避免一次性加载过多内容：

1. **SKILL.md**（本文件）— 了解总览、核心原则、选择档位
2. **phases/01 + 02 + 09 + 10** — 必读章节，掌握设计哲学、结构规范、DSL、性能策略
3. **phases/03~08** — 按需深入具体主题
4. **assets/** — 实操时使用模板和清单

> ⚠️ 不要跳过本文件直接加载 phases 文档。先确认需要哪个主题，再按需加载。
