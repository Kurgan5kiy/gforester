# Quickstart: Validate the Project Foundation

This guide validates issue #1 from a clean checkout. It does not exercise
worktree product behavior.

## Prerequisites

- Git
- uv
- Python 3.14 or later
- GitHub CLI authenticated as the repository maintainer for hosted-setting checks

## 1. Prepare the locked environment

```shell
uv sync --locked
```

Expected result: uv reports a synchronized environment without changing
`uv.lock`.

## 2. Run project-authored quality checks

```shell
uv run --locked ruff format --check .
uv run --locked ruff check .
uv run --locked ty check
uv run --locked pytest
```

Expected result: every command exits successfully. Managed `.specify/` tooling
is outside the configured project-authored Python scope.

## 3. Build distributions

```shell
uv build --no-sources
```

Expected result:

- one source distribution under `dist/`;
- one `gforester-0.1.0-py3-none-any.whl` under `dist/`.

## 4. Verify a clean wheel installation

POSIX:

```shell
python3.14 -m venv /tmp/gforester-clean-install
/tmp/gforester-clean-install/bin/python -m pip install dist/gforester-0.1.0-py3-none-any.whl
/tmp/gforester-clean-install/bin/python -c "import gforester"
/tmp/gforester-clean-install/bin/gforester --help
```

PowerShell:

```powershell
$cleanEnv = Join-Path $env:TEMP "gforester-clean-install"
py -3.14 -m venv $cleanEnv
& "$cleanEnv\Scripts\python.exe" -m pip install "dist\gforester-0.1.0-py3-none-any.whl"
& "$cleanEnv\Scripts\python.exe" -c "import gforester"
& "$cleanEnv\Scripts\gforester.exe" --help
```

Expected result: installation, import, and command startup all exit successfully
without using the development environment.

## 5. Verify repository intake and tracking

In the GitHub UI:

1. Confirm the public issue chooser offers Bug Report and Feature Request.
2. Confirm ordinary public users are not offered a blank or Development Task
   form.
3. Confirm issue #1 is `In progress`.
4. Confirm its checklist reflects verified progress.
5. Confirm `feature/1-prepare-project-foundation` appears in Development.
6. Confirm development-task issues enter the public Project in `To do`.

## 6. Verify merge policy

Confirm repository settings allow squash merge only and automatically delete
merged branches.

After the feature pull request produces the aggregate `CI` check, confirm the
active default-branch ruleset:

- requires a pull request;
- requires the branch to be up to date;
- requires resolved conversations;
- requires linear history and squash merge;
- requires `CI`;
- blocks force pushes and deletion;
- requires zero approvals;
- has no normal bypass actor.

## 7. Confirm the scope boundary

Review the diff and confirm it introduces no Git worktree operations, interactive
product flow, full-screen product screen, package-index publication, or
documentation-site deployment.
