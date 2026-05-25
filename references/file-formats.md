# File Formats Reference

Full schemas and examples for every file in `.project-memory/`.

---

## INDEX.md

**Purpose:** Agent entry point. 60-second overview of the project.

**Format:** Free Markdown. No strict schema.

**Required sections:**
- Project name and one-sentence description.
- Current tier.
- Active requirement count and most recent changelog date.
- Any current critical issues (F1/F5/F8/F10 flags).

**Example:**

```markdown
# MyApp — Project Memory Index

Tier: standard | Version: 1.2.0 | Last updated: 2026-03-15

A CLI tool for batch-renaming files by pattern.

## Quick state
- Active requirements: 8 (R-001 to R-012, R-007/R-010 deprecated)
- Last change: 2026-03-15 — R-008 bug fix (commit a3f9b2c)
- No open hotfix debts.
- No incomplete workflows.
```

---

## prd.md

**Purpose:** Active requirements with acceptance criteria and risk points.

**Format:** Markdown with inline XML tags for machine-readable fields.

**Per-requirement schema:**

```markdown
## R-NNN — {Short title}

<requirement id="R-NNN" status="active|deprecated|at-risk">
  <intent>{What this feature does. Max 200 chars total for this block.}</intent>
  <acceptance-criteria>
    <criterion id="AC-NNN-1">{Criterion in plain language. Verb-first.}</criterion>
    <criterion id="AC-NNN-2">{...}</criterion>
  </acceptance-criteria>
  <risk-points>{Known edge cases or failure modes. Specific, not vague.}</risk-points>
</requirement>
```

**Example:**

```markdown
## R-003 — Rename dry-run mode

<requirement id="R-003" status="active">
  <intent>User can preview renames without writing to disk. Activated by --dry-run flag.</intent>
  <acceptance-criteria>
    <criterion id="AC-003-1">Running with --dry-run prints each rename as "OLD → NEW" without modifying files.</criterion>
    <criterion id="AC-003-2">Exit code 0 even when --dry-run detects conflicts.</criterion>
  </acceptance-criteria>
  <risk-points>Conflict detection must use the same logic as live rename to avoid false previews.</risk-points>
</requirement>
```

---

## architecture.xml

**Purpose:** Module dependency graph.

**Format:** XML.

**Schema:**

```xml
<architecture version="1.0">
  <module name="{name}" purpose="{max 50 chars}">
    <depends-on>{module-name}</depends-on>
    <used-by>{module-name}</used-by>
  </module>
</architecture>
```

**Example:**

```xml
<architecture version="1.0">
  <module name="cli" purpose="Parses argv, dispatches to core">
    <depends-on>core</depends-on>
    <used-by>none</used-by>
  </module>
  <module name="core" purpose="Rename logic, conflict detection">
    <depends-on>fs-adapter</depends-on>
    <used-by>cli</used-by>
  </module>
  <module name="fs-adapter" purpose="Wraps Node fs calls for testability">
    <depends-on>none</depends-on>
    <used-by>core</used-by>
  </module>
</architecture>
```

---

## test-cases.xml

**Purpose:** Test cases linked to acceptance criteria.

**Format:** XML.

**Schema:**

```xml
<test-suite>
  <test id="T-NNN" covers="AC-NNN-N" status="passing|failing|pending|deprecated">
    <description>{What this test verifies. Must contain keywords from the criterion it covers.}</description>
    <location>{file path or test name}</location>
  </test>
</test-suite>
```

**Example:**

```xml
<test-suite>
  <test id="T-007" covers="AC-003-1" status="passing">
    <description>--dry-run prints OLD → NEW for each file without writing to disk</description>
    <location>tests/dry-run.test.ts:22</location>
  </test>
  <test id="T-008" covers="AC-003-2" status="passing">
    <description>--dry-run exits 0 even when conflicts are detected</description>
    <location>tests/dry-run.test.ts:45</location>
  </test>
</test-suite>
```

---

## decisions.md

**Purpose:** Architecture Decision Record (ADR) log. Append-only.

**Format:** Markdown with inline XML tags.

**Per-ADR schema:**

```markdown
## D-NNN — {Short title}

<decision id="D-NNN" date="YYYY-MM-DD" status="active|superseded">
  <context>{Why this decision was needed. Max 150 chars total for this block.}</context>
  <options>{Option A vs Option B. Specific trade-offs only.}</options>
  <decision>{What was chosen and why.}</decision>
  <irreversibility>{low|medium|high — and why.}</irreversibility>
  <supersedes>{D-NNN or none}</supersedes>
  <revisit-by>{YYYY-MM-DD or none}</revisit-by>
</decision>
```

**Example:**

```markdown
## D-002 — Use fs-adapter for all disk operations

<decision id="D-002" date="2026-01-10" status="active">
  <context>Core module was calling fs directly; made unit-testing impossible without mocking globals.</context>
  <options>A: inject fs mock in tests. B: wrap all fs calls in fs-adapter.</options>
  <decision>B. Adapter boundary is explicit; A hides the dependency.</decision>
  <irreversibility>medium — refactor needed if switching, but contained to core+adapter.</irreversibility>
  <supersedes>none</supersedes>
  <revisit-by>none</revisit-by>
</decision>
```

---

## changelog.md

**Purpose:** Functional changes, not git log.

**Format:** Markdown. One entry per change event. Apply anti-bloat merge rules.

**Per-entry schema:**

```markdown
## YYYY-MM-DD — {type}: {one-line summary}

- id: C-NNN
- type: added | fixed | removed | hotfix | refactor
- requirements: R-NNN, R-NNN
- commit: {hash}
- debt-flag: yes | no
```

**Example:**

```markdown
## 2026-03-15 — fixed: dry-run conflict detection

- id: C-012
- type: fixed
- requirements: R-003
- commit: a3f9b2c
- debt-flag: no
```

---

## dependencies.xml

**Purpose:** External dependency tracking. Strict tier only.

**Format:** XML.

**Schema:**

```xml
<dependencies>
  <dep name="{package-name}" version="{semver}" used-for="{max 30 chars}" status="active|deprecated"/>
</dependencies>
```

**Example:**

```xml
<dependencies>
  <dep name="minimist" version="^1.2.8" used-for="argv parsing" status="active"/>
  <dep name="chalk" version="^5.3.0" used-for="terminal color output" status="active"/>
</dependencies>
```

---

## debt.md

**Purpose:** Hotfix follow-up tracking. Strict tier required; recommended for standard.

**Format:** Markdown with inline XML tags.

**Per-entry schema:**

```markdown
## DB-NNN — {Short description}

<debt id="DB-NNN" status="open|resolved">
  <created>YYYY-MM-DD</created>
  <deadline>YYYY-MM-DD</deadline>
  <requirement>R-NNN</requirement>
  <skipped>{What workflow steps were skipped: tests | ADR | architecture | other}</skipped>
  <resolved>{YYYY-MM-DD or none}</resolved>
</debt>
```

**Example:**

```markdown
## DB-003 — Add tests for conflict-detection edge case

<debt id="DB-003" status="open">
  <created>2026-03-10</created>
  <deadline>2026-03-24</deadline>
  <requirement>R-003</requirement>
  <skipped>tests, ADR</skipped>
  <resolved>none</resolved>
</debt>
```

---

## feedback.md

**Purpose:** User pain points for skill improvement. Append-only.

**Format:** Free Markdown. One entry per pain point.

**Example:**

```markdown
## 2026-03-12 — Style contract too strict for one-liner fixes

Workflow B step 2 says to add a test before fixing. For a typo fix in a log message (R-001),
this felt like overhead that broke flow. Suggest: allow post-fix test for changes < 5 lines.
```

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 文件格式参考

`.project-memory/` 中每个文件的完整格式规范与示例。

---

## INDEX.md

**用途：** Agent 入口。60秒项目概览。

**格式：** 自由Markdown，无严格结构。

**必需部分：**
- 项目名称和一句话描述。
- 当前档位。
- 活跃需求数量和最新changelog日期。
- 当前严重问题（F1/F5/F8/F10标志）。

---

## prd.md

**用途：** 带验收标准和风险点的活跃需求。

**格式：** Markdown + 内嵌XML标签。

**每条需求的结构：**

```markdown
## R-NNN — {简短标题}

<requirement id="R-NNN" status="active|deprecated|at-risk">
  <intent>{功能描述。整块最多200字符。}</intent>
  <acceptance-criteria>
    <criterion id="AC-NNN-1">{用平实语言写的标准。动词开头。}</criterion>
    <criterion id="AC-NNN-2">{...}</criterion>
  </acceptance-criteria>
  <risk-points>{已知边缘案例或失败模式。具体，不空泛。}</risk-points>
</requirement>
```

---

## architecture.xml

**用途：** 模块依赖图。

**格式：** XML。每个模块的 `purpose` 最多50字符。

---

## test-cases.xml

**用途：** 关联到验收标准的测试用例。

**格式：** XML。`description` 必须包含所覆盖标准的关键词。

---

## decisions.md

**用途：** ADR日志，只追加。

**格式：** Markdown + 内嵌XML标签。整块最多150字符。

---

## changelog.md

**用途：** 功能变更，不是git日志。

**格式：** Markdown。每条最多100字符。应用反膨胀合并规则。

---

## dependencies.xml

**用途：** 外部依赖追踪。Strict档位必须。

**格式：** XML。每条 `used-for` 最多30字符。

---

## debt.md

**用途：** 紧急修复的后续追踪。Strict档位必须，standard档位推荐。

**格式：** Markdown + 内嵌XML标签。

---

## feedback.md

**用途：** 用户痛点，用于改进skill。只追加。

**格式：** 自由Markdown，每条一个痛点。
