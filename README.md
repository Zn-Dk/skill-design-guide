# Agent Skill 设计范式指南

一个用于指导 AI Agent 设计、构建和迭代高质量 Skill 的**元技能（Meta-Skill）**。

## 简介

本项目是一套完整的 Agent Skill 设计方法论，以 Skill 自身的形式呈现。它定义了：

- **目录结构规范** — 三档模型（Lite / Standard / Heavy），由执行模式决定
- **结构化语言 DSL** — Skill 文档的写作语言规范（Frontmatter、规则 DSL、表头规范）
- **性能与披露策略** — Token 预算管理、渐进式信息披露、信息密度优化

适用于任何需要创建、迭代或评审 Agent Skill 的场景。

## 核心设计原则（铁律）

| 原则           | 说明                                                                                        |
| -------------- | ------------------------------------------------------------------------------------------- |
| 最小必要       | 不为"完整"而创建空目录或冗余文档，每个文件必须有实际内容才创建                              |
| 确定性优先     | 能用 grep 模式 / 代码模板 / 脚本做的，不交给 LLM 自然语言理解                               |
| Token 预算意识 | frontmatter `description` ≤ 250 字符，入口 `SKILL.md` ≤ 2KB，Agent 在 Level 1 完成 80% 决策 |

## 执行模式分档

Skill 的目录结构由**执行模式**决定，而非复杂度：

| 档位         | 执行模式  | 典型场景                         | 默认结构                                          |
| ------------ | --------- | -------------------------------- | ------------------------------------------------- |
| **Lite**     | 纯指引    | 行为准则、编码规范、样式指南     | 单文件 `SKILL.md`                                 |
| **Standard** | 审查/生成 | Code Review、Spec 生成、设计指南 | `SKILL.md` + `phases/` or `modules/` + `assets/`  |
| **Heavy**    | Pipeline  | 数据分析、故障诊断、自动化报告   | 完整结构 + `scripts/` + `schemas/` + `state.json` |

> 默认选择 **Lite**，只有单文件无法承载时才升级到 Standard，只有需要脚本执行和状态管理时才升级到 Heavy。

## 目录结构

```
skill-design-guide/
├── SKILL.md                          # 入口文件（总览 + 索引 + 核心原则）
├── phases/                           # 分层文档（按需加载）
│   ├── 01_philosophy.md              # 核心设计哲学（最小必要/确定性优先/Token 预算）
│   ├── 02_structure.md               # 目录结构规范（三档模型与升档条件）
│   ├── 03_pipeline.md                # Pipeline 设计模式（Heavy 档阶段划分与状态管理）
│   ├── 04_constraints.md             # 全局约束设计（分类、层级与表达方式）
│   ├── 05_knowledge.md               # 知识管理模式（SSOT、分层与引用策略）
│   ├── 06_feedback-loop.md           # 自反哺闭环（纠错记录与经验固化）
│   ├── 07_team-mode.md               # 多实例并行（Team 模式与协作协议）
│   ├── 08_quality.md                 # 质量保障（质量原则、审计策略与护栏）
│   ├── 09_dsl.md                     # 结构化语言 DSL（Frontmatter、规则 DSL 与表头规范）
│   └── 10_performance.md             # 性能与披露策略（Token 预算与渐进式信息披露）
├── assets/                           # 资源文件
│   ├── new-skill-skeleton.md         # 新 Skill 脚手架模板（三档均有）
│   └── new-skill-checklist.md        # 新建 Skill 自检清单
├── README.md
└── LICENSE
```

## 子文档索引

| 章节           | 文件                         | 职责                             | 何时加载                        |
| -------------- | ---------------------------- | -------------------------------- | ------------------------------- |
| 核心设计哲学   | `phases/01_philosophy.md`    | 最小必要、确定性优先、Token 预算 | 新建或审计 Skill 前必读         |
| 目录结构规范   | `phases/02_structure.md`     | 三档模型、目录职责与升档条件     | 判断 Lite / Standard / Heavy 时 |
| Pipeline 设计  | `phases/03_pipeline.md`      | Heavy 档阶段划分与状态管理       | 需要 Pipeline / 状态传递时      |
| 全局约束设计   | `phases/04_constraints.md`   | 约束分类、层级与表达方式         | 需要定义全局约束时              |
| 知识管理模式   | `phases/05_knowledge.md`     | SSOT、知识分层与引用策略         | 设计知识库结构时                |
| 自反哺闭环     | `phases/06_feedback-loop.md` | 纠错记录与经验固化机制           | 需要建立反馈闭环时              |
| 多实例并行     | `phases/07_team-mode.md`     | Team 模式与协作协议              | 需要多 Agent 并行时             |
| 质量保障       | `phases/08_quality.md`       | 质量原则、审计策略与护栏         | 需要建立自检与审计时            |
| 结构化语言 DSL | `phases/09_dsl.md`           | Frontmatter、规则 DSL 与表头规范 | 设计文档写法时必读              |
| 性能与披露策略 | `phases/10_performance.md`   | Token 预算、渐进式披露、信息密度 | 需要压缩入口上下文时必读        |

## 查阅路径（推荐）

渐进式披露，避免一次性加载过多内容：

1. **SKILL.md** — 了解总览、核心原则、选择档位
2. **phases/01 + 02 + 09 + 10** — 必读章节（设计哲学、结构规范、DSL、性能策略）
3. **phases/03~08** — 按需深入具体主题
4. **assets/** — 实操时使用模板和清单

## 适用场景

- 🆕 从零新建一个 Skill
- 🔄 对已有 Skill 进行架构升级或质量审计
- 🔍 评审 Skill 的目录结构和内容组织是否合理

## 许可证

本项目基于 [MIT License](LICENSE) 开源。
