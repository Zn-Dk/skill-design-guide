# 09. 结构化语言 DSL 规范

> 本章定义 Skill 文档的"写作语言"——不同执行模式的 Skill，应该用什么格式和结构来表达规则。

## 核心原则

> ⚠️ **确定性优先**：能用 grep 模式 / 代码模板 / 脚本做的，绝不交给 LLM 自然语言理解。

自然语言理解是 LLM 最不稳定的能力。Skill 文档的目标是**最大化 Agent 执行的确定性**，而非"让 Agent 理解"。

---

## 1. Frontmatter 规范

所有 Skill 的 `SKILL.md` 必须包含 YAML frontmatter：

```yaml
---
name: string              # 必须，kebab-case，如 "o3-code-review"
description: |            # 必须，多行描述，首行为一句话摘要
  一句话摘要。
  详细说明适用场景和核心能力。
allowed-tools:            # 必须，声明需要的工具权限
  - Read
  - Grep
  - Edit
  - Terminal
trigger-keywords:         # 可选，触发此 Skill 的关键词
  - review
  - 评审
execution-mode: enum      # 可选，guide | review | generate | pipeline
tier: enum                # 可选，lite | standard | heavy
---
```

### 字段说明

| 字段 | 必须 | 说明 |
|------|:---:|------|
| `name` | ✅ | 唯一标识，kebab-case |
| `description` | ✅ | Agent 判断是否加载此 Skill 的依据 |
| `allowed-tools` | ✅ | 约束 Agent 在此 Skill 下可用的工具集 |
| `trigger-keywords` | ❌ | 辅助自动匹配 |
| `execution-mode` | ❌ | 声明执行模式，帮助 Agent 选择交互策略 |
| `tier` | ❌ | 声明档位，帮助 Agent 预估复杂度 |

---

## 2. 不同执行模式的文档语言

### 2.1 纯指引型（Guide）

**适用**：行为准则、编码规范、样式指南

**文档语言**：自然语言规则 + 正/反例对比

```markdown
## 规则：函数命名

**原则**：动词开头，描述行为而非实现

✅ 正确：
- `getUserById(id)` — 描述行为
- `validateEmail(input)` — 描述意图

❌ 错误：
- `data(id)` — 含义模糊
- `queryDatabaseForUser(id)` — 暴露实现
```

**特征**：
- 每条规则独立成段
- 必须有正/反例
- 无需 grep 模式（规则太抽象，无法用正则匹配）

---

### 2.2 审查型（Review）

**适用**：Code Review、Lint 规则、安全审计

**文档语言**：grep 模式 + 判定规则 + 修复模板

```markdown
## 规则：禁止手写 URL 验证

- **检测模式**：`/https?:\/\/|new URL\(|\.match\(.*url/i`
- **排除路径**：`src/utils/validate.ts`
- **判定**：匹配且不在排除路径 → 违规
- **修复模板**：
  ```typescript
  import { isUrl } from '@/utils/validate'
  // 替换手写逻辑为：
  if (isUrl(value)) { ... }
  ```
- **严重级别**：warning
```

**特征**：
- 每条规则必须有可执行的检测模式
- 判定逻辑是确定性的（匹配/不匹配）
- 修复模板可直接应用
- Agent 不需要"理解"规则，只需执行模式匹配

---

### 2.3 生成型（Generate）

**适用**：Spec 生成、代码脚手架、文档模板

**文档语言**：完整模板 + 占位符 + 决策树

```markdown
## 模板：Vue 组件文件

\```vue
<template>
  <div class="{{kebab-case(name)}}">
    <!-- {{SLOT:主体内容}} -->
  </div>
</template>

<script setup lang="ts">
// {{SLOT:imports}}

// {{SLOT:props定义}}
interface Props {
  {{SLOT:props列表}}
}
const props = defineProps<Props>()

// {{SLOT:业务逻辑}}
</script>
\```

## 决策树：选择组件模式

- 是否需要状态管理？
  - 是 → 使用 Pinia store 模式
  - 否 → 是否需要跨组件通信？
    - 是 → 使用 provide/inject 模式
    - 否 → 使用纯 props/emit 模式
```

**特征**：
- 模板是完整可用的，Agent 只需填充占位符
- 决策树帮助 Agent 选择正确的模板变体
- 占位符用 `{{SLOT:描述}}` 标记

---

### 2.4 Pipeline 型

**适用**：数据分析、故障诊断、自动化报告

**文档语言**：阶段指引 + Schema + 脚本调用

```markdown
## Phase 2: 数据采集

### 输入
- `state.json` 中的 `targets` 数组

### 执行
1. 调用脚本：`scripts/collect_data.py --targets {{targets}}`
2. 验证输出 Schema：`schemas/collected_data.json`
3. 更新 state：`state.phase = "analysis"`

### 输出 Schema
\```json
{
  "type": "object",
  "required": ["data", "metadata"],
  "properties": {
    "data": { "type": "array" },
    "metadata": { "type": "object" }
  }
}
\```

### 失败处理
- 脚本返回非零 → 记录错误到 state.errors，跳转 Phase 5（报告）
```

**特征**：
- 每个阶段有明确的输入/输出 Schema
- 脚本调用是确定性的
- 失败处理路径预定义
- Agent 的角色是"编排者"而非"执行者"

---

## 3. 深度标记 DSL

用于控制渐进式披露，告诉 Agent 什么时候该停止、什么时候该深入。

### 标记语法

```html
<!-- depth: summary -->
一句话概述，Agent 扫描索引时读这个

<!-- depth: detail -->
详细规则，Agent 需要执行具体检查时读这个

<!-- depth: reference -->
完整参考资料，Agent 遇到边界 case 时才读这个
```

### 使用规则

| 深度 | 何时读取 | Token 预算 |
|------|---------|-----------|
| `summary` | 始终读取（在 SKILL.md 中） | < 50 token/条 |
| `detail` | Agent 确认需要执行该规则时 | < 200 token/条 |
| `reference` | 遇到边界 case 或歧义时 | 不限 |

### 示例

```markdown
<!-- depth: summary -->
## 命名规范
组件用 PascalCase，工具函数用 camelCase，常量用 UPPER_SNAKE_CASE。

<!-- depth: detail -->
### 详细规则
- Vue 组件文件名：PascalCase，如 `UserProfile.vue`
- 组合式函数：`use` 前缀 + camelCase，如 `useUserStore`
- 类型定义：PascalCase + 后缀，如 `UserProfileProps`
- 枚举值：UPPER_SNAKE_CASE，如 `USER_STATUS.ACTIVE`

<!-- depth: reference -->
### 边界 case
- HOC 包装组件：`with` 前缀，如 `withAuth(Component)`
- 动态组件注册名：kebab-case，如 `<component :is="'user-profile'" />`
- 第三方库 re-export：保持原始命名
```

---

## 4. 规则编写检查清单

编写 Skill 规则时，逐条检查：

- [ ] 这条规则能否用 grep 模式检测？如果能 → 必须提供模式
- [ ] 这条规则的判定是否确定性的？如果是 → 用判定表而非自然语言
- [ ] 这条规则的修复是否模板化的？如果是 → 提供修复模板
- [ ] 这条规则是否有正/反例？→ 必须有
- [ ] 这条规则的深度标记是否正确？→ summary 不超过 50 token
