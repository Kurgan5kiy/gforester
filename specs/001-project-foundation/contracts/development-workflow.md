# Contract: Development Workflow

## Purpose

Define the observable relationship among an approved issue, its remote branch,
checklist, Project status, specification artifacts, and pull request.

## Start Work

1. The maintainer selects an approved `development-task` issue in `To do`.
2. The issue references the applicable approved specification.
3. A remote branch is created from current `main` through GitHub development
   tracking and follows `feature/<issue>-<description>` or
   `fix/<issue>-<description>`.
4. The branch is visible in the issue's Development section.
5. The issue moves to `In progress`.
6. Completed checklist items are checked; planned work remains unchecked.
7. Local work begins from the linked remote branch after `pull --rebase`.

## Submit Review

1. The branch is pushed without rewriting shared remote history.
2. The pull-request title follows the project commit-message convention.
3. The body includes:
   - `Closes #<issue>`;
   - links to the specification, plan, and tasks;
   - constitution-compliance confirmation;
   - relevant verification results.
4. The issue moves to `In review` when the pull request is ready.
5. No separate human approval is required while the project has one maintainer.
6. Every required check must pass before merge.

## Complete Work

1. Merge through GitHub using squash only.
2. Use the compliant pull-request title as the squash commit title.
3. Confirm the default-branch merge closed the issue.
4. Confirm the Project status is `Done`.
5. Confirm the merged source branch was deleted automatically.

## Release Delivery

1. Merge the original feature or fix into `main` first.
2. Create `deliver/<issue>-<description>` from the active release branch.
3. Apply the normal squash commit with `git cherry-pick -x`.
4. Open a separate pull request from the delivery branch to the release branch.
5. Squash-merge after the release checks pass.

## Failure Conditions

- The remote branch is absent or not linked to the issue.
- The issue checklist claims incomplete or unverified work is complete.
- The pull request omits issue/specification traceability.
- A required check is failed or missing.
- A non-squash merge path is offered or used.

