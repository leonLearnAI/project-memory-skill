# Skill Migrations

This file documents migration steps between skill versions. When a project's `.skill-version` differs from the current skill version, the agent reads this file to determine what changes are needed.

---

## Current Version: 1.0.0

Initial release. No migrations needed.

---

## Migration Template

When releasing a new version, add an entry here following this format:

```markdown
## X.Y.Z → X.Y.(Z+1)

**Release date**: YYYY-MM-DD

**Changes**:
- {what changed in the skill}

**Migration steps**:
1. {step 1}
2. {step 2}

**Compatibility**:
- Breaking: {yes/no}
- Auto-applicable: {yes/no — if no, requires user decisions}

**Rollback**:
- {how to revert to previous version, if possible}
```

---

## Migration Principles

1. **Migrations are additive when possible.** New required fields → start as optional with default values, become required in next major version.

2. **Never silently delete user data.** If a field is removed, move it to an archive note.

3. **Major version bumps require explicit user approval.** Minor and patch can run with a single confirmation.

4. **Test migrations on a backup first.** Skill always backs up `.project-memory/` before running migrations.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# Skill 版本迁移

本文件记录 skill 版本间的迁移步骤。当项目的 `.skill-version` 与当前 skill 版本不同时，agent 读取此文件以确定所需变更。

---

## 当前版本：1.0.0

初始版本。无需迁移。

---

## 迁移模板

发布新版本时，按以下格式添加条目：

```markdown
## X.Y.Z → X.Y.(Z+1)

**Release date**: YYYY-MM-DD

**Changes**:
- {skill 中变更了什么}

**Migration steps**:
1. {步骤1}
2. {步骤2}

**Compatibility**:
- Breaking: {yes/no}
- Auto-applicable: {yes/no — 如果 no，需要用户决策}

**Rollback**:
- {如何回退到上一版本，如果可能}
```

---

## 迁移原则

1. **迁移尽量是追加式。** 新增必填字段 → 先作为可选 + 默认值，下个大版本再变必填。

2. **永不静默删除用户数据。** 字段移除时，搬到归档备忘。

3. **大版本升级需要明确用户批准。** 小版本和补丁可单次确认。

4. **迁移先在备份上测试。** Skill 在运行迁移前总是备份 `.project-memory/`。
