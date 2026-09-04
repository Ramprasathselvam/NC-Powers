---
name: pr-create
description: Create the branch, commit, and PR for a ticket following the repo's git + PR-template conventions. Every git write is gated.
activation: "Create PR for <XR-KEY>"
tools: [terminal, github-cli]
gates: [branch-confirm, commit-confirm, push-pr-confirm]
---

# pr-create

## Purpose
Raise a PR for validated changes following BRANCH.md / CONTRIBUTING.md and the
pull_request_template.md.

## Activation conditions
Explicit command: `Create PR for <XR-KEY>`, only after validate has passed.

## Inputs
- XR ticket key, feature/base context, validation results (lint counts, test
  status), Jira/Figma/Spec links, light+dark validation proof.

## Workflow
1. Precondition: validate has run and passed. If not, refuse and name the missing
   step.
2. Inspect the working tree; stage only intended files (never `git add .`). Flag
   any `.env*`/credential-like file before staging.
3. Create branch `features/xxxx/<XR-KEY>` from the correct base (feature base or
   develop per BRANCH.md). [GATE: branch-confirm]
4. Commit with emoji + present-tense imperative + `[<XR-KEY>]`. [GATE: commit-confirm,
   shows staged files + message]
5. Push with `-u` and open a PR titled `[<XR-KEY>] <desc>`, filling the template:
   Jira link, Spec link, Figma link, before/after validation proof (light & dark),
   SOLID checklist, and the SwiftLint error/warning before/after count table.
   [GATE: push-pr-confirm]

## Expected output
A pushed branch and an open PR URL. Reports the branch and PR link.

## Constraints
- MUST NOT push to master/develop/release directly.
- MUST NOT force-push, amend pushed commits, or skip hooks.
- MUST NOT commit secrets/.env files.
- MUST NOT merge the PR or transition Jira.

## Failure handling
- Working tree has unrelated/secret files → halt and report.
- No upstream/auth, or branch already exists → report and stop.
- validate not run/failed → refuse until green.

## Required tools/MCP
Terminal + `gh` CLI (or GitHub MCP).

## Permission gates
Explicit confirmation before branch creation, before commit (show staged files +
message), and before push/PR. Flag secret-looking files before staging.
