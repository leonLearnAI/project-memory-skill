# Changelog

All notable changes to the **project-memory skill** are documented here.
This file tracks the evolution of the skill itself, not the projects it manages.

Format based on [Keep a Changelog](https://keepachangelog.com/).
This project adheres to [Semantic Versioning](https://semver.org/).

本文件记录 **project-memory skill 本身** 的版本变更，不是它所管理的项目的变更。

---

## [Unreleased]

Changes collected from real-world use, pending the next release.
从真实使用中收集的变更，等待下次发布。

---

## [1.1.0] - 2026-05-25

### Fixed
- `marketplace.json` plugin source format: replaced unsupported string `"."` with the required object format `{"source": "url", "url": "..."}`, enabling `claude plugin install project-memory` to work correctly.

### Changed
- Added `version` field to the plugin entry in `marketplace.json`.

---

## [1.0.0] - 2026-05-21

Initial release.

### Added
- Three project tiers (lite / standard / strict).
- Six atomic workflows (A New Requirement, B Bug Fix, C Onboarding, D Audit, E Deprecation, F Hotfix).
- Ten failure modes (F1–F10) with detection and recovery procedures.
- Writing style contract with hard per-file limits and anti-bloat merge rules.
- Acceptance-criteria-as-contract mechanism linking requirements to tests via `covers`.
- Observability: health report and 0–100 score, with lite-tier redistribution.
- Five manual commands (`[skill-init]`, `[skill-audit]`, `[skill-status]`, `[skill-upgrade]`, `[skill-feedback]`).
- Evolution mechanism: tier upgrade and skill self-upgrade via `MIGRATIONS.md`.
- Git integration guidance and `.gitignore` recommendations.
- Bilingual documentation (English then Chinese in every file).

### Known limitations
- Soft contract: cannot physically prevent agents from skipping workflows.
- Semantic test coverage is keyword-based, not understanding-based.
- Multi-agent locking is advisory only.
- Document bloat relies on the style contract; not auto-detected.

[Unreleased]: https://github.com/leonLearnAI/project-memory-skill/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/leonLearnAI/project-memory-skill/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/leonLearnAI/project-memory-skill/releases/tag/v1.0.0
