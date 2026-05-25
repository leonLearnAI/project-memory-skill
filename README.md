# Project Memory Skill

![version](https://img.shields.io/badge/version-1.0.0-blue)
![license](https://img.shields.io/badge/license-MIT-green)
![status](https://img.shields.io/badge/status-early%20(seeking%20field%20reports)-orange)

A Claude skill for maintaining long-term project stability in AI-assisted development.

> **Status:** v1.0.0, early release. The design is complete and self-consistent, but it has not yet been hardened by large-scale real-world use. If you try it, please file a [field report](.github/ISSUE_TEMPLATE/field-report.md) — that feedback is what drives the next version.

## Quick install

**Recommended — plugin install (one command):**

```bash
claude plugin install https://github.com/leonLearnAI/project-memory-skill
```

**Alternative — manual git clone:**

```bash
# Personal (available in all projects)
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory ~/.claude/skills/project-memory

# Project-level (shared with collaborators who clone the repo)
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory .claude/skills/project-memory
```

Then start a new Claude Code session and run `[skill-init]` in your project.

## What problem does this solve?

In AI-assisted coding (vibe coding), the scarce resource is no longer code production — it's **entropy control**. Over many iterations:

- Code drifts from original intent.
- Regression bugs reappear.
- Documents go stale, misleading future AI sessions.
- Tests pass while requirements quietly break.

## What does this skill do?

It maintains a structured `.project-memory/` directory containing:

- **PRD** — Active requirements with acceptance criteria and risk points.
- **Architecture** — Module dependency graph.
- **Test cases** — Linked to acceptance criteria via a `covers` attribute.
- **Decisions (ADR)** — Why we chose A over B.
- **Changelog** — Functional changes (not git log).
- **Dependencies** — External lib tracking (strict tier).
- **Debt log** — Hotfix follow-ups (strict tier).

And enforces six atomic workflows: A (New Requirement), B (Bug Fix), C (Project Onboarding), D (Audit), E (Deprecation), F (Hotfix).

## What this skill does NOT do

- Replace Git.
- Run tests.
- Enforce code style.
- Physically block bad commits.
- Guarantee requirement correctness.

This is a **soft contract** — it works only when followed.

## Three Tiers

| Tier | For | Files required |
|---|---|---|
| **lite** | Small scripts, prototypes | prd.md, changelog.md, INDEX.md |
| **standard** | Normal projects | + architecture.xml, test-cases.xml, decisions.md |
| **strict** | Long-term maintained, team | + dependencies.xml, debt.md, file headers |

## Five Manual Commands

| Command | Action |
|---|---|
| `[skill-init]` | Initialize project memory |
| `[skill-audit]` | Run audit workflow |
| `[skill-status]` | Show health report |
| `[skill-upgrade {target}]` | Upgrade tier or skill |
| `[skill-feedback {text}]` | Record pain point |

All other operations: just ask the agent in natural language.

## Installation

**Plugin install (recommended):**

```bash
claude plugin install https://github.com/leonLearnAI/project-memory-skill
```

**Manual install:**

```bash
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory ~/.claude/skills/project-memory
```

Then in a project, run `[skill-init]` and choose your tier when prompted.

## File Structure

```
project-memory-skill/
├── .claude-plugin/
│   └── plugin.json             ← Plugin manifest
├── skills/
│   └── project-memory/
│       ├── SKILL.md            ← Main skill definition
│       └── references/
│           ├── file-formats.md     ← Detailed file schemas
│           ├── workflows.md        ← Six workflows step-by-step
│           ├── failure-modes.md    ← 10 failure modes & recovery
│           ├── observability.md    ← Health metrics
│           └── style-examples.md   ← Good vs bad documentation
├── .github/
│   └── ISSUE_TEMPLATE/
│       ├── field-report.md     ← Real-world usage report template
│       └── bug_report.md       ← Bug report template
├── MIGRATIONS.md               ← Version migration steps
└── README.md                   ← This file
```

## Design Philosophy

**Code is the source of truth. Documents are its index and gatekeeper.**

Documents do not replace code — they make code findable, intent recoverable, and changes verifiable.

## Known Limitations

1. **Soft contract** — cannot physically prevent agents from skipping workflows.
2. **Semantic test coverage** — only structural; behavioral coverage needs coverage tools.
3. **Multi-agent locking** — advisory only, Git is final arbiter.
4. **Document bloat** — relies on the style contract; the skill cannot auto-detect bad prose.

## Version

1.0.0 — Initial release.

## Feedback

Use `[skill-feedback {your pain point}]` inside any project to record issues. Maintainers will collect feedback across projects to evolve the skill.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# Project Memory Skill（项目记忆 Skill）

一个用于 AI 辅助开发中保持项目长期稳定性的 Claude skill。

> **状态：** v1.0.0，早期版本。设计已完整且自洽，但尚未经过大规模真实使用的打磨。如果你试用了，请提交一份[实地报告](.github/ISSUE_TEMPLATE/field-report.md)——这些反馈是驱动下一版的关键。

## 快速安装

**推荐 — plugin 一键安装：**

```bash
claude plugin install https://github.com/leonLearnAI/project-memory-skill
```

**备选 — 手动 git clone：**

```bash
# 个人级（所有项目可用）
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory ~/.claude/skills/project-memory

# 项目级（与克隆仓库的协作者共享）
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory .claude/skills/project-memory
```

然后开一个新的 Claude Code 会话，在项目里运行 `[skill-init]`。

## 解决什么问题？

在 AI 辅助 coding（vibe coding）时代，稀缺的不再是代码产出能力，而是**熵增控制能力**。多轮迭代中：

- 代码偏离原意图。
- 回归 bug 反复出现。
- 文档过期，误导未来的 AI 会话。
- 测试通过但需求悄悄失效。

## 这个 skill 做什么？

它维护一个结构化的 `.project-memory/` 目录，包含：

- **PRD** — 带验收标准和风险点的活跃需求。
- **架构** — 模块依赖图。
- **测试用例** — 通过 `covers` 属性关联到验收标准。
- **决策（ADR）** — 为什么选 A 而不是 B。
- **变更日志** — 功能变更（不是 git log）。
- **依赖** — 外部库追踪（strict 档位）。
- **债务日志** — 紧急修复的后续（strict 档位）。

并执行六个原子工作流：A（新增需求）、B（修复 Bug）、C（接手项目）、D（审计）、E（废弃）、F（紧急修复）。

## 这个 skill 不做什么

- 不替代 Git。
- 不跑测试。
- 不强制代码风格。
- 不物理阻止 bad commit。
- 不保证需求本身正确。

这是**软约束**——只在被遵守时起作用。

## 三档

| 档位 | 适用 | 必需文件 |
|---|---|---|
| **lite** | 小脚本、原型 | prd.md、changelog.md、INDEX.md |
| **standard** | 正常项目 | + architecture.xml、test-cases.xml、decisions.md |
| **strict** | 长期维护、团队 | + dependencies.xml、debt.md、文件头 |

## 五个手动命令

| 命令 | 作用 |
|---|---|
| `[skill-init]` | 初始化项目记忆 |
| `[skill-audit]` | 运行审计工作流 |
| `[skill-status]` | 显示健康报告 |
| `[skill-upgrade {target}]` | 升级档位或 skill |
| `[skill-feedback {text}]` | 记录痛点 |

其他操作：直接用自然语言问 agent。

## 安装

**Plugin 安装（推荐）：**

```bash
claude plugin install https://github.com/leonLearnAI/project-memory-skill
```

**手动安装：**

```bash
git clone https://github.com/leonLearnAI/project-memory-skill.git /tmp/project-memory-skill
cp -r /tmp/project-memory-skill/skills/project-memory ~/.claude/skills/project-memory
```

然后在项目中运行 `[skill-init]`，提示时选择档位。

## 文件结构

```
project-memory-skill/
├── .claude-plugin/
│   └── plugin.json             ← Plugin 清单
├── skills/
│   └── project-memory/
│       ├── SKILL.md            ← 主 skill 定义
│       └── references/
│           ├── file-formats.md     ← 详细文件格式
│           ├── workflows.md        ← 六个工作流完整步骤
│           ├── failure-modes.md    ← 10种失败模式与恢复
│           ├── observability.md    ← 健康指标
│           └── style-examples.md   ← 文档好例与坏例
├── .github/
│   └── ISSUE_TEMPLATE/
│       ├── field-report.md     ← 实地报告模板
│       └── bug_report.md       ← Bug 报告模板
├── MIGRATIONS.md               ← 版本迁移步骤
└── README.md                   ← 本文件
```

## 设计哲学

**代码是事实来源。文档是它的索引和守门员。**

文档不替代代码——它们让代码可被检索、意图可被回溯、变更可被验证。

## 已知限制

1. **软约束** — 无法物理阻止 agent 跳步。
2. **语义测试覆盖** — 仅结构性检查，行为覆盖需覆盖率工具。
3. **多 Agent 并发** — 仅建议性，Git 是最终仲裁。
4. **文档膨胀** — 靠风格契约控制，skill 无法自动检测劣质文字。

## 版本

1.0.0 — 初始版本。

## 反馈

在任何项目中使用 `[skill-feedback {你的痛点}]` 来记录问题。维护者会跨项目收集反馈来演化 skill。
