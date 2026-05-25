# Contributing

Thanks for considering a contribution to the project-memory skill.

## What this skill needs most

This skill is at v1.0.0 and has NOT yet been hardened by large-scale real-world use. The most valuable contributions right now are not new features — they are **evidence from real usage**:

- Did the writing style contract actually keep your docs lean, or did the agent still bloat them?
- Did the audit's keyword-based semantic check produce false alarms?
- Did the health score feel accurate for your project's actual state?
- Which workflow step was unclear or impossible to follow?

Open an issue with the `field-report` label describing what happened on a real project. One concrete field report is worth more than ten speculative feature requests.

## How to contribute

1. **Bug / unclear behavior** → open an issue using the bug template.
2. **Field report from real use** → open an issue using the field-report template.
3. **Doc fix or small improvement** → open a pull request directly.
4. **New feature or workflow change** → open an issue to discuss first, before writing code. This skill values consistency over feature count; not every good idea fits.

## Style rules for this repo

- All documentation files are bilingual: complete English first, then a divider, then complete Chinese. Match this format.
- The writing style contract in `SKILL.md` applies to the skill's own example content, not to explanatory prose in `SKILL.md` / `references/`.
- Keep `SKILL.md` under 500 lines for the English portion; push detail into `references/`.
- Bump the version in `MIGRATIONS.md` and add a `CHANGELOG.md` entry for any behavior change.

## Versioning

Semantic Versioning. Behavior-breaking changes bump the major version and require a migration entry in `MIGRATIONS.md`.

===============================================================================
中文版 CHINESE VERSION
===============================================================================

# 贡献指南

感谢你考虑为 project-memory skill 做贡献。

## 这个 skill 现在最需要什么

本 skill 处于 v1.0.0，**尚未经过大规模真实使用的打磨**。现在最有价值的贡献不是新功能，而是**来自真实使用的证据**：

- 文档风格契约真的让你的文档保持精简了吗？还是 agent 仍然写得啰嗦？
- 审计的关键词语义检查是否产生了误报？
- 健康分对你项目的实际状态来说准确吗？
- 哪个工作流步骤说不清楚或无法照做？

用 `field-report` 标签开一个 issue，描述在真实项目上发生了什么。**一份具体的实地报告，胜过十个臆想的功能请求。**

## 如何贡献

1. **Bug / 行为不清** → 用 bug 模板开 issue。
2. **真实使用的实地报告** → 用 field-report 模板开 issue。
3. **文档修正或小改进** → 直接开 pull request。
4. **新功能或工作流变更** → 先开 issue 讨论，再写代码。本 skill 看重一致性胜过功能数量；不是每个好主意都适合加进来。

## 本仓库的风格规则

- 所有文档文件都是双语：先完整英文，再分隔线，再完整中文。请遵循此格式。
- `SKILL.md` 中的文档风格契约适用于 skill 自身的示例内容，不适用于 `SKILL.md` / `references/` 中的讲解性散文。
- `SKILL.md` 的英文部分保持在 500 行以内；细节推到 `references/`。
- 任何行为变更都要在 `MIGRATIONS.md` 升级版本并在 `CHANGELOG.md` 加条目。

## 版本管理

语义化版本。破坏行为的变更升级主版本号，并需要在 `MIGRATIONS.md` 加迁移条目。
