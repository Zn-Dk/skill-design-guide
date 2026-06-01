# 新建 Skill 自检清单

> 新建或迭代 Skill 时，逐项检查以下清单。每项标注了适用档位：
> - 🟢 **All** = 所有档位必须满足
> - 🔵 **Standard+** = Standard 和 Heavy 档位需要
> - 🟣 **Heavy** = 仅 Heavy（Pipeline 型）需要

---

## 一、入口设计 🟢 All

- [ ] `SKILL.md` 包含完整 frontmatter（name、description、allowed-tools）
- [ ] 入口文件 ≤ 2KB（约 500 token），Agent 在 L1 可完成 80% 决策
- [ ] 明确声明 Skill 的执行模式（guide / review / generate / pipeline）
- [ ] 核心原则用 `> ⚠️` blockquote 突出

## 二、目录结构 🟢 All

- [ ] 档位选择正确（Lite 单文件 / Standard 多模块 / Heavy 完整六层）
- [ ] 无空目录——每个目录必须有实际内容才创建
- [ ] 文件命名遵循 `NN_kebab-case.md` 规范（phases 目录）

### Standard+ 补充 🔵

- [ ] `phases/` 或 `modules/` 目录按职责拆分
- [ ] `assets/` 目录存放模板和检查清单

### Heavy 补充 🟣

- [ ] `scripts/` 目录存放确定性脚本
- [ ] `schemas/` 目录存放 JSON Schema 约束
- [ ] `knowledge/` 目录存放 SSOT 知识库

## 三、文档语言 DSL 🟢 All

- [ ] 确定性操作优先：能用 grep 模式/代码模板/脚本做的，不交给 LLM 自然语言理解
- [ ] 审查型规则提供检测模式 + 判定规则 + 修复模板
- [ ] 生成型规则提供完整模板 + 占位符
- [ ] 深度标记（`<!-- depth: summary/detail/reference -->`）已添加（如适用）

## 四、性能与披露 🟢 All

- [ ] 渐进式披露：L0 frontmatter → L1 正文 → L2 子文档 → L3 参考
- [ ] 信息密度：每行文字对 Agent 决策有直接帮助，无解释性废话
- [ ] 子文档按需加载，不预加载全部内容

## 五、约束设计 🔵 Standard+

- [ ] 全局约束用 `> ⚠️` 在 SKILL.md 顶层定义
- [ ] 约束覆盖：职责边界、输出格式、质量底线
- [ ] 正/反例对比清晰（Do / Don't）

## 六、Pipeline 设计 🟣 Heavy

- [ ] 每阶段一个 phase 指引 `.md` + 对应脚本
- [ ] 阶段间单向依赖，禁止反向修改
- [ ] 每阶段可单独幂等重跑
- [ ] State schema 已定义（阶段间传递的结构化数据）
- [ ] State Merge 机制已定义

## 七、LLM 阶段保障 🟣 Heavy

- [ ] LLM 阶段有 few-shot 示例 + 输出格式模板
- [ ] 输出结构有 JSON Schema 约束
- [ ] Phase 指引中列出"必须完成的 N 个子任务"（Checklist 驱动）
- [ ] 指明需要读取的 knowledge/ 文件

## 八、知识管理 🔵 Standard+

- [ ] SSOT 原则：同一知识只在一处维护
- [ ] 静态数据与动态数据分离
- [ ] 领域规则文件有明确的消费方标注

## 九、质量保障 🔵 Standard+

- [ ] 有明确的质量验证机制（人工 review / 脚本审计 / 自测用例）
- [ ] 质量规则按严重度分级（ERROR 阻断 / WARNING 提示）

### Heavy 补充 🟣

- [ ] 审计脚本做最终校验（ERROR 级别阻断输出）
- [ ] 审计失败时有明确的回溯重跑机制

## 十、可选特性

### 自反哺机制 🔵 Standard+（推荐）

- [ ] `CORRECTIONS.md` 记录用户纠正 + 周期固化
- [ ] 固化流程：未固化记录 → 提取规律 → 更新知识库

### 多实例并行 🟣 Heavy（如适用）

- [ ] Team 模式 + 独立 output_dir
- [ ] 工作流无状态，可被多个 sub-agent 并发使用
- [ ] 主 Agent 只做编排和审计

### 安全与配置 🔵 Standard+（如适用）

- [ ] MCP/API Token 管理：环境变量 > 配置文件 > 明文
- [ ] 敏感信息不硬编码在 skill 文件中

---

## 使用方式

1. 新建 skill 时，先确定档位（Lite / Standard / Heavy）
2. 只检查对应档位及以下的项目（如 Standard 只需检查 🟢 + 🔵）
3. 逐项勾选，未满足的项标注原因（"不适用"或"待补充"）
4. 所有 ERROR 级别项必须满足后才能合入
