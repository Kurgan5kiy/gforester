# Data Model: Project Foundation

The foundation introduces workflow concepts rather than runtime application
models. They are represented by repository artifacts and hosted-service state;
no application persistence layer is required.

## GovernanceArtifact

Represents an approved source of project intent or delivery policy.

### Attributes

- **kind**: constitution, specification, plan, tasks, checklist, or guidance
- **path**: stable repository-relative location
- **language**: English for committed project guidance and specifications
- **approval_state**: draft or approved
- **version**: required for the constitution; optional for other artifacts
- **related_issue**: issue number governing the change

### Validation Rules

- Private local planning notes are never GovernanceArtifact instances.
- An implementation may begin only when its specification, plan, and tasks are
  approved and mutually consistent.
- The constitution has precedence over conflicting artifacts.

## DevelopmentTask

Represents one independently deliverable, maintainer-approved change.

### Attributes

- **issue_number**: unique repository issue number
- **classification**: `development-task`
- **project_status**: To do, In progress, In review, or Done
- **specification_links**: one or more durable artifact references
- **remote_branch**: published feature or fix branch
- **branch_link_state**: unlinked or linked
- **checklist_state**: ordered verified/incomplete progress items
- **pull_request**: optional pull-request reference
- **required_check_state**: pending, passed, or failed

### State Transitions

```text
To do -> In progress -> In review -> Done
```

- `To do -> In progress`: work is taken, and the remote branch is linked.
- `In progress -> In review`: the pull request is ready and required checks run.
- `In review -> Done`: required checks pass and the pull request is
  squash-merged.
- A failed or missing required check prevents `Done`.
- Checklist items become complete only after their result is verifiable.

## QualityGate

Represents one required category of change validation.

### Attributes

- **name**: stable human-readable check name
- **category**: format, lint, type, test, package, or clean-install
- **platform_scope**: Linux, macOS, Windows, or platform-independent
- **required**: whether merge readiness depends on the result
- **result**: pending, running, passed, or failed
- **diagnostic**: actionable failure information

### State Transitions

```text
pending -> running -> passed
                   -> failed
```

- A missing required result is equivalent to a non-passing result.
- The aggregate `CI` result passes only when every required dependency passes.

## PublicReport

Represents unapproved public intake.

### Attributes

- **report_type**: Bug or Feature
- **reporter**: GitHub user
- **form_responses**: structured answers supplied by the reporter
- **public_labels**: non-authorizing labels such as `bug` or `enhancement`
- **development_authority**: always false at creation

### Validation Rules

- Public forms never assign `development-task`.
- An API-created or edited issue does not gain implementation authority from its
  title or body.
- Only the maintainer or an authorized agent may classify approved work as a
  DevelopmentTask.

## DistributionArtifact

Represents a build output validated independently from the source checkout.

### Attributes

- **project_name**: `gforester`
- **project_version**: package version
- **artifact_type**: source distribution or pure-Python wheel
- **path**: exact built artifact path
- **build_result**: pending, built, or failed
- **install_result**: pending, verified, or failed
- **smoke_results**: import and command-start outcomes

### State Transitions

```text
planned -> built -> verified
        -> failed
built   -> failed
```

- Verification uses a clean Python 3.14 environment.
- Editable installation cannot satisfy clean-install verification.
