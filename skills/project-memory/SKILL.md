---
name: project-memory
description: Maintain long-term project stability through structured memory of requirements, architecture, tests, and decisions. ALWAYS use this skill at the start of any coding session that touches an existing codebase, before reading or modifying any code file. Triggers include any mention of project maintenance, code architecture, regression bugs, requirement tracking, technical debt, refactoring, code review, or when working on a project that has a `.project-memory/` directory (auto-detect this on every session). Also use when the user asks to "track this project", "set up project memory", "maintain this codebase", or works on a codebase that needs long-term stability across multiple coding sessions. Use this skill even if the user does not explicitly request it — if the project has `.project-memory/`, this skill applies.
---

# Project Memory Skill

A skill for keeping AI-assisted projects stable across many iterations. It maintains a synchronized set of documents — requirements, architecture, tests, decisions, dependencies — so that code, intent, and verification stay aligned over time.

---

## Core Philosophy

**Code is the source of truth. Documents are its index and gatekeeper.**

Documents do not replace code. They make code findable, intent recoverable, and changes verifiable. When document and code conflict, code wins — but the conflict itself is a signal that something failed.

---

## When This Skill Activates

**Mandatory entry actions when this skill activates:**

1. Check if `.project-memory/` exists in project root.
2. If yes: read `INDEX.md` first, then `.workflow-state` if present.
3. If no: ask the user whether to initialize. Do not auto-initialize.
4. Run quick health check (NOT full audit): scan for F1, F5, F8, F10 only.
5. Display the Health Report before doing any work.

**Never skip step 5.** This is the only mechanism that lets the user see whether the skill is working.

**Audit distinction:**
- **Quick health check** (steps 4-5): automatic, every entry, only checks F1/F5/F8/F10.
- **Full audit (Workflow D)**: manual only via `[skill-audit]`, runs all 10 checks.

---

## Project Tiers

Three tiers, set in `.project-memory/.tier`. Choose at initialization.

| Tier | For | Required files |
|---|---|---|
| **lite** | Small scripts, prototypes, < 20 files | `prd.md`, `changelog.md`, `INDEX.md` |
| **standard** | Normal projects | + `architecture.xml`, `test-cases.xml`, `decisions.md` |
| **strict** | Long-term maintained, team | + `dependencies.xml`, `debt.md`, file headers, scheduled audit |

Tier can be upgraded (lite → standard → strict), never downgraded without `--force`.

---

## File Structure

```
.project-memory/
├── INDEX.md                ← Agent entry point. 60-second project overview.
├── .tier                   ← lite | standard | strict
├── .skill-version          ← Skill version used by this project
├── .workflow-state         ← Active workflow tracker (auto-managed)
├── prd.md                  ← Active requirements (Markdown + inline XML tags)
├── architecture.xml        ← Module relationships (standard+)
├── test-cases.xml          ← Test cases linked to acceptance criteria (standard+)
├── decisions.md            ← ADR log, append-only (standard+)
├── dependencies.xml        ← External dependencies (strict)
├── changelog.md            ← Functional changes, not commits
├── debt.md                 ← Hotfix debts (strict)
├── feedback.md             ← User pain points for skill improvement
└── archive/
    ├── prd-archived.md
    ├── decisions-archived.md
    ├── changelog-{YYYY}Q{N}.md
    └── test-cases-archived.xml
```

For file format details, read `references/file-formats.md` when needed.

**Generated reports** (not in the structure above, created on demand, not committed to Git):
- `current-state-report.md` — produced by Workflow C phase 1.
- `migration-report.md` — produced by a tier upgrade.
- `inconsistencies.md` — produced by Workflow D audit.

These are transient outputs for the user to read, then delete or ignore.

---

## Writing Style Contract

**This is the most important rule in this skill. Document bloat kills the system.**

**Scope:** This contract applies to files inside `.project-memory/` and to code file headers. It does NOT apply to SKILL.md or reference docs, which need to be explanatory.

Every sentence in any memory file must satisfy one of:

1. Contain a fact (ID, date, commit hash, status, number).
2. Contain a judgment that remains useful in the future (why A not B, when to revisit).

**Sentences satisfying neither must be deleted.** Examples of forbidden content:

- "to improve user experience" — empty
- "this is an important feature" — vacuous
- "after careful consideration" — narrative
- "as mentioned above" — redundant connector

**Hard limits per entry:**

| File | Limit per entry | Required fields |
|---|---|---|
| `prd.md` requirement | 200 chars | id, intent, acceptance-criteria, risk-points |
| `architecture.xml` module | 50 chars | name, purpose, depends-on, used-by |
| `decisions.md` ADR | 150 chars | context, options, decision, irreversibility |
| `changelog.md` entry | 100 chars | date, type, affected-requirements, commit |
| `dependencies.xml` dep | 30 chars | name, version, used-for |
| Code file header | 10 lines | purpose, key-methods |

**Rule of one source:**
- Same fact never appears in two files. Use ID references instead.

**Anti-bloat merge rule:**
- Same requirement modified within 1 day → merge changelog entries into one, update timestamp to the latest.
- Same requirement modified within 7 days → collapse older entries; only the latest stays in the main view.
- Functions repeatedly changed during debugging should overwrite, not accumulate, in the same requirement block.

**Last step of every update:**
- Ask: "Did this update make any field redundant? If yes, delete it."

---

## Acceptance Criteria as a Contract

The link between requirements and tests is the weak point of any such system. Structural links (a `linked-tests` field) do not guarantee that tests actually verify the requirement.

**Mechanism:** Every requirement must have explicit `<acceptance-criteria>`, written in plain language, each with its own ID. Every test case must declare which criterion it covers via a `covers` attribute. Audit verifies that every criterion is covered by at least one test, and that the test description contains keywords from the criterion.

This is a human-readable, human-auditable contract. Neither the agent nor the user can fake it without it being visible.

---

## Workflows

Six atomic workflows. Each is an ordered checklist. Skipping steps is forbidden.

| ID | Name | When |
|---|---|---|
| A | New Requirement | Adding new feature |
| B | Bug Fix | Fixing existing requirement |
| C | Project Onboarding | First time on existing codebase |
| D | Audit | Manual trigger, optional periodic |
| E | Deprecation | Removing feature |
| F | Hotfix | Production emergency |

**For workflow details, read `references/workflows.md` before executing any workflow.**

After every workflow step, update `.workflow-state` so interruptions can be recovered.

**When to output `[skill-checklist]`:**
- At the end of every workflow execution (A/B/C/D/E/F).
- NOT in regular conversation, code review, or read-only operations.
- One checklist per workflow run, even if many files changed.

---

## Failure Modes

Ten known failure modes. When detected, follow the recovery procedure.

| ID | Failure | Detection |
|---|---|---|
| F1 | Workflow interrupted | `.workflow-state` shows incomplete |
| F2 | Document inconsistency | Audit cross-validation |
| F3 | Contradicting ADRs | New ADR conflicts but lacks `supersedes` |
| F4 | Circular reference | Cycle detection in reference graph |
| F5 | Agent skipped workflow | git commit newer than changelog by >1h; or missing `[skill-checklist]` |
| F6 | Tests pass but requirement fails | Criterion not semantically covered by any test |
| F7 | Concurrent modification | Another session's `last_activity` < 30min on same requirement |
| F8 | Hotfix debt accumulation | `debt.md` entries past deadline |
| F9 | Archive removed referenced item | Active item references archived item |
| F10 | Skill version mismatch | `.skill-version` differs from current skill |

**Truth priority when documents conflict:**

```
Code > test-cases.xml > prd.md > architecture.xml > changelog.md
```

**For full failure mode handling, read `references/failure-modes.md`.**

---

## Observability

**Health Report — displayed on every project entry. Non-negotiable.**

Format:
```
📊 Project Memory Health Report
================================
Tier: {tier} | Skill version: {version} | Score: {0-100}

✅ Healthy
  • {N} active requirements, all with acceptance-criteria
  • {N} test cases, {X}% criteria covered
  • Last audit: {N} days ago

⚠️ Attention
  • {N} hotfix debts pending (earliest deadline: {time})
  • {N} criteria without tests

🔴 Critical
  • {if any}

Recent activity:
  • {date} {summary}
```

**Health score calculation:**

```
Score = completeness × 30%
      + test-coverage × 30%
      + freshness × 15%
      + (100 - debt-penalty) × 15%
      + consistency × 10%
```

For the **lite** tier (no test-cases.xml), the test-coverage 30% is redistributed: completeness becomes 50%, freshness 25%, consistency 25%. Debt does not apply to lite.

If score < 70, strongly suggest maintenance before new work.

**For metric definitions, read `references/observability.md`.**

---

## Manual Commands

Only five commands. All other operations use natural language with the agent.

| Command | Action |
|---|---|
| `[skill-init]` | Initialize `.project-memory/` (asks tier first) |
| `[skill-audit]` | Run workflow D fully |
| `[skill-status]` | Show health report |
| `[skill-upgrade {target}]` | Upgrade tier or skill (`tier:standard` / `skill`) |
| `[skill-feedback {text}]` | Append to `feedback.md` |

When user message starts with `[skill-`, execute the command directly. Do not engage in conversational preamble.

---

## Evolution

**Project tier upgrade:**

```
Trigger: user runs [skill-upgrade tier:standard]
1. Backup .project-memory/ → .project-memory-backup-{timestamp}/
2. Scan existing files, identify missing fields per target tier
3. Generate scaffolding for new files
4. Mark fields needing human review as <needs-migration>
5. Write migration-report.md
6. Update .tier
```

**Skill self-upgrade:**

```
Trigger: every project entry compares .skill-version to current
If skill is newer:
1. Read skill's MIGRATIONS.md
2. Find migration steps from project version to current
3. Prompt user: "Skill updated to X.Y.Z. Apply migration?"
4. On confirmation, run migration steps
5. Update .skill-version
```

**Cross-project learning:**

Users record pain points via `[skill-feedback]`. Skill maintainers review `feedback.md` files across projects to evolve the skill itself.

---

## Git Integration

`.project-memory/` should be committed to Git. It is part of the project, like source code.

**Recommended `.gitignore` entries inside `.project-memory/`:**

```
.workflow-state            # Per-session state, not for sharing
.project-memory-backup-*/  # Temporary backups from migrations
*-report.md                # current-state-report, migration-report
inconsistencies.md         # Transient audit output
```

**Keep in Git:**
- All `.md` and `.xml` files EXCEPT the transient reports listed in `.gitignore` above.
- `.tier`, `.skill-version` (track per project)
- `archive/` directory

**Why:** Documents must move with code. Cloning the repo without `.project-memory/` defeats the purpose.

---

## Boundaries

**This skill does NOT:**

- Enforce code style — that's lint/formatter's job.
- Run or analyze tests automatically — Claude Code handles execution.
- Replace Git — version control is Git's job.
- Block commits or modifications — this is a soft contract, not CI/CD.
- Guarantee requirement correctness — only consistency.

**Known limitations:**

- Cannot physically prevent agents from skipping workflows.
- Multi-agent locking is advisory only.
- Document bloat must be controlled by the style contract; the skill cannot auto-detect bad prose.
- Semantic test coverage is keyword-based, not understanding-based.

---

## User Override

If the user explicitly says "skip the skill", "just change the code", or otherwise asks to bypass the workflow, the agent should:

1. Make the change as requested.
2. At the end, briefly note: "Skill workflow skipped. To stay consistent, consider running `[skill-status]` or recording a changelog entry later."
3. Do NOT refuse, lecture, or repeatedly suggest the workflow. Soft contract means soft.

This skill respects user authority. The user, not the skill, owns the project.

---

## Quick Reference

**Starting a session:**
1. Detect `.project-memory/` → read `INDEX.md`.
2. Display health report.
3. Check `.workflow-state` for unfinished work.
4. Wait for user task.

**Making a code change:**
1. Identify workflow (A/B/E/F).
2. Read relevant memory files.
3. Follow workflow steps in order.
4. Update `.workflow-state` per step.
5. Output `[skill-checklist]` at end.

**Detecting trouble:**
- Inconsistency → `[skill-audit]`.
- Workflow stuck → check `.workflow-state`.
- Documents bloating → review style contract.

---

For deeper details, read these references on demand:

- `references/file-formats.md` — Full file schemas with examples.
- `references/workflows.md` — All six workflows step-by-step.
- `references/failure-modes.md` — All ten failure modes with recovery.
- `references/observability.md` — Health metric definitions.
- `references/style-examples.md` — Good vs bad documentation examples.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# Project Memory Skill（项目记忆 Skill）

一个用于在多轮 AI 辅助开发中保持项目稳定的 skill。它维护一组同步的文档——需求、架构、测试、决策、依赖——确保代码、意图、验证三者长期对齐。

---

## 核心哲学

**代码是事实来源。文档是它的索引和守门员。**

文档不替代代码。文档让代码可被检索、意图可被回溯、变更可被验证。当文档与代码冲突时，代码胜出——但冲突本身就是一个失败信号。

---

## 触发时机

**触发时必须执行的入场动作：**

1. 检查项目根目录是否存在 `.project-memory/`。
2. 如果存在：先读 `INDEX.md`，然后读 `.workflow-state`（如果存在）。
3. 如果不存在：询问用户是否初始化。不要自动初始化。
4. 运行快速健康检查（不是完整审计）：仅扫描 F1、F5、F8、F10。
5. 在做任何工作前展示健康报告。

**绝不跳过步骤5。** 这是用户能看到 skill 是否在工作的唯一机制。

**审计区分：**
- **快速健康检查**（步骤4-5）：自动，每次进入，只查 F1/F5/F8/F10。
- **完整审计（工作流D）**：仅手动 `[skill-audit]` 触发，运行全部10项检查。

---

## 项目档位

三个档位，记录在 `.project-memory/.tier` 中。初始化时选择。

| 档位 | 适用 | 必需文件 |
|---|---|---|
| **lite** | 小脚本、原型、< 20文件 | `prd.md`、`changelog.md`、`INDEX.md` |
| **standard** | 正常项目 | + `architecture.xml`、`test-cases.xml`、`decisions.md` |
| **strict** | 长期维护、团队项目 | + `dependencies.xml`、`debt.md`、文件头、定期审计 |

档位只能升级（lite → standard → strict），不能降级（除非加 `--force`）。

---

## 文件结构

```
.project-memory/
├── INDEX.md                ← Agent 入口。60秒项目概览。
├── .tier                   ← lite | standard | strict
├── .skill-version          ← 本项目使用的 skill 版本
├── .workflow-state         ← 活跃工作流追踪（自动管理）
├── prd.md                  ← 活跃需求（Markdown + 内嵌 XML 标签）
├── architecture.xml        ← 模块关系（standard+）
├── test-cases.xml          ← 关联到验收标准的测试用例（standard+）
├── decisions.md            ← ADR 日志，只追加（standard+）
├── dependencies.xml        ← 外部依赖（strict）
├── changelog.md            ← 功能变更，不是 commit
├── debt.md                 ← 紧急修复债务（strict）
├── feedback.md             ← 用户痛点，用于改进 skill
└── archive/
    ├── prd-archived.md
    ├── decisions-archived.md
    ├── changelog-{YYYY}Q{N}.md
    └── test-cases-archived.xml
```

文件格式细节，需要时读 `references/file-formats.md`。

**生成的报告**（不在上面的结构图中，按需创建，不提交 Git）：
- `current-state-report.md` — 由工作流 C 阶段1产出。
- `migration-report.md` — 由档位升级产出。
- `inconsistencies.md` — 由工作流 D 审计产出。

这些是给用户读的临时产出，读完即可删除或忽略。

---

## 文档风格契约

**这是 skill 中最重要的规则。文档膨胀会毁掉整个系统。**

**适用范围：** 本契约适用于 `.project-memory/` 中的文件和代码文件头部。不适用于 SKILL.md 和 reference 文档——这些需要讲解性内容。

memory 文件中的每一句话必须满足以下之一：

1. 包含一个事实（ID、日期、commit hash、状态、数字）。
2. 包含一个未来仍然有用的判断（为什么选A不选B、何时重新审视）。

**不满足任一条件的句子必须删除。** 禁止内容举例：

- "为了更好地提升用户体验" — 空话
- "这是一个重要的功能" — 废话
- "经过深思熟虑" — 叙事
- "如上所述" — 冗余连接

**每条上限：**

| 文件 | 每条上限 | 必填字段 |
|---|---|---|
| `prd.md` 需求 | 200字符 | id、intent、acceptance-criteria、risk-points |
| `architecture.xml` 模块 | 50字符 | name、purpose、depends-on、used-by |
| `decisions.md` ADR | 150字符 | context、options、decision、irreversibility |
| `changelog.md` 条目 | 100字符 | date、type、affected-requirements、commit |
| `dependencies.xml` 依赖 | 30字符 | name、version、used-for |
| 代码文件头 | 10行 | purpose、key-methods |

**信息只在一处：**
- 同一事实不允许在两个文件出现。用 ID 交叉引用。

**反膨胀合并规则：**
- 同一需求一天内多次修改 → 合并 changelog 条目为一条，时间戳更新为最后一次。
- 同一需求7天内多次修改 → 折叠较旧条目；主视图只保留最新。
- 调试中反复修改的功能，应在同一需求块内覆盖，不累积。

**每次更新的最后一步：**
- 自问："本次更新有没有让任何字段变成冗余？有就删。"

---

## 验收标准即契约

需求与测试之间的关联是这类系统的薄弱环节。结构性关联（一个 `linked-tests` 字段）不能保证测试真的验证了需求。

**机制：** 每个需求必须有显式的 `<acceptance-criteria>`，用平实语言书写，每条有自己的 ID。每个测试用例必须通过 `covers` 属性声明它覆盖哪条标准。审计验证每条标准至少被一个测试覆盖，且测试描述包含该标准的关键词。

这是一个人类可读、人类可审查的契约。Agent 和用户都无法在不被看见的情况下糊弄它。

---

## 工作流

六个原子工作流。每个是有序清单。禁止跳步。

| ID | 名称 | 何时使用 |
|---|---|---|
| A | 新增需求 | 添加新功能 |
| B | 修复 Bug | 修复现有需求 |
| C | 接手项目 | 第一次接触现有代码库 |
| D | 审计 | 手动触发，可选定期 |
| E | 废弃 | 移除功能 |
| F | 紧急修复 | 生产环境紧急情况 |

**执行任何工作流前，先读 `references/workflows.md`。**

每完成一步，更新 `.workflow-state`，以便中断后恢复。

**何时输出 `[skill-checklist]`：**
- 每个工作流执行结束时（A/B/C/D/E/F）。
- 普通对话、代码审查、只读操作时不输出。
- 一次工作流运行一个 checklist，即便修改了多个文件。

---

## 失败模式

十种已知失败模式。检测到时按恢复流程处理。

| ID | 失败 | 检测 |
|---|---|---|
| F1 | 工作流中断 | `.workflow-state` 显示未完成 |
| F2 | 文档不一致 | 审计交叉验证 |
| F3 | ADR 矛盾 | 新 ADR 冲突但缺 `supersedes` |
| F4 | 循环引用 | 引用图环检测 |
| F5 | Agent 跳步 | git commit 比 changelog 新超过1小时；或缺少 `[skill-checklist]` |
| F6 | 测试过但需求未实现 | 标准未被任何测试语义覆盖 |
| F7 | 并发修改 | 其他会话30分钟内活动过同一需求 |
| F8 | 紧急修复债务累积 | `debt.md` 条目逾期 |
| F9 | 归档误删 | 活跃项引用了已归档项 |
| F10 | skill 版本不匹配 | `.skill-version` 与当前 skill 不同 |

**文档冲突时的可信度优先级：**

```
代码 > test-cases.xml > prd.md > architecture.xml > changelog.md
```

**完整失败模式处理，读 `references/failure-modes.md`。**

---

## 可观测性

**健康报告——每次进入项目时展示，不可省略。**

格式：
```
📊 Project Memory Health Report
================================
Tier: {档位} | Skill version: {版本} | Score: {0-100}

✅ Healthy 健康
  • {N} 个活跃需求，全部有 acceptance-criteria
  • {N} 个测试用例，{X}% 标准被覆盖
  • 上次审计：{N} 天前

⚠️ Attention 注意
  • {N} 个紧急修复债务待补（最早截止：{时间}）
  • {N} 条标准没有测试

🔴 Critical 严重
  • {如有}

Recent activity 最近活动:
  • {日期} {摘要}
```

**健康度计算：**

```
Score = completeness × 30%
      + test-coverage × 30%
      + freshness × 15%
      + (100 - debt-penalty) × 15%
      + consistency × 10%
```

对于 **lite** 档位（无 test-cases.xml），test-coverage 的 30% 重新分配：completeness 变为 50%，freshness 25%，consistency 25%。债务不适用于 lite。

分数 < 70 时，强烈建议先做维护再做新功能。

**指标定义详见 `references/observability.md`。**

---

## 手动命令

仅五个命令。其他操作通过自然语言与 agent 交互即可。

| 命令 | 作用 |
|---|---|
| `[skill-init]` | 初始化 `.project-memory/`（先问档位） |
| `[skill-audit]` | 完整运行工作流D |
| `[skill-status]` | 显示健康报告 |
| `[skill-upgrade {target}]` | 升级档位或 skill（`tier:standard` / `skill`） |
| `[skill-feedback {text}]` | 追加到 `feedback.md` |

当用户消息以 `[skill-` 开头时，直接执行命令。不要进入对话式开场。

---

## 演化机制

**项目档位升级：**

```
触发：用户运行 [skill-upgrade tier:standard]
1. 备份 .project-memory/ → .project-memory-backup-{timestamp}/
2. 扫描已有文件，识别目标档位缺失的字段
3. 为新文件生成骨架
4. 将需要人工审核的字段标记为 <needs-migration>
5. 写 migration-report.md
6. 更新 .tier
```

**Skill 自身升级：**

```
触发：每次进入项目时比较 .skill-version 与当前版本
如果 skill 更新了：
1. 读 skill 的 MIGRATIONS.md
2. 找到从项目版本到当前版本的迁移步骤
3. 提示用户："Skill 已更新到 X.Y.Z。是否应用迁移？"
4. 确认后运行迁移步骤
5. 更新 .skill-version
```

**跨项目知识沉淀：**

用户通过 `[skill-feedback]` 记录痛点。Skill 维护者通过审阅多个项目的 `feedback.md` 来演化 skill 本身。

---

## Git 集成

`.project-memory/` 应该提交到 Git。它是项目的一部分，类似源代码。

**`.project-memory/` 内推荐的 `.gitignore` 条目：**

```
.workflow-state            # 单会话状态，不用于共享
.project-memory-backup-*/  # 迁移产生的临时备份
*-report.md                # current-state-report、migration-report
inconsistencies.md         # 临时审计产出
```

**进入 Git：**
- 所有 `.md` 和 `.xml` 文件，**除了**上面 `.gitignore` 中列出的临时报告。
- `.tier`、`.skill-version`（按项目追踪）
- `archive/` 目录

**为什么：** 文档必须随代码一起走。克隆仓库时若缺少 `.project-memory/`，整套机制失效。

---

## 边界

**这个 skill 不做：**

- 不强制代码风格——那是 lint 的事。
- 不自动跑测试——Claude Code 处理执行。
- 不替代 Git——版本控制是 Git 的事。
- 不阻止 commit——这是软约束不是 CI/CD。
- 不保证需求本身正确——只保证一致性。

**已知限制：**

- 无法物理阻止 Agent 跳步。
- 多 Agent 并发是建议性约束。
- 文档膨胀靠风格契约控制，skill 无法自动检测劣质文字。
- 语义测试覆盖基于关键词，不基于理解。

---

## 用户绕过

如果用户明确说"跳过 skill"、"直接改代码"或要求绕过工作流，agent 应该：

1. 按要求修改。
2. 结尾简短提示："已跳过 skill 工作流。如需保持一致性，建议稍后运行 `[skill-status]` 或补充 changelog 条目。"
3. **不要**拒绝、说教、重复建议。软约束就是软的。

skill 尊重用户的权威。项目归用户所有，不归 skill 所有。

---

## 快速参考

**开始会话：**
1. 检测 `.project-memory/` → 读 `INDEX.md`。
2. 展示健康报告。
3. 检查 `.workflow-state` 是否有未完成工作。
4. 等待用户任务。

**修改代码：**
1. 识别工作流（A/B/E/F）。
2. 读相关 memory 文件。
3. 按顺序执行工作流步骤。
4. 每步更新 `.workflow-state`。
5. 结尾输出 `[skill-checklist]`。

**检测问题：**
- 不一致 → `[skill-audit]`。
- 工作流卡住 → 检查 `.workflow-state`。
- 文档膨胀 → 复查风格契约。

---

更多细节，按需阅读：

- `references/file-formats.md` — 完整文件格式与示例。
- `references/workflows.md` — 六个工作流的完整步骤。
- `references/failure-modes.md` — 十种失败模式与恢复。
- `references/observability.md` — 健康指标定义。
- `references/style-examples.md` — 文档好例与坏例。
