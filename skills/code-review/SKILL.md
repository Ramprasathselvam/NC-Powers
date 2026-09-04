---
name: code-review
description: Review a ticket's diff/PR against this repo's conventions and design intent. Read-only by default; posting to GitHub is gated.
activation: "Review PR for <XR-KEY>"
tools: [filesystem-read, github-cli, semantic-reviewer]
gates: [post-comment-confirm]
---

# code-review

## Purpose
Design- and convention-level review against the finalized steering: VIPER
boundaries, no hardcoded tokens, correct test style, brand/region gating, SOLID,
SwiftLint thresholds.

## Activation conditions
Explicit command: `Review PR for <XR-KEY>` (or a local diff pre-PR).

## Inputs
- PR number for the ticket, or a local diff.

## Workflow
1. Load the diff (PR via `gh`, or local diff).
2. Review by concern:
   - Correctness vs acceptance criteria.
   - Architecture: VIPER boundaries + module factory wiring (architecture.md).
   - Networking: correct stack (legacy vs OneID) and error handling.
   - Design system: `Asset`/`FontFamily`/`L10n` used, nothing hardcoded; tokens
     not used inside a Kit.
   - Accessibility identifiers present where expected.
   - Tests: correct style per target and mock naming (testing.md).
   - Brand/region gating respected; Swift version correct for the target.
   - SwiftLint thresholds (swift.md).
3. Produce findings grouped by concern with file:line references and severity.

## Expected output
A review report in chat. No source changes.

## Constraints
- Read-only by default. Do not modify code.
- Do not approve/merge or post comments to GitHub unless explicitly asked, and
  only behind the gate below.
- Flag real issues; do not rubber-stamp.

## Failure handling
- Diff too large → chunk by concern/file and review in parts.
- PR inaccessible → report and stop.

## Required tools/MCP
Filesystem read; `gh` CLI / GitHub MCP to read the PR; may delegate to
semantic-reviewer.

## Permission gates
Read/report needs no gate. Explicit confirmation before posting any comment or
approval to GitHub.
