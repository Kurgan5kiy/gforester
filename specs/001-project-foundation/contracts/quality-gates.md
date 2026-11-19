# Contract: Quality Gates

## Canonical Local Commands

```shell
uv sync --locked
uv run --locked ruff format --check .
uv run --locked ruff check .
uv run --locked ty check
uv run --locked pytest
uv build --no-sources
```

Each command must return exit code zero. Failures must retain the tool's
actionable diagnostic output.

## Scope

- Ruff and ty cover project-authored `src/` and `tests/`.
- Managed `.specify/` tooling is excluded.
- Tests cover package import and entry-point startup in issue #1.
- Product behavior tests are deferred until product behavior exists.

## CI Jobs

| Job | Platform | Required behavior |
|-----|----------|-------------------|
| `quality` | Linux | Format, lint, and type checks pass |
| `test` | Linux, macOS, Windows | Smoke tests pass on every matrix entry |
| `package` | Linux | Build and clean-install contract passes |
| `CI` | Hosted aggregate | All dependent jobs pass |

The default-branch ruleset requires only the stable `CI` context. A failed,
cancelled, skipped, or missing required dependency makes `CI` non-passing.

## Workflow Security

- Run on pull requests and pushes to `main`.
- Grant `contents: read` unless a documented job requires more.
- Do not use `pull_request_target` for ordinary code validation.
- Pin third-party actions to full commit SHAs and include readable version
  comments.
- Use the committed lockfile with `--locked`; CI must not update dependencies.

## Merge Readiness

A pull request is merge-ready only when:

- the branch is up to date with `main`;
- all conversations are resolved;
- the aggregate `CI` result passes;
- issue/specification traceability is present;
- no separate external approval is required under the current solo-maintainer
  policy.

