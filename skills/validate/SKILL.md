---
name: validate
description: Run the project's quality gates (SwiftLint/Danger + unit tests) for a ticket's changes and report results, matching the CI PR gate. Does not modify source or touch git/Jira.
activation: "Validate <XR-KEY>"
tools: [terminal]
gates: [region-switch-confirm]
---

# validate

## Purpose
Run the same gates CI runs on a PR and report pass/fail plus the SwiftLint
error/warning counts needed for the PR template table.

## Activation conditions
Explicit command: `Validate <XR-KEY>`, after implement.

## Inputs
- XR ticket key. Assumes the intended brand/region is already configured; if a
  region switch is needed, that is gated (see below).

## Workflow
1. Confirm the target brand/region is configured. If it must change, use the
   verified env from workflow.md and gate it (configure is destructive — it runs
   `reset_git_repo(force: true)`).
2. Run `bundle exec fastlane lint` (SwiftLint) and/or `bundle exec fastlane
   danger_lint`.
3. Run `bundle exec fastlane unit_test` (scheme NCI, Debug, device "iPhone 17" —
   detect if that simulator is missing and report rather than failing blindly).
4. Report: lint pass/fail with before/after error/warning counts, and unit-test
   pass/fail with failing test names.

## Expected output
A validation report in chat (lint counts + test results). No source changes, no
git actions, no Jira changes.

## Constraints
- MUST NOT edit source or tests to force a pass.
- MUST NOT alter `.swiftlint.yml` to reduce counts.
- MUST NOT skip hooks, create commits/branches, or open PRs.
- On failure, report the root cause and hand back to implement — do not patch to
  hide the failure.

## Failure handling
- Build/test failure → report root cause; stop (do not auto-fix).
- "iPhone 17" simulator unavailable → report the mismatch and the available
  devices; do not silently substitute.
- Pods/SwiftGen out of date → report; suggest `pod install` /
  `xcodebuild -resolvePackageDependencies`.

## Required tools/MCP
Terminal.

## Permission gates
Lint/test runs need no gate (read-only to source). A brand/region `configure`
requires explicit confirmation and a clean working tree first (workflow.md).
