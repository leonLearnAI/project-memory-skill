# Failure Modes Reference

Ten known failure modes. When detected, follow the recovery procedure.

**Truth priority when documents conflict:**

```
Code > test-cases.xml > prd.md > architecture.xml > changelog.md
```

---

## F1 — Workflow Interrupted

**Detection:** `.workflow-state` shows `status=in-progress` at session start.

**Recovery:**
1. Read `.workflow-state` to find last completed step and workflow type.
2. Resume from the next step. Do not restart from step 1.
3. If the interrupted workflow made partial changes to documents, verify consistency before continuing.
4. If you cannot determine safe resume point, run Workflow D first.

---

## F2 — Document Inconsistency

**Detection:** Audit cross-validation finds a requirement in `prd.md` with no corresponding test in `test-cases.xml`, or a test that references a non-existent requirement ID.

**Recovery:**
1. List all unlinked items.
2. For missing tests: ask user whether to add tests or mark requirement `status=at-risk`.
3. For orphaned tests (no matching requirement): ask user whether to create the requirement or delete the test.
4. Apply changes using Workflow A or B as appropriate.

---

## F3 — Contradicting ADRs

**Detection:** Two ADRs in `decisions.md` mandate incompatible behaviors and neither has a `supersedes` field pointing to the other.

**Recovery:**
1. Present both ADRs to the user.
2. User decides which is authoritative.
3. Add `supersedes: {older ADR ID}` to the newer ADR.
4. Mark the superseded ADR `status=superseded` with a note pointing to the new one.
5. Do NOT delete superseded ADRs — they are historical record.

---

## F4 — Circular Reference

**Detection:** Cycle detected in `depends-on` chains in `architecture.xml`.

**Recovery:**
1. Output the cycle path (e.g., `ModuleA → ModuleB → ModuleC → ModuleA`).
2. Identify which dependency is wrong (usually the most recently added).
3. Ask user to confirm the correct direction.
4. Remove or reverse the incorrect `depends-on` entry.
5. If a true circular dependency exists in the code, record it as a `risk-point` in the affected requirement.

---

## F5 — Agent Skipped Workflow

**Detection:** Most recent git commit timestamp is more than 1 hour newer than the most recent `changelog.md` entry; OR a code-modifying session has no `[skill-checklist]` output in its transcript.

**Recovery:**
1. Identify which commits lack changelog coverage.
2. Ask user to describe what changed.
3. Add retrospective entries to `changelog.md` for each uncovered commit.
4. If tests were skipped, add them now (Workflow B step 5 pattern).
5. If architecture changed, update `architecture.xml`.
6. Append note to `decisions.md` if a significant design choice was made.

---

## F6 — Tests Pass But Requirement Fails

**Detection:** All test cases for a criterion are marked passing, but the criterion's keywords are not present in any test description — indicating tests verify something else.

**Recovery:**
1. Display the criterion text and the linked test descriptions side by side.
2. Ask user: is the test wrong, or is the criterion too broadly stated?
3. If test is wrong: rewrite the test description and implementation to match the criterion.
4. If criterion is too broad: narrow it to what the test actually verifies, and create a separate criterion for the uncovered behavior.
5. Do NOT simply mark the issue resolved without a concrete change to test or criterion.

---

## F7 — Concurrent Modification

**Detection:** `.workflow-state` `last_activity` timestamp from another session is less than 30 minutes ago and overlaps the same requirement IDs as your current task.

**Recovery:**
1. Do NOT proceed with your modification.
2. Alert user: another session was recently active on the same requirement.
3. Wait for user to confirm the other session is complete.
4. Check if that session produced a `[skill-checklist]` and updated `.workflow-state` to `completed`.
5. Once confirmed clear, proceed normally.

---

## F8 — Hotfix Debt Accumulation

**Detection:** `debt.md` contains entries with `deadline` in the past.

**Recovery:**
1. List all overdue debt entries with their deadlines.
2. Present to user before starting any new work.
3. For each overdue entry: complete the skipped steps (tests, ADR, architecture) using Workflow B.
4. Mark entry `status=resolved` with completion date.
5. If deadline extension is needed, user must explicitly approve with a new deadline.

---

## F9 — Archive Removed Referenced Item

**Detection:** A live requirement, test, or ADR contains an ID reference (e.g., `see R-005`, `supersedes D-003`) that points to an entry now located in `archive/`.

**Recovery:**
1. Locate the archived item.
2. Determine if the reference is still meaningful.
3. If meaningful: copy the minimum relevant information from the archive back into a note in the active file, or update the reference to point to the archive path explicitly.
4. If no longer meaningful: remove the dangling reference.

---

## F10 — Skill Version Mismatch

**Detection:** `.skill-version` in the project differs from the current skill version.

**Recovery:**
1. Read `MIGRATIONS.md` in the skill folder.
2. Find migration steps from the project's version to the current version.
3. Present migration steps to user: "Skill updated to X.Y.Z. Apply migration?"
4. On confirmation, run the migration steps.
5. Update `.skill-version` to current version.
6. If no migration entry exists for this version transition, check if `.skill-version` is newer than current — this means the skill was downgraded; warn user.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 失败模式参考

十种已知失败模式。检测到时按恢复流程处理。

**文档冲突时的可信度优先级：**

```
代码 > test-cases.xml > prd.md > architecture.xml > changelog.md
```

---

## F1 — 工作流中断

**检测：** 会话开始时 `.workflow-state` 显示 `status=in-progress`。

**恢复：**
1. 读 `.workflow-state`，找到最后完成的步骤和工作流类型。
2. 从下一步继续。不要从步骤1重新开始。
3. 如果中断的工作流对文档做了部分修改，继续前先验证一致性。
4. 如果无法确定安全的恢复点，先运行工作流D。

---

## F2 — 文档不一致

**检测：** 审计交叉验证发现 `prd.md` 中的需求在 `test-cases.xml` 中没有对应测试，或者测试引用了不存在的需求ID。

**恢复：**
1. 列出所有未关联的条目。
2. 对于缺失的测试：询问用户是添加测试还是将需求标为 `status=at-risk`。
3. 对于孤立测试（没有匹配需求）：询问用户是创建需求还是删除测试。
4. 视情况使用工作流A或B应用变更。

---

## F3 — ADR 矛盾

**检测：** `decisions.md` 中两个ADR规定了互不相容的行为，且两者都没有指向对方的 `supersedes` 字段。

**恢复：**
1. 向用户呈现两个ADR。
2. 用户决定哪个为准。
3. 向较新的ADR添加 `supersedes: {旧ADR ID}`。
4. 将被取代的ADR标为 `status=superseded`，注明指向新ADR。
5. 不要删除被取代的ADR——它们是历史记录。

---

## F4 — 循环引用

**检测：** 在 `architecture.xml` 的 `depends-on` 链中检测到环。

**恢复：**
1. 输出循环路径（如 `模块A → 模块B → 模块C → 模块A`）。
2. 确定哪条依赖是错误的（通常是最近添加的）。
3. 请用户确认正确的方向。
4. 删除或反转错误的 `depends-on` 条目。
5. 如果代码中确实存在真正的循环依赖，将其记录为受影响需求的 `risk-point`。

---

## F5 — Agent 跳步

**检测：** 最新git commit时间戳比最新 `changelog.md` 条目新超过1小时；或者修改代码的会话没有输出 `[skill-checklist]`。

**恢复：**
1. 确定哪些commit没有对应的changelog条目。
2. 询问用户变更了什么。
3. 为每个未覆盖的commit向 `changelog.md` 补充回溯条目。
4. 如果跳过了测试，现在添加（参照工作流B步骤5）。
5. 如果架构发生变化，更新 `architecture.xml`。
6. 如果做出了重要设计决定，向 `decisions.md` 追加备注。

---

## F6 — 测试通过但需求未实现

**检测：** 某条标准的所有测试用例标记为通过，但任何测试描述中都不包含该标准的关键词——说明测试验证的是其他内容。

**恢复：**
1. 并排展示标准文本和关联的测试描述。
2. 询问用户：是测试写错了，还是标准写得太宽泛？
3. 如果测试写错：重写测试描述和实现，使其与标准一致。
4. 如果标准太宽泛：将其收窄为测试实际验证的内容，并为未覆盖行为创建单独的标准。
5. 不要在没有具体变更的情况下简单标记问题已解决。

---

## F7 — 并发修改

**检测：** 另一个会话的 `.workflow-state` `last_activity` 时间戳不到30分钟前，且与当前任务的需求ID重叠。

**恢复：**
1. 不要继续修改。
2. 提醒用户：另一个会话最近在同一需求上活动过。
3. 等待用户确认另一个会话已完成。
4. 确认该会话已输出 `[skill-checklist]` 且 `.workflow-state` 已更新为 `completed`。
5. 确认无冲突后，正常继续。

---

## F8 — 紧急修复债务累积

**检测：** `debt.md` 中包含截止日期已过的条目。

**恢复：**
1. 列出所有逾期债务条目及其截止日期。
2. 开始任何新工作前先呈现给用户。
3. 对每个逾期条目：使用工作流B完成跳过的步骤（测试、ADR、架构）。
4. 将条目标为 `status=resolved`，注明完成日期。
5. 如需延期，用户必须明确批准并给出新截止日期。

---

## F9 — 归档误删引用目标

**检测：** 活跃需求、测试或ADR包含ID引用（如 `see R-005`、`supersedes D-003`），但该条目已被移入 `archive/`。

**恢复：**
1. 找到已归档的条目。
2. 判断该引用是否仍有意义。
3. 如果有意义：将归档中的最少相关信息复制回活跃文件的备注中，或将引用明确指向存档路径。
4. 如果不再有意义：删除悬空引用。

---

## F10 — Skill 版本不匹配

**检测：** 项目中的 `.skill-version` 与当前skill版本不同。

**恢复：**
1. 读skill文件夹中的 `MIGRATIONS.md`。
2. 找到从项目版本到当前版本的迁移步骤。
3. 向用户呈现迁移步骤："Skill 已更新到 X.Y.Z。是否应用迁移？"
4. 确认后运行迁移步骤。
5. 将 `.skill-version` 更新为当前版本。
6. 如果此次版本跨越没有迁移条目，检查 `.skill-version` 是否比当前版本更新——这说明skill被降级了；警告用户。
