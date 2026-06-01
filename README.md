# Agent Skill 设计范式指南

一个用于指导 AI Agent 设计、构建和迭代高质量 Skill 的**元技能（Meta-Skill）**。

## 简介

本项目是一套完整的 Agent Skill 设计方法论，以 Skill 自身的形式呈现。它定义了：

- **目录结构规范** — 三档模型（Lite / Standard / Heavy）
- **结构化语言 DSL** — Skill 文档的写作语言规范
- **性能与披露策略** — Token 预算管理与渐进式信息加载

适用于任何需要创建、迭代或评审 Agent Skill 的场景。

## 核心设计原则

| 原则           | 说明                                       |
| -------------- | ------------------------------------------ |
| 最小必要       | 不为"完整"而创建空目录或冗余文档           |
| 确定性优先     | 能用模式/模板/脚本做的，不交给自然语言理解 |
| Token 预算意识 | 入口文件 ≤ 2KB，80% 决策在 Level 1 完成    |

## 目录结构

```
skill-design-guide/
├── SKILL.md                          # 入口文件（总览 + 索引）
├── phases/                           # 分层文档（按需加载）
│   ├── 01_philosophy.md              # 核心设计哲学
│   ├── 02_structure.md               # 目录结构规范
│   ├── 03_pipeline.md                # Pipeline 设计模式
│   ├── 04_constraints.md             # 全局约束设计
│   ├── 05_knowledge.md               # 知识管理模式
│   ├── 06_feedback-loop.md           # 自反哺闭环
│   ├── 07_team-mode.md               # 多实例并行
│   ├── 08_quality.md                 # 质量保障
│   ├── 09_dsl.md                     # 结构化语言 DSL
│   └── 10_performance.md             # 性能与披露策略
├── assets/                           # 资源文件
│   ├── new-skill-skeleton.md         # 新 Skill 脚手架模板
│   └── new-skill-checklist.md        # 新建 Skill 自检清单
├── README.md
└── LICENSE
```

## 执行模式分档

| 档位         | 执行模式  | 典型场景              | 默认结构                           |
| ------------ | --------- | --------------------- | ---------------------------------- |
| **Lite**     | 纯指引    | 行为准则、编码规范    | 单文件 `SKILL.md`                  |
| **Standard** | 审查/生成 | Code Review、设计指南 | `SKILL.md` + `phases/` + `assets/` |
| **Heavy**    | Pipeline  | 数据分析、自动化报告  | 完整结构 + `scripts/` + `schemas/` |

> 默认选择 Lite，只有当单文件无法承载时才逐级升级。

## 使用方式

### 作为 Agent Skill 加载

将本目录放置到你的 Agent Skill 目录中，Agent 会自动识别 `SKILL.md` 作为入口文件。

### 查阅路径（推荐）

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
