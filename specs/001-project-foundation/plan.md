# Implementation Plan: Project Foundation

**Branch**: `feature/1-prepare-project-foundation` | **Date**: 2026-11-19 |
**Spec**: [spec.md](spec.md)

**Input**: Feature specification from `/specs/001-project-foundation/spec.md`

## Summary

Establish a reproducible, reviewable development foundation without implementing
Git worktree behavior. The change adds project governance and contributor
guidance, locked Python quality tooling, cross-platform automated checks, package
build and clean-install validation, structured public issue intake, and protected
issue-linked delivery through GitHub.

## Technical Context

**Language/Version**: Python 3.14 or later for project-authored code

**Primary Dependencies**: No runtime dependencies in this feature; uv and
uv_build for environment/build management; Ruff, ty, and pytest in the locked
development dependency group; GitHub Actions, Issues, Projects, and repository
rulesets as hosted integrations

**Storage**: Version-controlled Markdown, TOML, YAML, and lock files plus hosted
GitHub repository and Project settings; no application data store

**Testing**: Ruff format/lint, ty static analysis, pytest smoke tests, wheel and
source-distribution build, isolated wheel installation, package import, and
installed command startup

**Target Platform**: Development and validation on Linux, macOS, and Windows;
public GitHub repository

**Project Type**: Installable Python command-line package and repository
foundation

**Performance Goals**: A new contributor can locate the authoritative workflow
and begin the documented validation path within 10 minutes

**Constraints**: No worktree operations or product UI; no unused runtime
dependencies; GPL-3.0-only; project-authored Python excludes
`from __future__ import annotations`; managed `.specify/` tooling is outside
project-authored quality scope; local `docs/planning.md` remains untracked

**Scale/Scope**: One Python package, one development dependency group, three CI
operating systems, two public issue forms, one aggregate required CI check, and
one default-branch ruleset

## Constitution Check

*GATE: Passed before Phase 0 research and re-checked after Phase 1 design.*

- **Specification first**: PASS. The approved spec and clarification precede
  planning, tasks, and implementation.
- **Domain-centered OOP**: PASS. No product classes, domain packages, or worktree
  behavior are introduced by this foundation.
- **Contracts and dependency direction**: PASS. Foundation contracts document
  hosted and packaging behavior without creating hidden application dependencies.
- **Quality as delivery**: PASS. Ruff, ty, pytest, package build, and clean-install
  checks are mandatory and scoped to project-authored files.
- **Fast, cross-platform CLI**: PASS. The existing entry point receives only smoke
  validation; no interface framework is eagerly added or initialized.
- **Structural constraints**: PASS. Python 3.14+, uv, uv_build, English artifacts,
  GPL-3.0-only, and managed-tooling boundaries are explicit.
- **Workflow gates**: PASS. Issue checklist maintenance, linked remote branches,
  rebase pulls, squash merges, stable checks, and release guidance are covered.

Post-design re-check: PASS. The Phase 1 artifacts introduce no constitution
violation and require no complexity exception.

## Project Structure

### Documentation (this feature)

```text
specs/001-project-foundation/
├── checklists/
│   └── requirements.md
├── contracts/
│   ├── development-workflow.md
│   ├── issue-intake.md
│   ├── package-validation.md
│   └── quality-gates.md
├── data-model.md
├── plan.md
├── quickstart.md
├── research.md
├── spec.md
└── tasks.md                    # Created later by $speckit-tasks
```

### Repository Foundation

```text
.github/
├── ISSUE_TEMPLATE/
│   ├── bug.yml
│   ├── config.yml
│   └── feature.yml
├── workflows/
│   └── ci.yml
└── pull_request_template.md

src/
└── gforester/
    └── __init__.py             # Existing placeholder entry point remains

tests/
└── smoke/
    ├── test_cli_entry_point.py
    └── test_package_import.py

AGENTS.md
CHANGELOG.md
CONTRIBUTING.md
DEVELOPMENT.md
ISSUES.md
LICENSE.md
README.md
ROADMAP.md
pyproject.toml
uv.lock
```

**Structure Decision**: Keep the current single-package `src/` layout. Add only
foundation documents, hosted-service configuration, and smoke tests. Do not
create domain, application, infrastructure, CLI, interactive, or TUI packages
until a product specification requires them. `GUIDE.md`, `HANDBOOK.md`, the
documentation website, and product documentation content remain outside issue #1.

## Delivery Design

- Use one PEP 735 `dev` dependency group for Ruff, ty, and pytest. Commit
  `uv.lock` and require `--locked` for CI and documented validation.
- Configure project metadata with the SPDX expression `GPL-3.0-only` and
  `license-files = ["LICENSE.md"]`.
- Scope project-authored quality checks to `src/` and `tests/`; exclude managed
  `.specify/` tooling.
- Split CI into `quality`, a three-platform `test` matrix, `package`, and a
  stable aggregate `CI` job. Require only the aggregate check in the ruleset.
- Pin third-party actions to full commit SHAs with human-readable version comments
  and grant the workflow only read access to repository contents.
- Offer only Bug and Feature forms to public users. Disable ordinary blank issue
  creation and never assign `development-task` from public forms.
- Protect `main` with a ruleset requiring pull requests, an up-to-date branch,
  resolved conversations, linear history, squash merge, and the `CI` check.
  Require zero external approvals and allow no normal bypass actors.
- Configure the required `CI` context only after it has appeared on the feature
  pull request. Complete the ruleset before squash-merging that pull request.

## Complexity Tracking

No constitution violations or justified complexity exceptions are present.
