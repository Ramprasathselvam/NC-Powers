---
name: jira-to-ios
description: Turn an XR Jira ticket into a structured, iOS-oriented requirements brief before any code is written. Read-only.
activation: "Start <XR-KEY>", "requirements for <XR-KEY>", "summarize <XR-KEY>", "what does <XR-KEY> need"
tools: [jira-mcp-read, filesystem-read]
gates: none
---

# jira-to-ios

## Purpose
Convert an XR Jira ticket into an actionable iOS requirements brief: goal,
acceptance criteria, in/out of scope, brand/region applicability, and linked
Figma/Spec URLs. This is the intake step — it produces understanding, not code.

## Activation conditions
User references an XR ticket to begin work, or explicitly asks to summarize a
ticket. Runs before implementation. Jira project prefix is XR
(acmsviseo.atlassian.net).

## Inputs
- Jira ticket key (XR-###). Optional: linked epic.

## Workflow
1. Fetch the ticket via Jira MCP (read-only).
2. Extract: summary, description, acceptance criteria, attachments, and any
   Figma / Spec links.
3. Determine brand/region applicability (Nissan vs Infiniti; EUR/GOM/UKR/IND/AUS/
   Thai). Behavior is conditional in this codebase — see product.md; never assume
   a feature is universal.
4. Produce a requirements brief in chat: Goal, Acceptance Criteria, In Scope,
   Out of Scope, Brand/Region, Linked Figma/Spec, Open Questions.

## Expected output
A requirements brief in chat. No files created. Feeds figma-to-ios and
ios-architecture.

## Constraints
- Read-only. Do not transition the ticket, comment, assign, or edit any field.
- Do not write code or create branches.
- If acceptance criteria are missing, surface them as Open Questions — do not
  invent requirements.

## Failure handling
- Ticket not found / no access → report and stop.
- No acceptance criteria → list Open Questions rather than proceeding.
- Jira MCP unavailable → report; ask the user to paste ticket details.

## Required tools/MCP
Jira (Atlassian) MCP — read scope only. Filesystem read (to relate to code).

## Permission gates
None (read-only). Jira writes are out of scope here; only jira-complete may
mutate Jira.
