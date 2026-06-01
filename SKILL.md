---
name: skill-design-guide
description: |
  Agent Skill 设计范式指南。
  指导如何设计、构建和迭代高质量的 Agent Skill。
  核心职责：目录结构规范、结构化语言 DSL、性能与披露策略。
  适用场景：新建 Skill、迭代已有 Skill、Skill 架构评审。
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

> ⚠️ **Token 预算意识**：入口 SKILL.md ≤ 2KB。Agent 在 Level 1 应能完成 80% 决策，仅 20% 边界 case 需深入子文档。

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

| 章节 | 文件 | 描述 | 优先级 |
|------|------|------|--------|
| 核心设计哲学 | [phases/01_philosophy.md](phases/01_philosophy.md) | 最小必要、确定性优先、Token 预算 | 🔴 必读 |
| 目录结构规范 | [phases/02_structure.md](phases/02_structure.md) | 三档模型详细规范与选择指南 | 🔴 必读 |
| Pipeline 设计模式 | [phases/03_pipeline.md](phases/03_pipeline.md) | 阶段划分、状态管理（Heavy 型专用） | 🟡 按需 |
| 全局约束设计 | [phases/04_constraints.md](phases/04_constraints.md) | 约束分类与定义模式 | 🟡 按需 |
| 知识管理模式 | [phases/05_knowledge.md](phases/05_knowledge.md) | SSOT 原则、按需加载策略 | 🟡 按需 |
| 自反哺闭环 | [phases/06_feedback-loop.md](phases/06_feedback-loop.md) | 实时记录、周期固化（可选特性） | ⚪ 可选 |
| 多实例并行 | [phases/07_team-mode.md](phases/07_team-mode.md) | Team 模式（高级特性，Heavy 型专用） | ⚪ 可选 |
| 质量保障 | [phases/08_quality.md](phases/08_quality.md) | 通用质量原则 + 审计策略 | 🟡 按需 |
| 结构化语言 DSL | [phases/09_dsl.md](phases/09_dsl.md) | Skill 文档的写作语言规范 | 🔴 必读 |
| 性能与披露策略 | [phases/10_performance.md](phases/10_performance.md) | Token 预算、渐进式披露、信息密度 | 🔴 必读 |

## 资源文件

| 文件 | 用途 |
|------|------|
| [assets/new-skill-skeleton.md](assets/new-skill-skeleton.md) | 新 Skill 脚手架模板（三档均有） |
| [assets/new-skill-checklist.md](assets/new-skill-checklist.md) | 新建 Skill 时的自检清单 |

## 查阅路径

渐进式披露，避免一次性加载过多内容：

1. **SKILL.md**（本文件）— 了解总览、核心原则、选择档位
2. **phases/01 + 02 + 09 + 10** — 必读章节，掌握设计哲学、结构规范、DSL、性能策略
3. **phases/03~08** — 按需深入具体主题
4. **assets/** — 实操时使用模板和清单

> ⚠️ 不要跳过本文件直接加载 phases 文档。先确认需要哪个主题，再按需加载。
