# Observability Reference

Health metric definitions for the 0–100 score shown in the Health Report.

---

## Score Formula

```
Score = completeness × 30%
      + test-coverage × 30%
      + freshness × 15%
      + (100 - debt-penalty) × 15%
      + consistency × 10%
```

**Lite tier redistribution** (no `test-cases.xml`):

```
Score = completeness × 50%
      + freshness × 25%
      + consistency × 25%
```

Debt does not apply to lite tier.

---

## Metric Definitions

### Completeness (0–100)

Measures whether requirements have all required fields populated.

```
completeness = (requirements with all required fields / total active requirements) × 100
```

Required fields per requirement:
- `id` — must follow `R-NNN` format
- `intent` — non-empty, ≤ 200 chars
- `acceptance-criteria` — at least one `<criterion>` with its own ID
- `risk-points` — non-empty

A requirement missing any field counts as incomplete. Deprecated requirements are excluded.

---

### Test Coverage (0–100)

Measures whether every acceptance criterion is covered by at least one passing test.

```
test-coverage = (criteria with ≥1 passing test / total active criteria) × 100
```

A test "covers" a criterion if:
1. The test has `covers="AC-NNN-N"` pointing to that criterion, AND
2. The test's `description` contains at least one keyword from the criterion text (keyword = non-stopword token ≥ 4 chars).

If condition 2 fails, the link is structural only (F6 failure mode).

Tests with `status=failing` or `status=pending` do not count as coverage.

---

### Freshness (0–100)

Measures how recently the memory files were updated relative to code changes.

```
freshness = max(0, 100 - (days_since_changelog_update × 5))
```

Where `days_since_changelog_update` = current date − date of most recent `changelog.md` entry.

Interpretation:
- 0 days → 100 (fully fresh)
- 10 days → 50
- 20 days → 0

This is approximate. A project with no recent code changes should still have a recent entry confirming "no changes." Use a `type=maintenance` entry if needed.

---

### Debt Penalty (0–100, subtracted from score)

Measures the weight of unresolved hotfix debt.

```
debt-penalty = min(100, overdue_entries × 15 + open_entries × 5)
```

Where:
- `overdue_entries` = entries in `debt.md` with `deadline` < today and `status=open`
- `open_entries` = all entries with `status=open` (including non-overdue)

Capped at 100. Applied only in standard and strict tiers.

---

### Consistency (0–100)

Measures cross-file coherence. Checked by F2, F3, F4, F9 in the audit.

```
consistency = max(0, 100 - (active_inconsistencies × 20))
```

Where `active_inconsistencies` = count of failures in F2 + F3 + F4 + F9.

Each unresolved inconsistency costs 20 points.

---

## Score Interpretation

| Range | Meaning | Recommendation |
|---|---|---|
| 85–100 | Healthy | Proceed normally |
| 70–84 | Acceptable | Minor cleanup on next opportunity |
| 50–69 | Degraded | Address before new features |
| < 50 | Critical | Run `[skill-audit]` before any work |

---

## Health Report Format

```
📊 Project Memory Health Report
================================
Tier: {tier} | Skill version: {version} | Score: {0-100}

✅ Healthy
  • {N} active requirements, all with acceptance-criteria
  • {N} test cases, {X}% criteria covered
  • Last audit: {N} days ago

⚠️ Attention
  • {N} hotfix debts pending (earliest deadline: {date})
  • {N} criteria without tests

🔴 Critical
  • {list if any}

Recent activity:
  • {date} {summary from latest changelog entry}
```

Show ✅ / ⚠️ / 🔴 sections only if they have content.
If score < 70, append: "Score below 70 — recommend maintenance before new work."

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 可观测性参考

健康报告中0–100分数的指标定义。

---

## 计分公式

```
Score = completeness × 30%
      + test-coverage × 30%
      + freshness × 15%
      + (100 - debt-penalty) × 15%
      + consistency × 10%
```

**Lite档位重新分配**（无 `test-cases.xml`）：

```
Score = completeness × 50%
      + freshness × 25%
      + consistency × 25%
```

Lite档位不适用债务惩罚。

---

## 指标定义

### Completeness（完整度）0–100

衡量需求是否填写了所有必填字段。

```
completeness = (填写完整所有必填字段的需求数 / 活跃需求总数) × 100
```

每条需求的必填字段：`id`、`intent`（≤200字符）、`acceptance-criteria`（至少一条标准）、`risk-points`。已废弃需求不计入。

---

### Test Coverage（测试覆盖率）0–100

衡量每条验收标准是否至少有一个通过的测试覆盖。

```
test-coverage = (有≥1个通过测试的标准数 / 活跃标准总数) × 100
```

测试"覆盖"标准的条件：
1. 测试有 `covers="AC-NNN-N"` 指向该标准，且
2. 测试描述包含该标准文本中的至少一个关键词（关键词 = 长度≥4的非停用词）。

仅满足条件1不满足条件2 = 仅结构性关联（F6失败模式）。`status=failing` 或 `status=pending` 的测试不计入覆盖。

---

### Freshness（新鲜度）0–100

衡量memory文件相对于代码变更的更新及时性。

```
freshness = max(0, 100 - (距最新changelog条目天数 × 5))
```

- 0天 → 100（完全新鲜）
- 10天 → 50
- 20天 → 0

近期没有代码变更的项目，仍应有近期条目确认"无变更"。可使用 `type=maintenance` 条目。

---

### Debt Penalty（债务惩罚）0–100，从分数中扣除

衡量未解决的紧急修复债务的重量。

```
debt-penalty = min(100, 逾期条目数 × 15 + 未关闭条目数 × 5)
```

仅适用于standard和strict档位。上限100。

---

### Consistency（一致性）0–100

衡量跨文件的一致性，由审计中的F2、F3、F4、F9检查。

```
consistency = max(0, 100 - (活跃不一致数 × 20))
```

每个未解决的不一致扣20分。

---

## 分数解读

| 区间 | 含义 | 建议 |
|---|---|---|
| 85–100 | 健康 | 正常进行 |
| 70–84 | 可接受 | 下次机会小幅清理 |
| 50–69 | 退化 | 新功能前先处理 |
| < 50 | 严重 | 任何工作前先运行 `[skill-audit]` |
