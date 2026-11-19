# Contract: Issue Intake

## Public Chooser

The public issue chooser exposes exactly:

1. **Bug Report**
2. **Feature Request**

Ordinary blank issue creation is disabled. GitHub may still expose a
maintainers-only blank option to users with elevated repository permissions.

## Bug Report Form

Required information:

- concise problem summary;
- observed behavior;
- expected behavior;
- reproduction steps;
- gforester version or commit;
- operating system and relevant environment information.

Optional information:

- logs or terminal output with secrets removed;
- additional context;
- willingness to help investigate.

The form assigns only the public `bug` label.

## Feature Request Form

Required information:

- problem or user need;
- desired outcome;
- concrete usage example;
- alternatives or current workaround.

Optional information:

- additional context;
- willingness to help design or implement.

The form assigns only the public `enhancement` label.

## Authorization Boundary

- Neither form assigns `development-task`.
- Neither form assigns the public Project directly.
- Neither form uses organization-only issue types.
- Form content is guidance, not a security boundary; reporters can edit generated
  bodies, and external interfaces can create unstructured issues.
- Only the maintainer or authorized agents may turn approved requirements into a
  `development-task`.

## Configuration

`.github/ISSUE_TEMPLATE/config.yml` sets
`blank_issues_enabled: false`. Contact links are omitted until a supported
community or security channel exists.

