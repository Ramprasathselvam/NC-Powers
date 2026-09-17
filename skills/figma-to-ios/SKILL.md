---
name: figma-to-ios
description: >-
  Analyze a Figma design linked on a ticket and map its structure, variables,
  visual properties, assets, and components to the existing iOS design system.
  Read-only. Does not generate or modify final UI code or Figma designs.
activation:
  - "Pull the Figma for <XR-KEY>"
  - "map this design to our components"
  - "what tokens does this screen use"
  - "analyze this Figma for iOS implementation"
  - "inspect the Figma design"
tools:
  - figma-mcp-read
  - filesystem-read
gates: []
---

# figma-to-ios

## Purpose
Translate Figma design intent into concrete implementation guidance for the existing
iOS codebase. Use the Figma MCP server as the source for design structure and
visual details, then map those details to existing NC/NCI/Commons design tokens,
components, typography, localization, and platform conventions.

This skill is a **read-only analysis/mapping stage**. It prepares reliable input
for the implementation skill; it does not write Swift/SwiftUI/UIKit code and does
not modify Figma.

## Source of Figma URL
For ticket-driven work, Jira is the source of truth for the Figma link. Read the
Figma URL from the Jira ticket output first. Do not ask the developer to provide
the URL again when it is already present in Jira.

If the developer explicitly supplies a Figma URL or frame/node ID, use it as an
additional optimization hint and validate that it corresponds to the ticket.

### Supported Figma URL information
Typical Figma design URLs have this form:

```text
https://figma.com/design/:fileKey/:fileName?node-id=1-2
```

Extract:
- `fileKey` → the path segment immediately after `/design/`
- `nodeId` → the `node-id` query parameter, for example `1-2`

For example:

```text
https://figma.com/design/kL9xQn2VwM8pYrTb4ZcHjF/DesignSystem?node-id=42-15
```

maps to:
- `fileKey = kL9xQn2VwM8pYrTb4ZcHjF`
- `nodeId = 42-15`

When using a Figma desktop MCP connection, the currently open Figma file may be
used by the MCP server, so the file key may not be required. Continue to use the
exact selected node ID when available.

## Inputs
- Figma URL from Jira (preferred).
- Optional Figma frame/node ID supplied by the developer.
- Target feature/screen.
- Optional known implementation files/symbols from the user or prior stage.

## Figma MCP workflow
Follow this sequence unless a step is genuinely unnecessary for the requested
analysis.

### 1. Resolve the exact target node
1. Read the Figma URL from Jira or the supplied developer context.
2. Extract `fileKey` and `nodeId`.
3. Prefer the exact frame/component node over broad file exploration.
4. Do not analyze a visually similar node when the ticket provides a specific node.

### 2. Fetch design context first
Use Figma MCP `get_design_context` for the target node.

This is the primary Figma MCP read operation and should be used before mapping
implementation details. Treat its returned code/structure as a **representation
of the design**, not as code to copy into the iOS project.

Use the returned context to identify:
- Layout hierarchy
- Auto Layout behavior
- Constraints and sizing
- Spacing and padding
- Typography
- Colors
- Component structure and variants
- Design tokens and styles
- Relevant image/icon assets

### 3. Recover from large or truncated responses
When `get_design_context` is too large, incomplete, or difficult to reason about:

1. Use `get_metadata` for the same node.
2. Use the sparse node map to identify the specific child nodes that matter.
3. Call `get_design_context` again for those child node IDs.
4. Repeat only for the required branches of the hierarchy.

Do not substitute `get_metadata` for design context when actual styling or component
properties are required; metadata is primarily for structure and node discovery.

### 4. Capture a visual reference
Use Figma MCP `get_screenshot` for the target node when visual comparison is useful
or when the design context does not fully communicate visual relationships.

Use the screenshot to validate:
- Overall hierarchy
- Alignment
- Relative spacing
- Visual grouping
- Image/icon placement
- Component variants
- Empty/disabled/selected states visible in the frame

Treat the screenshot as the visual reference, while using structured Figma context
for exact inspectable properties.

### 5. Inspect Figma variables and styles
Use Figma MCP `get_variable_defs` when variable/style information is needed.

Use it to identify:
- Color variables
- Typography styles
- Spacing variables
- Other reusable Figma design tokens

Do not blindly copy Figma variable names into the iOS project. Map them to the
existing app tokens after checking the repository.

### 6. Check Code Connect information when available
When the Figma MCP connection exposes Code Connect data, use:
- `get_code_connect_map` to find existing Figma → code mappings.
- `get_code_connect_suggestions` when a component has no known mapping and a
  code-level equivalent may exist.

Treat Code Connect as a strong signal for component identity, but still verify the
actual Swift/SwiftUI/UIKit implementation in the repository before reporting a
match.

This skill does not create or send new Code Connect mappings. Mapping changes are
outside this skill's read-only scope.

### 7. Inspect assets without inventing replacements
When the Figma MCP response contains image, SVG, or icon asset references:
- Record the asset and its intended usage.
- Prefer the existing app asset/component when the repository already has the
  equivalent.
- Do not invent a replacement icon or package.
- Do not add a new icon library merely because Figma returned an icon asset.
- If the asset cannot be mapped to an existing app asset, list it as a Gap.

If the MCP server provides a `localhost` asset URL, treat it as an MCP-served asset
source. Do not silently rewrite it into an unrelated external URL.

## Map Figma to the existing iOS project
After collecting Figma information, inspect the repository and map each relevant
design decision to the existing implementation patterns.

### Colors
Resolve colors to existing project tokens, for example:
- `Asset.<name>.color`
- NC / NCI / Commons color definitions
- SwiftGen-generated asset accessors

Never create a new color token only because Figma contains a different raw value.
If an exact or acceptable project token cannot be found, record the mismatch as a Gap.

### Typography
Map Figma typography to existing iOS typography definitions, for example:
- `UIFont.<brand><weight>Font(ofSize:)`
- `FontFamily`
- Existing SwiftUI typography modifiers/components

Check font family, weight, size, line height, and letter spacing where available.
Do not introduce a new font definition solely from Figma output.

### Components
Map Figma components and variants to existing:
- `NC` components
- `NCI` components
- Shared/Common components
- Existing feature-local custom components

Check the repository before deciding that a component is missing.

### Layout
Translate Figma layout intent into project concepts without producing final code.
Capture:
- Parent/child hierarchy
- Horizontal/vertical arrangement
- Fixed vs flexible dimensions
- Insets and spacing
- Alignment
- Corner radius
- Borders
- Shadows/elevation
- Scrolling behavior
- Responsive/constraint behavior

### Localization
For visible copy, map text to the correct feature localization table and key, for example:
- Feature `.strings` table
- `L10n.<Table>.<key>`

Do not assume Figma copy is already the exact production localization key.
Record missing localization keys as Gaps.

### Platform boundaries
Remember that token/component availability may differ by target. App-layer tokens
and components must not be assumed to exist in the watch app or widgets; check the
project guidance and implementation target before mapping.

## Expected output
Return a **design-mapping note in chat** with enough detail for the implementation
stage to work without re-reading the whole Figma design.

Recommended structure:

### Figma target
- File key
- Node ID
- Frame/component name

### Design structure
- Screen hierarchy
- Important child nodes
- Layout behavior

### Token mapping
| Figma property | Figma value/variable | Existing iOS token | Status |
|---|---|---|---|

### Component mapping
| Figma component/variant | Existing iOS component | Location | Status |
|---|---|---|---|

### Typography
- Figma font/style
- Existing iOS equivalent

### Localization
- Visible copy
- Existing localization key/table

### Assets
- Asset/node
- Existing app asset/component
- Gap when unavailable

### Gaps
Only list genuine gaps, conflicts, or decisions requiring human input.

## Constraints
- Read-only; never modify Figma.
- Do not create or send Code Connect mappings.
- Do not generate final SwiftUI/UIKit/Swift code.
- Do not fabricate project tokens, components, fonts, localization keys, or assets.
- Treat Figma MCP generated React/Tailwind output as a design representation, not
  as final project code.
- Reuse existing project design-system definitions whenever possible.
- Never replace a repository lookup with an assumption from the Figma name alone.
- Keep the analysis focused on the requested node/screen; avoid unnecessary file-wide
  Figma exploration.

## Failure handling
### Jira has no Figma link
Report that the ticket has no Figma URL. Ask whether to continue without Figma
rather than inventing a design source.

### Figma MCP is unavailable
Report that Figma MCP access is unavailable. Do not claim that the design was inspected
and do not infer exact visual properties from the ticket description alone.

### Invalid or incomplete Figma URL
Report which required information is missing (`fileKey` and/or `nodeId`) and use a
known supplied node ID when possible.

### `get_design_context` is truncated
Use `get_metadata`, identify relevant child nodes, and fetch smaller design-context
requests.

### No matching iOS token/component
List the design value and the closest repository evidence under **Gaps**. Do not
invent a token or implementation.

### Figma and project tokens differ
Report both values. Prefer the existing project's design-system token for eventual
iOS implementation unless the developer explicitly requests a new token decision.
Do not silently change the project's design system during this analysis stage.

## Required tools/MCP
### Figma MCP read operations
- `get_design_context` — primary design inspection
- `get_metadata` — hierarchy/node discovery and recovery from large responses
- `get_screenshot` — visual reference/validation
- `get_variable_defs` — variables and styles/token inspection
- `get_code_connect_map` — existing Figma-to-code mapping lookup, when available
- `get_code_connect_suggestions` — candidate component mappings, when available

### Repository read operations
- Filesystem/read tools to locate existing tokens, components, typography,
  localization, and assets.

Do not require write-capable Figma tools for this skill.

## Permission gates
None (read-only).

## Source alignment
This workflow follows the Figma MCP server guidance for extracting `fileKey`/`nodeId`,
fetching design context, recovering through metadata, capturing screenshots, inspecting
variables, and reusing project design-system conventions. Figma MCP guidance also
emphasizes that generated framework-specific output is a starting representation rather
than final project code.
