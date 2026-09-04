---
name: jira-complete
description: Close out an XR ticket — transition status and add a completion comment linking the PR. The only skill allowed to mutate Jira. Gated.
activation: "Complete <XR-KEY>"
tools: [jira-mcp-write]
gates: [jira-write-confirm]
---

# jira-complete

## Purpose
Final step: update the XR ticket's status and add a completion comment with the
PR link.

## Activation conditions
Explicit command: `Complete <XR-KEY>`, after the PR exists (and typically merged).

## Inputs
- XR ticket key, target transition/status, PR link, short summary.

## Workflow
1. Read the ticket's available transitions.
2. Confirm the target status with the user (the transition is a Jira write).
3. Add a completion comment linking the PR, then apply the transition. [GATE]

## Expected output
The ticket transitioned + a comment added. Reports what changed.

## Constraints
- MUST NOT transition without an explicit user-confirmed target status.
- MUST NOT edit unrelated fields or other tickets.
- MUST NOT close a ticket whose PR is not merged unless the user says so.
- This is the ONLY skill that writes to Jira; every other skill is read-only there.

## Failure handling
- Invalid transition for the workflow → report valid transitions and stop.
- No permission → report and stop.
- Ambiguous target status → ask; do not assume.

## Required tools/MCP
Jira (Atlassian) MCP — write scope.

## Permission gates
Explicit confirmation before any Jira write (transition or comment).
