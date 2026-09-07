---
name: figma-to-ios
description: >-
  Map a Figma design linked on a ticket to existing app design tokens/components,
  flagging gaps. Read-only. Does not generate final UI code.
activation:
  - "Pull the Figma for <XR-KEY>"
  - "map this design to our components"
  - "what tokens does this screen use"
tools:
  - figma-mcp-read
  - filesystem-read
gates: []
---

# figma-to-ios

## Purpose
Translate the design intent from the Jira ticket's Figma link into concrete
app design tokens and existing components, so implementation reuses the design
system instead of hardcoding.

## Source of Figma URL
For ticket-driven work, Jira is the source of truth for the Figma link. Read the
Figma URL from the Jira ticket output first. Do not ask the developer to provide
the URL again when it is already present in Jira.

If the developer explicitly supplies a Figma URL or frame/node ID, use it as an
additional optimization hint and validate that it corresponds to the ticket.

## Inputs
- Figma URL from Jira (preferred).
- Optional Figma frame/node ID supplied by the developer.
- Target feature/screen.
- Optional known implementation files/symbols from the user or prior stage.

## Workflow
1. Resolve the Figma URL from the Jira ticket.
2. Read the relevant Figma frame via Figma MCP (read-only).
3. If a frame/node ID is known, inspect that exact target first instead of
   broad Figma discovery.
4. For each visual element, resolve to an existing token/component:
   - Colors → `Asset.<name>.color` (NC/NCI/Commons/SwiftGen/Assets.swift).
   - Fonts → `UIFont.<brand><weight>Font(ofSize:)` (UIFonts+NCI) or `FontFamily`.
   - Copy → correct feature `.strings` table + `L10n.<Table>.<key>`.
   - Components → existing `NC`/`NCI` component or `Custom` variant.
5. Produce a design-mapping note: element → token/component, plus a Gaps list for
   anything with no existing token/component.

## Expected output
A design-mapping note in chat. No files. Feeds implement.

## Constraints
- Read-only; never modify Figma.
- Do NOT generate final SwiftUI/UIKit code (that is implement's job).
- Never invent a token/color/font not present in the repo — record it as a Gap.
- Remember tokens are app-layer only; the watch app and widgets use their own
  local tokens (see swiftui.md).

## Failure handling
- UI ticket has no Figma link in Jira → report; ask whether to continue without Figma.
- No Figma access → report; do not pretend the design was inspected.
- Design value has no matching token → list it under Gaps for a human decision;
  do not fabricate.

## Required tools/MCP
Figma MCP — read scope. Filesystem read (to match existing tokens/components).

## Permission gates
None (read-only).
