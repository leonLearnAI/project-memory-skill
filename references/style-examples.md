# Style Examples Reference

Good vs bad documentation examples, mapped to the writing style contract in `SKILL.md`.

The contract applies to files inside `.project-memory/`. It does NOT apply to `SKILL.md` or reference docs.

---

## The Core Test

Every sentence must satisfy one of:
1. Contains a **fact** (ID, date, commit hash, status, number).
2. Contains a **judgment that remains useful in the future** (why A not B, when to revisit).

Sentences satisfying neither → delete.

---

## prd.md Examples

### ❌ Bad intent

```xml
<intent>This feature improves the user experience by making the application
more intuitive and easier to use for end users who want to rename files.</intent>
```

Problems: "improves user experience", "more intuitive", "easier to use" — vacuous. No testable fact.

### ✅ Good intent

```xml
<intent>User renames multiple files matching a glob pattern in one command.
Activated by: rename-tool "*.txt" --prefix "2026-"</intent>
```

Facts: what it does, how it's invoked.

---

### ❌ Bad acceptance criterion

```xml
<criterion id="AC-003-1">The dry-run mode should work correctly and provide
useful feedback to the user.</criterion>
```

Problems: "work correctly" is not testable. "Useful feedback" is vague.

### ✅ Good acceptance criterion

```xml
<criterion id="AC-003-1">Running with --dry-run prints each rename as
"OLD → NEW" without modifying files on disk.</criterion>
```

Fact: specific output format, specific non-side-effect. A test can directly verify this.

---

### ❌ Bad risk-point

```xml
<risk-points>Edge cases should be handled carefully. Performance may be
an issue for large directories.</risk-points>
```

Problems: "handled carefully" says nothing. No threshold for "large".

### ✅ Good risk-point

```xml
<risk-points>Directories with >10,000 files: glob expansion may OOM.
See D-005 for the decision to cap at 10,000 and error early.</risk-points>
```

Facts: threshold number, consequence, pointer to ADR.

---

## decisions.md Examples

### ❌ Bad ADR

```markdown
## D-004 — Chose the best architecture

After careful consideration of many options, we decided to go with the
approach that seemed most maintainable and scalable for future growth.
```

Problems: "careful consideration" — narrative. "Most maintainable" — no comparison. "Future growth" — empty.

### ✅ Good ADR

```xml
<decision id="D-004" date="2026-02-01" status="active">
  <context>Core module called fs directly; 100% of tests required real disk I/O.</context>
  <options>A: mock fs in tests (global patch). B: wrap fs in adapter injected via DI.</options>
  <decision>B. A hides the dependency; B makes it explicit at call site.</decision>
  <irreversibility>medium — all callers of core must pass adapter; contained refactor.</irreversibility>
  <supersedes>none</supersedes>
  <revisit-by>none</revisit-by>
</decision>
```

Facts: concrete problem (100% I/O), concrete options, specific reason for choice.

---

## changelog.md Examples

### ❌ Bad changelog entry

```markdown
## Today — Various improvements

Made some improvements to the codebase, fixed some bugs, and added
some new functionality to improve the overall quality of the project.
```

Problems: no date, no IDs, no commit hash, "some" repeated, "various improvements" — useless.

### ✅ Good changelog entry

```markdown
## 2026-03-15 — fixed: dry-run conflict detection

- id: C-012
- type: fixed
- requirements: R-003
- commit: a3f9b2c
- debt-flag: no
```

Facts: exact date, type, requirement ID, commit.

---

## architecture.xml Examples

### ❌ Bad module entry

```xml
<module name="utils" purpose="Utility functions that help with various things
throughout the application">
```

Problems: "various things" — not a purpose. Exceeds 50 chars without adding information.

### ✅ Good module entry

```xml
<module name="fs-adapter" purpose="Wraps Node fs calls for testability">
  <depends-on>none</depends-on>
  <used-by>core</used-by>
</module>
```

Facts: what it wraps, why (testability), dependencies explicit.

---

## Anti-Bloat Merge Rule Examples

### ❌ Accumulating entries for the same requirement

```markdown
## 2026-03-10 — fixed: R-003 dry-run output format (commit abc1234)
## 2026-03-10 — fixed: R-003 dry-run exit code (commit def5678)
## 2026-03-10 — fixed: R-003 dry-run edge case with spaces (commit ghi9012)
```

Three entries same day, same requirement → merge into one.

### ✅ Merged entry

```markdown
## 2026-03-10 — fixed: R-003 dry-run (output format, exit code, space edge case)

- id: C-009
- type: fixed
- requirements: R-003
- commit: ghi9012
- debt-flag: no
```

Latest commit hash, one entry, all changes summarized.

---

## Code File Header Examples (strict tier)

### ❌ Bad header

```typescript
// This file contains the core business logic for the application.
// It was written to handle the main functionality.
// Author: developer
// Date: sometime in 2026
```

Problems: "core business logic" and "main functionality" — say nothing specific.

### ✅ Good header

```typescript
// core.ts — Rename execution and conflict detection.
// Key methods: executeRename(plan, adapter), detectConflicts(plan)
// Requirements: R-001, R-003
// Last modified: 2026-03-15 (commit a3f9b2c, C-012)
```

Facts: file purpose, key methods by name, requirement IDs, last commit.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 文档风格示例

好例与坏例对比，对应 `SKILL.md` 中的文档风格契约。

契约适用于 `.project-memory/` 中的文件，不适用于 `SKILL.md` 和 reference 文档。

---

## 核心测试

每句话必须满足以下之一：
1. 包含**事实**（ID、日期、commit hash、状态、数字）。
2. 包含**未来仍有用的判断**（为什么选A不选B、何时重新审视）。

不满足任一条件 → 删除。

---

## prd.md 示例

### ❌ 坏的 intent

```xml
<intent>此功能通过让应用程序更直观、更易用来改善用户体验，
方便想重命名文件的终端用户。</intent>
```

问题："改善用户体验"、"更直观"、"更易用"——空话，无法测试。

### ✅ 好的 intent

```xml
<intent>用户用一条命令批量重命名匹配glob模式的文件。
调用方式：rename-tool "*.txt" --prefix "2026-"</intent>
```

事实：做什么、怎么调用。

---

### ❌ 坏的验收标准

```xml
<criterion id="AC-003-1">dry-run模式应该正确工作并向用户提供有用的反馈。</criterion>
```

问题："正确工作"无法测试，"有用的反馈"含糊。

### ✅ 好的验收标准

```xml
<criterion id="AC-003-1">带 --dry-run 运行时，打印每个重命名为 "OLD → NEW"，不修改磁盘文件。</criterion>
```

事实：具体输出格式，具体无副作用。测试可直接验证。

---

## decisions.md 示例

### ❌ 坏的 ADR

```markdown
## D-004 — 选择了最佳架构

经过深思熟虑，我们选择了看起来对未来增长最易维护和最可扩展的方案。
```

问题："深思熟虑"是叙事，"最易维护"无对比，"未来增长"是空话。

### ✅ 好的 ADR

```xml
<decision id="D-004" date="2026-02-01" status="active">
  <context>core模块直接调用fs；100%测试需要真实磁盘I/O。</context>
  <options>A：测试中mock fs（全局patch）。B：将fs封装进adapter，通过DI注入。</options>
  <decision>B。A隐藏了依赖；B在调用点显式体现。</decision>
  <irreversibility>medium — 所有core调用者须传adapter；可控范围内重构。</irreversibility>
  <supersedes>none</supersedes>
  <revisit-by>none</revisit-by>
</decision>
```

事实：具体问题（100% I/O），具体选项，具体选择原因。

---

## changelog.md 示例

### ❌ 坏的条目

```markdown
## 今天 — 各种改进

对代码库做了一些改进，修复了一些bug，添加了一些新功能以提升整体质量。
```

问题：无日期、无ID、无commit hash，"一些"反复出现，"各种改进"无意义。

### ✅ 好的条目

```markdown
## 2026-03-15 — fixed: dry-run冲突检测

- id: C-012
- type: fixed
- requirements: R-003
- commit: a3f9b2c
- debt-flag: no
```

事实：精确日期、类型、需求ID、commit。

---

## 反膨胀合并规则示例

### ❌ 同一需求同一天累积多条

```markdown
## 2026-03-10 — fixed: R-003 dry-run输出格式 (commit abc1234)
## 2026-03-10 — fixed: R-003 dry-run退出码 (commit def5678)
## 2026-03-10 — fixed: R-003 dry-run空格边缘案例 (commit ghi9012)
```

同一天，同一需求，三条 → 合并为一条。

### ✅ 合并后

```markdown
## 2026-03-10 — fixed: R-003 dry-run（输出格式、退出码、空格边缘案例）

- id: C-009
- type: fixed
- requirements: R-003
- commit: ghi9012
- debt-flag: no
```

最新commit hash，一条条目，所有变更概括其中。
