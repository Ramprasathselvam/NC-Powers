---
name: jira-to-ios
description: >-
  Turn an XR Jira ticket into a structured, iOS-oriented requirements brief
  before any code is written. Read-only.
activation:
  - "Start <XR-KEY>"
  - "requirements for <XR-KEY>"
  - "summarize <XR-KEY>"
  - "what does <XR-KEY> need"
tools:
  - jira-mcp-read
  - filesystem-read
gates: []
---

# jira-to-ios

## Purpose
Convert an XR Jira ticket into an actionable iOS requirements brief: goal,
acceptance criteria, ticket type, in/out of scope, brand/region applicability,
and the linked Figma/Spec URLs. This is the intake step — it produces
understanding, not code.

## Ticket format
Use `docs/jira-ticket-format.md` as the team standard. Supported ticket types:
- New Development / New Feature
- Existing Feature Development / Enhancement
- Bug
- Technical Change / Maintenance without UI
- UI-only Change

For UI work, the Figma link should be present in the Jira ticket. The workflow
must read the Figma URL from Jira instead of requiring the developer to paste it
again. If UI work has no Figma link, report the gap and ask whether to continue
without Figma.

## Efficient inputs
The Jira ticket is the source of truth. The user may additionally provide
known context to reduce discovery:

```text
Start XR-123
Known files:
- NC/NCI/Features/Garage/GarageViewModel.swift
Known symbols:
- GarageViewModel.loadVehicles()
```

Treat supplied paths/symbols as high-confidence starting points, not as a
complete change list. Validate them before use.

## Workflow
1. Fetch the ticket via Jira MCP (read-only).
2. Extract: summary, description, ticket type, acceptance criteria, attachments,
   and any Figma / Spec links.
3. Determine brand/region applicability (Nissan vs Infiniti; EUR/GOM/UKR/IND/AUS/
   Thai). Behavior is conditional in this codebase — see product.md; never assume
a feature is universal.
4. Identify whether the ticket is new development, existing-feature work, bug,
   technical/no-UI change, or UI change.
5. Produce a requirements brief in chat: Goal, Type, Acceptance Criteria,
   In Scope, Out of Scope, Brand/Region, Linked Figma/Spec, Known Files/Symbols,
   Open Questions.

## Expected output
A requirements brief in chat. No files created. Feeds figma-to-ios and
ios-architecture.

## Constraints
- Read-only. Do not transition the ticket, comment, assign, or edit any field.
- Do not write code or create branches.
- If acceptance criteria are missing, surface them as Open Questions — do not
  invent requirements.
- Do not ask the user to repeat a Figma URL that is already present in Jira.

## Failure handling
- Ticket not found / no access → report and stop.
- No acceptance criteria → list Open Questions rather than proceeding.
- Jira MCP unavailable → report; ask the user to paste ticket details.
- UI ticket has no Figma link → report the missing design input and ask whether to
  continue without Figma.

## Required tools/MCP
Jira (Atlassian) MCP — read scope only. Filesystem read (to relate to code).

## Permission gates
None (read-only). Jira writes are out of scope here; only jira-complete may
mutate Jira.
