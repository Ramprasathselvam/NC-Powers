---
name: figma-to-ios
description: Map a Figma design linked on a ticket to existing app design tokens/components, flagging gaps. Read-only. Does not generate final UI code.
activation: "Pull the Figma for <XR-KEY>", "map this design to our components", "what tokens does this screen use"
tools: [figma-mcp-read, filesystem-read]
gates: none
---

# figma-to-ios

## Purpose
Translate the design intent from a ticket's Figma link into concrete app design
tokens and existing components, so implementation reuses the design system
instead of hardcoding.

## Activation conditions
UI work where the ticket has a Figma/Spec link, after requirements. This is a
mapping step; it precedes but does not perform implementation.

## Inputs
- Figma URL (from jira-to-ios output or provided by the user).
- Target feature/screen.

## Workflow
1. Read the Figma frame via Figma MCP (read-only).
2. For each visual element, resolve to an existing token/component:
   - Colors → `Asset.<name>.color` (NC/NCI/Commons/SwiftGen/Assets.swift).
   - Fonts → `UIFont.<brand><weight>Font(ofSize:)` (UIFonts+NCI) or `FontFamily`.
   - Copy → correct feature `.strings` table + `L10n.<Table>.<key>`.
   - Components → existing `NC`/`NCI` component or `Custom` variant.
3. Produce a design-mapping note: element → token/component, plus a Gaps list for
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
- No Figma access / link missing → report; ask the user to attach the frame.
- Design value has no matching token → list it under Gaps for a human decision;
  do not fabricate.

## Required tools/MCP
Figma MCP — read scope. Filesystem read (to match existing tokens/components).

## Permission gates
None (read-only).
