# gforester Constitution

## Core Principles

### I. Specification Before Implementation

Every feature, bug fix, and behavior-changing task MUST begin with an approved
specification that defines the intended outcome, requirements, success criteria,
and explicit exclusions.

Implementation MUST NOT begin until:

1. Ambiguities in the specification have been resolved.
2. A technical plan has been reviewed and approved.
3. Dependency-ordered implementation tasks have been generated.
4. The specification, plan, and tasks have passed consistency analysis.

Every implementation issue and pull request MUST reference its corresponding
Spec Kit artifacts. If implementation reveals an incorrect or incomplete
requirement, the specification and dependent artifacts MUST be updated before
the implementation continues.

This ordering ensures that code follows deliberate decisions instead of becoming
the source of undocumented product behavior.

### II. Domain-Centered Object-Oriented Design

The codebase MUST use object-oriented design where classes represent meaningful
domain, application, or infrastructure responsibilities. Classes MUST NOT be
introduced merely to wrap functions or create artificial layers.

Models MUST reside in dedicated model packages within their architectural
context. They MUST NOT be placed inside use-case, service, adapter, presenter,
or CLI modules.

Constants, enums, type aliases, and other shared definitions MUST reside in
dedicated modules or packages grouped by purpose. They MUST NOT be placed in a
class module merely because that class consumes them. Class modules MUST remain
focused on the primary class and its inseparable private implementation details.

Domain rules shared by multiple use cases or services MUST be implemented once
in an appropriate domain service. Duplicating domain or application logic is
prohibited; shared behavior MUST be moved to the lowest architectural layer that
can own it without depending on higher-level concerns.

Design patterns MUST address a concrete source of variation or complexity.
Strategy contracts and implementations MUST represent interchangeable behavior.
Facades MAY provide a stable entry point to a cohesive group of operations when
they simplify callers and reduce coupling.

One primary class per file is the default. Multiple classes MAY share a file only
when they form a small, inseparable implementation detail and separating them
would reduce readability or cohesion.

These rules preserve a discoverable structure, prevent duplicated behavior, and
keep object-oriented design purposeful rather than ceremonial.

### III. Explicit Contracts and Dependency Direction

Every project-authored interface contract MUST use `abc.ABC` and an uppercase
`I` prefix, such as `IWorktreeReader`. Concrete implementations MUST remain
replaceable behind those contracts.

Dependencies MUST be supplied through constructors when an object depends on an
external capability or interchangeable implementation. Object graph assembly
MUST occur in a dedicated composition root or application factory. Hidden global
dependencies and service locators are prohibited.

Dependencies MUST point toward domain and application policy. Infrastructure
adapters MAY depend on application contracts, but domain code MUST NOT depend on
CLI frameworks, terminal UI frameworks, configuration storage, or Git process
execution details.

These rules make business behavior independently testable and allow interfaces
to share the same use cases without duplicating policy.

### IV. Quality Is a Delivery Requirement

Every package `__init__.py` MUST contain a package-level docstring and an explicit
`__all__`. Every class and method MUST have a Google-style docstring describing
its contract and responsibility without restating the implementation. `Args`,
`Returns`, and `Raises` sections MUST be included whenever the corresponding
contract element exists.

All changed code MUST pass Ruff formatting and linting, ty type checking, and the
relevant pytest suite. New or changed behavior MUST have automated tests at the
lowest useful level, with integration coverage for Git subprocess boundaries,
configuration persistence, and complete CLI flows where applicable.

Test depth MUST follow behavioral risk. The project MUST NOT introduce an
arbitrary coverage percentage that rewards line execution without verifying
meaningful behavior.

Warnings, type errors, failing tests, undocumented public behavior, and copied
domain logic are delivery failures rather than optional cleanup. A pull request
MUST NOT merge while a required quality gate fails.

### V. Fast, Predictable, Cross-Platform CLI

Direct commands, guided prompts, and the full-screen terminal interface MUST use
the same application use cases and MUST produce equivalent domain outcomes.
Presentation layers MUST NOT reimplement worktree rules.

Commands MUST provide actionable errors, stable exit behavior, and output that is
clear in both interactive terminals and automation. Normal operations MUST avoid
unnecessary Git subprocesses, repeated repository scans, and eager initialization
of interfaces that the user did not request. A direct command MUST NOT load
Questionary or Textual unless their interface is invoked, and one operation MUST
NOT reload unchanged configuration without a demonstrated need.

Performance-sensitive paths MUST be measured before and after optimization so
that speed claims remain evidence-based rather than anecdotal.

Supported behavior MUST remain consistent on Linux, macOS, and Windows. Platform
differences MUST be isolated behind explicit contracts and verified by tests.

## Technical and Structural Constraints

- Project-authored Python code MUST support Python 3.14 or later and MUST use
  modern Python syntax directly. `from __future__ import annotations` MUST NOT be
  used in project-authored code. Unmodified third-party generated tooling is
  exempt from this rule and MUST remain identifiable as externally managed code.
- The package MUST be buildable as a wheel and installable through `pip`. `uv`
  and `uv_build` are the project environment and build tools.
- Typer is the direct command interface, Rich provides terminal presentation,
  Questionary provides guided prompts, and Textual provides the full-screen TUI.
- TOMLKit MUST be used for configuration serialization so user-authored TOML
  formatting and comments are preserved where practical. Global configuration
  belongs under `~/.config/gforester`; repository-specific configuration remains
  local to the repository. Commands that write settings MUST support explicit
  `--local` and `--global` scope selection and MUST default to local scope.
- User documentation and committed specifications MUST be written in English.
  Spec Kit artifacts are development records under `specs/`, not user-facing
  documentation.
- The project MUST be distributed under GPL-3.0-only.

## Development Workflow and Quality Gates

- Every independently deliverable change MUST have a GitHub issue before its
  implementation branch is created. Spec Kit task-list entries remain internal
  steps unless they require an independent deliverable and pull request.
- Feature branches MUST use `feature/<issue-number>-<kebab-description>` and fix
  branches MUST use `fix/<issue-number>-<kebab-description>`.
- Pulls MUST rebase. Pull requests MUST merge by squash. Merge commits and rebase
  merges into `main` are prohibited.
- Commit messages MUST start with a capital letter, contain one to three
  sentences, and end with a period.
- A release branch MUST be cut before work begins for the following release and
  MUST be named `release/<version>`.
- Work completed during an open release MUST merge into `main` first. If it is
  required in that release, a `deliver/<issue-number>-<kebab-description>` branch
  MUST be created from the active release branch. The squash commit from `main`
  MUST be applied with `git cherry-pick -x` and merged into the release branch
  through a separate squash pull request. `git cherry-pick -m` MUST NOT be used
  for this normal, non-merge squash commit.
- Agents MAY create and manage development issues through `gh` only from
  maintainer-approved specifications. Public issue intake MUST expose Bug and
  Feature forms but MUST NOT expose Development Task creation.
- Required checks include Ruff, ty, pytest, package build, and clean-install
  verification. Platform-sensitive behavior MUST be exercised on Linux, macOS,
  and Windows before release.
- A task moves to `In review` when its pull request is ready and to `Done` only
  after the squash merge and required verification succeed.

## Governance

This constitution is the highest-authority project development policy. Specs,
plans, tasks, contributor guidance, agent instructions, and implementation MUST
comply with it. When another project document conflicts with this constitution,
the constitution takes precedence.

Amendments MUST be proposed explicitly, reviewed by the maintainer, and recorded
in this file before dependent implementation proceeds. Each amendment MUST
include its reason, affected workflows or artifacts, and any migration required
for existing code or open work.

Constitution versions follow semantic versioning:

- MAJOR for removal or incompatible redefinition of a principle.
- MINOR for a new principle or materially expanded mandatory guidance.
- PATCH for clarification or wording changes that do not alter obligations.

Every specification and pull-request review MUST include a constitution
compliance check. Any justified exception MUST be documented in the relevant
plan and approved by the maintainer before implementation.

**Version**: 1.0.0 | **Ratified**: 2026-11-17 | **Last Amended**: 2026-11-18
