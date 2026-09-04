# NC-Powers Workflow

`NC-Powers` implements a gated development lifecycle for the NissanConnect iOS project:

```text
Start / Requirements
        ↓
      Figma
        ↓
 Architecture Discovery
        ↓
    Implement
        ↓
     Validate
        ↓
    Create PR
        ↓
   Review PR
        ↓
     Complete
```

## Commands

### `Start <XR-KEY>`
Read the Jira ticket and produce the requirements brief. This stage is read-only.

### `Implement <XR-KEY>`
Runs requirements/design/architecture discovery as needed, confirms the implementation approach, writes source and tests, records evidence, and stops.

It must not automatically validate, create a branch, create a PR, review a PR, or complete Jira.

If Jira is `To Do`, the workflow asks for explicit approval before moving it to `In Progress`.

### `Validate <XR-KEY>`
Runs the applicable build, test, lint, acceptance-criteria, design, navigation/state, localization, accessibility, and related quality checks. It reports actual results only.

A passing validation does not authorize a Jira transition automatically.

### `Create PR for <XR-KEY>`
Preconditions include a successful validation. Branch creation, commit, and push/PR creation are separately gated. The workflow never pushes directly to protected branches and never merges.

### `Review PR for <XR-KEY>`
Reviews the actual PR diff against project architecture, design-system, testing, brand/region, accessibility, and linting rules. Reporting is read-only by default. Posting GitHub comments or approval requires explicit approval.

### `Complete <XR-KEY>`
Confirms the PR state and asks for explicit approval immediately before the Jira `In Review → Done` transition. The completion comment and transition are Jira writes and must not be inferred.

## Approval model

Approval is action-specific.

```text
Yes to Jira transition   ≠ Yes to file writes
Yes to file writes        ≠ Yes to commit
Yes to commit             ≠ Yes to push/PR
Yes to review             ≠ Yes to GitHub post
Yes to one Jira status    ≠ Yes to another Jira status
```

When approval is denied, the current command stops without performing the gated action.

## Evidence model

The workflow only reports evidence that was actually observed in the current execution. It must not invent:

- build or test results
- screenshots
- Jira comments or transitions
- pull-request links
- review findings
- lint counts
- validation outcomes

## Jira writes

`jira-complete` is the only Skill that may mutate Jira. Other Jira interaction is read-only.

## Git/GitHub writes

`pr-create` owns branch/commit/push/PR creation. `code-review` may post review output only after explicit approval. No Skill may silently merge a pull request.

## Project Steering

The `steering/` directory in this repository contains NissanConnect-specific team/project knowledge used by the Power and Skills. It includes architecture, product, design-system, Swift/SwiftUI/UIKit, testing, and workflow rules.

These files are intentionally kept with `NC-Powers` because this repository is maintained specifically for the current NissanConnect iOS project/team.
