# Research: Project Foundation

## Decision 1: Use standardized development dependency groups and a committed lockfile

**Decision**: Declare Ruff, ty, and pytest in one PEP 735 `dev` dependency
group, commit `uv.lock`, and use `uv sync --locked` and
`uv run --locked ...` in validation.

**Rationale**: The standard dependency-group table is supported directly by uv.
`--locked` rejects project metadata that disagrees with the lockfile instead of
silently changing the resolved environment.

**Alternatives considered**:

- `tool.uv.dev-dependencies`: rejected because it is legacy configuration.
- Multiple quality/test groups: deferred until partial environments provide a
  demonstrated benefit.
- Exact direct-dependency pins in `pyproject.toml`: rejected because the lockfile
  provides reproducibility while declared ranges retain maintainable intent.

**Sources**:

- <https://docs.astral.sh/uv/concepts/projects/dependencies/#development-dependencies>
- <https://docs.astral.sh/uv/concepts/projects/sync/>

## Decision 2: Keep runtime dependencies empty during foundation work

**Decision**: Do not add CLI, presentation, prompt, TUI, or TOML runtime libraries
until a product specification introduces code that uses them.

**Rationale**: Issue #1 establishes development infrastructure and explicitly
excludes product flows. Unused runtime packages increase installation cost and
security surface without delivering behavior.

**Alternatives considered**:

- Add all constitution-approved libraries immediately: rejected as premature
  dependency loading without product code.
- Create domain/application package skeletons: rejected because empty layers
  violate the constitution's purposeful-abstraction rule.

## Decision 3: Use current package metadata and an isolated build

**Decision**: Use `uv_build>=0.13.9,<0.14`, an SPDX
`license = "GPL-3.0-only"` expression, `license-files = ["LICENSE.md"]`, and
`uv build --no-sources`.

**Rationale**: The metadata follows current packaging standards, the backend range
avoids unreviewed compatibility changes, and `--no-sources` proves the package
does not depend on local uv source overrides.

**Alternatives considered**:

- Legacy table-form license metadata: rejected because current metadata standards
  use SPDX expressions and separate license-file declarations.
- License Trove classifiers: rejected because they are deprecated when SPDX
  license metadata is present.
- Editable-install validation: rejected because it does not prove the built wheel
  is self-contained.

**Sources**:

- <https://docs.astral.sh/uv/concepts/build-backend/>
- <https://docs.astral.sh/uv/concepts/projects/build/>
- <https://packaging.python.org/en/latest/guides/writing-pyproject-toml/#license-and-license-files>
- <https://packaging.python.org/en/latest/specifications/core-metadata/>

## Decision 4: Configure explicit, locked quality gates

**Decision**: Use Ruff with explicit rule families, Google docstring convention,
and `TID251` to prohibit `__future__.annotations` in project-authored code.
Use ty with Python 3.14 and `src/` roots. Use pytest 9 native TOML
configuration with strict mode.

**Rationale**: Explicit Ruff families do not silently expand when Ruff adds new
rules. TID251 enforces the constitution mechanically. ty understands the
`src/` layout and Python target. Locked pytest 9 makes native strict
configuration reproducible.

**Alternatives considered**:

- Ruff `ALL`: rejected because upgrades can silently add rules.
- Ruff FA100/FA102: rejected because those rules encourage the prohibited future
  import.
- Checking `.specify/`: rejected because it is managed third-party generated
  tooling, not project-authored Python.
- Arbitrary coverage threshold: rejected by the constitution in favor of
  behavior- and risk-driven tests.

**Sources**:

- <https://docs.astral.sh/ruff/linter/>
- <https://docs.astral.sh/ruff/rules/banned-api/>
- <https://docs.astral.sh/ty/reference/configuration/>
- <https://docs.pytest.org/en/stable/reference/customize.html>
- <https://docs.pytest.org/en/stable/reference/reference.html#confval-strict>

## Decision 5: Use a least-privilege, stable-check CI design

**Decision**: Trigger CI for pull requests and pushes to `main`. Grant
`contents: read`, pin actions to full SHAs, and create `quality`, a
Linux/macOS/Windows `test` matrix, `package`, and aggregate `CI` jobs.

**Rationale**: The matrix verifies platform-sensitive behavior, isolated jobs give
actionable failures, and a single stable aggregate context prevents branch
protection from depending on generated matrix names.

**Alternatives considered**:

- `pull_request_target`: rejected because ordinary CI must not run untrusted
  changes with elevated repository context.
- A single monolithic job: rejected because it hides platform and gate ownership.
- Requiring every matrix context in the ruleset: rejected as fragile when matrix
  names change.

**Sources**:

- <https://docs.astral.sh/uv/guides/integration/github/>
- <https://github.com/astral-sh/setup-uv/blob/main/README.md>
- <https://docs.github.com/en/actions/tutorials/build-and-test-code/python>
- <https://docs.github.com/en/actions/reference/security/secure-use>

## Decision 6: Treat issue forms as guided intake, not authorization

**Decision**: Provide `bug.yml`, `feature.yml`, and
`config.yml` with `blank_issues_enabled: false`. Public forms assign only
public report labels and never `development-task`, project metadata, or
organization-only issue types.

**Rationale**: Forms improve report quality, while labels and Project automation
preserve maintainer control. Forms are editable and API-created issues remain
possible, so authorization cannot depend on form content.

**Alternatives considered**:

- Public Development Task form: rejected because approved tasks are
  maintainer/agent controlled.
- Assign the public Project from forms: rejected because submitters may lack
  Project write permission.
- Use organization issue types: rejected because this is a user-owned repository.

**Sources**:

- <https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/syntax-for-issue-forms>
- <https://docs.github.com/en/enterprise-cloud@latest/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository>

## Decision 7: Protect only the default branch with a repository ruleset

**Decision**: Require a pull request, the aggregate `CI` check, an up-to-date
branch, resolved conversations, linear history, and squash merging on `main`.
Block force pushes and deletion. Require zero approvals and configure no normal
bypass actors.

**Rationale**: This enforces the approved solo-maintainer workflow without
requiring a second reviewer. Targeting only `main` allows merged feature
branches to be deleted automatically.

**Alternatives considered**:

- One required approval: rejected because it blocks a solo maintainer.
- Maintainer bypass: rejected because it weakens the normal required-check path.
- Protect all branches against deletion: rejected because it conflicts with
  automatic feature-branch cleanup.

**Sources**:

- <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets>
- <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>

## Decision 8: Link work through native development relationships

**Decision**: Create future remote implementation branches through
`gh issue develop`, keep issue checklists current, and include `Closes #N` in
pull-request bodies.

**Rationale**: Native links expose the branch and later the pull request in the
issue's Development section. The closing keyword completes the task only after
the default-branch merge.

**Alternatives considered**:

- Infer relationships only from branch names: rejected because GitHub does not
  expose that as an explicit development relationship.
- Close issues when a pull request opens: rejected because work is not Done until
  checks pass and the change merges.

**Sources**:

- <https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-a-branch-for-an-issue>
- <https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue>

## Decision 9: Deliver only meaningful foundation documentation

**Decision**: Add `AGENTS.md`, `CONTRIBUTING.md`, `DEVELOPMENT.md`,
`ISSUES.md`, `LICENSE.md`, `ROADMAP.md`, `CHANGELOG.md`, and README links.
Defer `GUIDE.md`, `HANDBOOK.md`, product documentation, and the documentation
website.

**Rationale**: The selected documents have complete foundation content now. User
and operator guides depend on product behavior that issue #1 explicitly excludes.

**Alternatives considered**:

- Empty placeholder product guides: rejected because they provide no reliable
  documentation.
- Include local planning notes: rejected because they are private, unapproved,
  and explicitly outside project history.
