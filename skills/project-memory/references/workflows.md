# Workflows Reference

Six atomic workflows. Each is an ordered checklist. Skipping steps is forbidden.
After every step, update `.workflow-state`.

---

## Workflow A — New Requirement

**When:** Adding a new feature or capability.

1. Check `.workflow-state` for any interrupted workflow. If found, resolve it first (Workflow D or resume).
2. Search `prd.md` for any existing requirement that partially covers this feature. If found, extend it (do not duplicate).
3. Add requirement to `prd.md`:
   - Assign a new ID (`R-NNN`).
   - Write `intent`, `acceptance-criteria` (each criterion gets its own ID `AC-NNN-N`), and `risk-points`.
   - Apply the writing style contract: max 200 chars, no empty sentences.
4. If architecture is affected, update `architecture.xml`. Add or modify the relevant `<module>` entries.
5. Add at least one test case in `test-cases.xml` for each acceptance criterion, with `covers="AC-NNN-N"`.
6. Write the code.
7. Verify the code satisfies every acceptance criterion manually or via tests.
8. Append to `changelog.md`: date, type=`added`, affected-requirement ID, commit hash.
9. If `strict` tier: check `dependencies.xml` for any new external dependency introduced.
10. Update `.workflow-state` to `completed`.
11. Output `[skill-checklist]`.

---

## Workflow B — Bug Fix

**When:** Fixing behavior that violates an existing requirement.

1. Identify the requirement ID that is violated. If none exists, create one (Workflow A first).
2. Check `test-cases.xml` for the failing criterion. If the test does not exist, add it now (before fixing).
3. Confirm the test fails against current code (document result in `.workflow-state`).
4. Fix the code.
5. Confirm the test passes.
6. If the bug reveals a gap in `prd.md` (missing risk point, ambiguous criterion), update it.
7. Append to `changelog.md`: date, type=`fixed`, affected-requirement ID, commit hash.
8. If `strict` tier and a hotfix was used: add entry to `debt.md` with deadline.
9. Update `.workflow-state` to `completed`.
10. Output `[skill-checklist]`.

---

## Workflow C — Project Onboarding

**When:** First session on an existing codebase that has `.project-memory/`.

**Phase 1 — Understand current state (do NOT modify code yet):**

1. Read `INDEX.md` for the 60-second overview.
2. Read `prd.md` fully. Note any requirement marked `status=deprecated` or `status=at-risk`.
3. Read `architecture.xml` (if present). Map modules to actual files.
4. Read `decisions.md` (if present). Note any ADR marked `revisit-by` in the past.
5. Run the full health check (all 10 failure modes, not just F1/F5/F8/F10).
6. Produce `current-state-report.md`:
   - List every inconsistency found.
   - List every requirement without test coverage.
   - List every ADR past its `revisit-by` date.
   - List any `debt.md` entries past deadline.
7. Present the report to the user before proceeding.

**Phase 2 — Resolve issues (if user approves):**

8. For each critical inconsistency: ask user whether to fix document or code.
9. Apply fixes using the appropriate workflow (A/B/D/E).
10. Delete `current-state-report.md` after review.
11. Update `.workflow-state` to `completed`.
12. Output `[skill-checklist]`.

---

## Workflow D — Audit

**When:** Manual trigger via `[skill-audit]`, or when inconsistency is suspected.

Run all 10 failure mode checks:

1. F1 — Check `.workflow-state` for incomplete workflows.
2. F2 — Cross-validate: every requirement in `prd.md` has at least one test in `test-cases.xml`.
3. F3 — Scan `decisions.md` for ADRs that contradict each other without `supersedes` links.
4. F4 — Check `architecture.xml` for circular `depends-on` chains.
5. F5 — Compare most recent git commit timestamp to most recent `changelog.md` entry. Flag gap > 1 hour.
6. F6 — For each acceptance criterion, verify at least one test's description contains criterion keywords.
7. F7 — Check `.workflow-state` `last_activity` for sessions < 30 min ago on same requirements.
8. F8 — Scan `debt.md` for entries past deadline.
9. F9 — Scan all files for ID references that point to archived items.
10. F10 — Compare `.skill-version` to current skill version.

Produce `inconsistencies.md` listing every failure found with its recovery action.
Present to user. Do NOT auto-fix; wait for user to decide.

Update `.workflow-state` to `completed`.
Output `[skill-checklist]`.

---

## Workflow E — Deprecation

**When:** Removing a feature or module.

1. Identify requirement ID(s) being deprecated.
2. Check for any other requirements that reference this requirement via ID. Update them.
3. In `prd.md`, mark requirement `status=deprecated`. Do NOT delete yet.
4. In `architecture.xml`, remove or mark affected modules. Update `used-by` / `depends-on` in all referencing modules.
5. In `test-cases.xml`, mark affected test cases `status=deprecated`. Do NOT delete yet.
6. Remove or disable the code.
7. Append to `changelog.md`: date, type=`removed`, affected-requirement ID, commit hash.
8. Append to `decisions.md`: why deprecated, what replaces it (if anything), date.
9. After one release cycle: move deprecated entries to `archive/`.
10. Update `.workflow-state` to `completed`.
11. Output `[skill-checklist]`.

---

## Workflow F — Hotfix

**When:** Production emergency requiring immediate fix without full workflow.

1. Open `.workflow-state` and set `mode=hotfix`, record the affected requirement ID and start time.
2. Fix the code. Commit immediately.
3. Add a minimal `changelog.md` entry: date, type=`hotfix`, requirement ID, commit hash, debt-flag=`yes`.
4. Add entry to `debt.md` (strict tier required; recommended for standard):
   - What was skipped (tests, ADR, architecture update).
   - Deadline to complete properly (default: next sprint).
5. Update `.workflow-state` to `completed`.
6. Output `[skill-checklist]`.
7. **After the emergency:** complete the skipped steps using Workflow B and mark `debt.md` entry resolved.

---

## Workflow State Format

`.workflow-state` is a plain-text file. Format:

```
workflow: {A|B|C|D|E|F}
status: {in-progress|completed}
step: {current step number}
requirement: {R-NNN or n/a}
last_activity: {ISO timestamp}
notes: {free text for recovery context}
```

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 工作流参考

六个原子工作流。每个都是有序清单，禁止跳步。
每完成一步，更新 `.workflow-state`。

---

## 工作流 A — 新增需求

**何时使用：** 添加新功能或能力。

1. 检查 `.workflow-state` 是否有中断的工作流。如有，先解决（工作流D或继续）。
2. 在 `prd.md` 中搜索是否已有部分覆盖此功能的需求。如有，扩展它（不要重复）。
3. 向 `prd.md` 添加需求：
   - 分配新ID（`R-NNN`）。
   - 写 `intent`、`acceptance-criteria`（每条标准有自己的ID `AC-NNN-N`）和 `risk-points`。
   - 遵守文档风格契约：每条最多200字符，不写空话。
4. 如果架构受影响，更新 `architecture.xml`。添加或修改相关 `<module>` 条目。
5. 在 `test-cases.xml` 中为每条验收标准至少添加一个测试用例，带 `covers="AC-NNN-N"`。
6. 写代码。
7. 手动或通过测试验证代码满足每条验收标准。
8. 向 `changelog.md` 追加：日期、type=`added`、受影响需求ID、commit hash。
9. 若为 `strict` 档位：检查 `dependencies.xml` 是否引入了新的外部依赖。
10. 将 `.workflow-state` 更新为 `completed`。
11. 输出 `[skill-checklist]`。

---

## 工作流 B — 修复 Bug

**何时使用：** 修复违反现有需求的行为。

1. 确定被违反的需求ID。如无，先用工作流A创建。
2. 在 `test-cases.xml` 中查找失败的标准对应的测试。如不存在，先添加（修复前）。
3. 确认测试在当前代码下失败（在 `.workflow-state` 中记录结果）。
4. 修复代码。
5. 确认测试通过。
6. 如果 bug 揭示了 `prd.md` 的缺口（缺少风险点、标准模糊），更新之。
7. 向 `changelog.md` 追加：日期、type=`fixed`、受影响需求ID、commit hash。
8. 若为 `strict` 档位且使用了紧急修复：向 `debt.md` 添加带截止日期的条目。
9. 将 `.workflow-state` 更新为 `completed`。
10. 输出 `[skill-checklist]`。

---

## 工作流 C — 接手项目

**何时使用：** 第一次接触已有 `.project-memory/` 的代码库。

**阶段1 — 了解现状（暂不修改代码）：**

1. 读 `INDEX.md` 获取60秒概览。
2. 完整读 `prd.md`。注意标记为 `status=deprecated` 或 `status=at-risk` 的需求。
3. 读 `architecture.xml`（如有）。将模块映射到实际文件。
4. 读 `decisions.md`（如有）。注意 `revisit-by` 已过期的ADR。
5. 运行完整健康检查（全部10种失败模式，不只是F1/F5/F8/F10）。
6. 生成 `current-state-report.md`：
   - 列出所有发现的不一致。
   - 列出所有没有测试覆盖的需求。
   - 列出所有过了 `revisit-by` 日期的ADR。
   - 列出 `debt.md` 中所有逾期条目。
7. 在继续之前，将报告呈现给用户。

**阶段2 — 解决问题（如用户批准）：**

8. 对每个严重不一致：询问用户是修文档还是修代码。
9. 使用相应工作流（A/B/D/E）应用修复。
10. 审阅完成后删除 `current-state-report.md`。
11. 将 `.workflow-state` 更新为 `completed`。
12. 输出 `[skill-checklist]`。

---

## 工作流 D — 审计

**何时使用：** 通过 `[skill-audit]` 手动触发，或怀疑有不一致时。

运行全部10种失败模式检查：

1. F1 — 检查 `.workflow-state` 是否有未完成的工作流。
2. F2 — 交叉验证：`prd.md` 中每个需求在 `test-cases.xml` 中至少有一个测试。
3. F3 — 扫描 `decisions.md`，检查互相矛盾但没有 `supersedes` 链接的ADR。
4. F4 — 检查 `architecture.xml` 中的循环 `depends-on` 链。
5. F5 — 比较最新git commit时间戳与最新 `changelog.md` 条目时间。标记超过1小时的间隔。
6. F6 — 对每条验收标准，验证至少一个测试的描述包含该标准的关键词。
7. F7 — 检查 `.workflow-state` 的 `last_activity`，查看是否有不到30分钟前在同一需求上活动的会话。
8. F8 — 扫描 `debt.md` 中逾期的条目。
9. F9 — 扫描所有文件中指向已归档条目的ID引用。
10. F10 — 比较 `.skill-version` 与当前skill版本。

生成 `inconsistencies.md`，列出每个发现的失败及其恢复动作。
呈现给用户。不自动修复；等待用户决定。

将 `.workflow-state` 更新为 `completed`。
输出 `[skill-checklist]`。

---

## 工作流 E — 废弃

**何时使用：** 移除功能或模块。

1. 确定被废弃的需求ID。
2. 检查其他通过ID引用此需求的需求。更新它们。
3. 在 `prd.md` 中标记需求 `status=deprecated`。暂不删除。
4. 在 `architecture.xml` 中删除或标记受影响模块。更新所有引用模块的 `used-by` / `depends-on`。
5. 在 `test-cases.xml` 中标记受影响测试用例 `status=deprecated`。暂不删除。
6. 移除或禁用代码。
7. 向 `changelog.md` 追加：日期、type=`removed`、受影响需求ID、commit hash。
8. 向 `decisions.md` 追加：废弃原因、替代方案（如有）、日期。
9. 一个发布周期后：将已废弃条目移入 `archive/`。
10. 将 `.workflow-state` 更新为 `completed`。
11. 输出 `[skill-checklist]`。

---

## 工作流 F — 紧急修复

**何时使用：** 生产环境紧急情况，需要立即修复，不走完整流程。

1. 打开 `.workflow-state`，设置 `mode=hotfix`，记录受影响需求ID和开始时间。
2. 修复代码。立即commit。
3. 向 `changelog.md` 添加最简条目：日期、type=`hotfix`、需求ID、commit hash、debt-flag=`yes`。
4. 向 `debt.md` 添加条目（strict档位必须；standard档位推荐）：
   - 跳过了什么（测试、ADR、架构更新）。
   - 补全截止日期（默认：下一个sprint）。
5. 将 `.workflow-state` 更新为 `completed`。
6. 输出 `[skill-checklist]`。
7. **紧急情况解除后：** 用工作流B完成跳过的步骤，将 `debt.md` 条目标为已解决。

---

## workflow-state 格式

`.workflow-state` 是纯文本文件。格式：

```
workflow: {A|B|C|D|E|F}
status: {in-progress|completed}
step: {当前步骤编号}
requirement: {R-NNN 或 n/a}
last_activity: {ISO时间戳}
notes: {恢复上下文的自由文本}
```
