# 目录结构规范

按执行模式分档，最小必要原则。

---

## 核心原则

> ⚠️ **最小必要**：不要为了"完整"而创建空目录。每个目录必须有实际内容才创建。

> ⚠️ **执行模式决定结构**：目录结构由 Skill 的执行模式决定，而非复杂度。

---

## 三档模型

### Lite（轻量档）

**适用场景**：行为准则、编码规范、样式指南、最佳实践

**结构**：单文件

```
skill-name/
└── SKILL.md              # 所有内容集中在一个文件
```

**判定条件**（满足全部）：
- 无需脚本执行
- 无需分阶段工作流
- 内容 ≤ 3000 字
- 无外部资源依赖

**项目实例**：`karpathy-guidelines`、`o3-tailwind-style`

---

### Standard（标准档）

**适用场景**：Code Review、Spec 生成、设计指南、开发规范集

**结构**：入口 + 模块 + 资源

```
skill-name/
├── SKILL.md              # 入口：核心原则 + 模块索引（≤ 2KB）
├── phases/               # 或 modules/，按主题拆分的详细指引
│   ├── 01_xxx.md
│   ├── 02_xxx.md
│   └── 03_xxx.md
└── assets/               # 模板、检查清单、速查表
    ├── template.md
    └── checklist.md
```

**判定条件**（满足任一即升档）：
- 内容超过 3000 字，需要拆分
- 有多个独立主题域需要按需加载
- 需要提供模板或检查清单

**项目实例**：`o3-code-review`、`spec-writing`、`skill-design-guide`

---

### Heavy（重型档）

**适用场景**：数据分析、故障诊断、自动化报告、多阶段 Pipeline

**结构**：完整六层

```
skill-name/
├── SKILL.md              # 入口：工作流总览 + 全局约束
├── phases/               # 阶段详细指引（每阶段一个 .md）
│   ├── 01_xxx.md
│   ├── 02_xxx.md
│   └── 05_examples/     # Few-shot 示例
├── scripts/              # 确定性脚本（Python/Shell）
│   ├── state_manager.py  # 状态合并器
│   ├── collector.py      # 数据采集
│   ├── composer.py       # 数据合成
│   └── auditor.py        # 审计校验
├── schemas/              # JSON Schema 约束定义
├── knowledge/            # SSOT 知识库
├── data/                 # 静态数据（CSV/JSON/YAML）
└── .learnings/           # 自反哺日志
    ├── CORRECTIONS.md
    └── LEARNINGS.md
```

**判定条件**（满足任一即升档）：
- 需要确定性脚本执行（API 调用、数据清洗）
- 需要阶段间状态传递（state.json）
- 需要 JSON Schema 约束 LLM 输出格式
- 需要多实例并行（Team 模式）

---

## 各层职责速查

| 目录 | 职责 | 消费者 | 适用档位 |
|------|------|--------|---------|
| `SKILL.md` | 入口 + 核心原则 + 索引 | Agent 首次加载 | 全部 |
| `phases/` 或 `modules/` | 按主题/阶段拆分的详细指引 | Agent 按需加载 | Standard + Heavy |
| `assets/` | 模板、清单、速查表 | Agent 按需引用 | Standard + Heavy |
| `scripts/` | 确定性操作封装 | OS 直接运行 | Heavy |
| `schemas/` | 结构约束定义 | 脚本校验 + Agent 输出约束 | Heavy |
| `knowledge/` | 领域知识 SSOT | 脚本 + Agent 推理参考 | Heavy |
| `data/` | 静态事实数据 | 脚本直接读取 | Heavy |
| `.learnings/` | 反馈闭环存储 | 定时固化回流 | Heavy（可选） |

---

## 命名规范

| 项目 | 规范 | 示例 |
|------|------|------|
| Skill 目录名 | kebab-case | `o3-code-review` |
| 阶段文件 | 数字前缀 + 下划线 + 名称 | `01_philosophy.md` |
| 脚本文件 | snake_case | `state_manager.py` |
| 资源文件 | kebab-case 或 snake_case | `new-skill-checklist.md` |

---

## 升档决策树

```
开始
 │
 ├─ 内容 ≤ 3000 字 且 无脚本 且 无多主题？
 │   └─ 是 → Lite
 │
 ├─ 需要确定性脚本 或 阶段间状态传递？
 │   └─ 是 → Heavy
 │
 └─ 其他 → Standard
```

> 💡 **默认选择 Standard**。只有明确满足 Lite 条件时才降档，明确需要脚本/状态时才升档。
