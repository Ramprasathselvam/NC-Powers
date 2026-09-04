# Jira Ticket Format

This guide defines the minimum information expected in Jira tickets so `NC-Powers` can process them efficiently and consistently.

## General rules

Every ticket should answer:

1. What is changing?
2. Why is it changing?
3. Where is it changing?
4. What is the expected behavior?
5. How do we know it is complete?

### Figma rule

For UI work, the **Figma link must be added to the Jira ticket**. Do not require developers to provide a separate Figma link when running `Implement <XR-KEY>`.

The Power should read the Figma link from Jira and use it as the design source. If the ticket requires UI work but no Figma link is present, report the gap and ask the user whether to continue without Figma.

### Efficient context

When using commands, developers can optionally provide known implementation context to reduce discovery time:

```text
Implement XR-123

Known files:
- NC/NCI/Features/Garage/GarageViewController.swift
- NC/NCI/Features/Garage/GarageViewModel.swift

Known symbols:
- GarageViewModel.loadVehicles()
```

Known paths/symbols are optimization hints, not proof that no other files are required. The Power validates the paths, reads them first, and searches only for missing dependencies.

---

## 1. New Development / New Feature

Use when introducing functionality that does not currently exist.

### Jira title

`[Feature] <Short feature name>`

### Required sections

```markdown
## Type
New Development

## Summary
<One or two sentences describing the feature.>

## Business / User Requirement
<Why this feature is needed and who uses it.>

## Scope
### In Scope
- <item>
- <item>

### Out of Scope
- <item>

## Expected Behavior
1. <step>
2. <step>
3. <step>

## UI / Figma
Figma: <Figma URL>

## Acceptance Criteria
- [ ] <observable requirement>
- [ ] <observable requirement>
- [ ] Loading state is handled.
- [ ] Success state is handled.
- [ ] Empty/error state is handled where applicable.
- [ ] Localization is supported.
- [ ] Accessibility is supported.

## API / Data
- Endpoint: <if applicable>
- Request: <if applicable>
- Response/model: <if applicable>

## Dependencies
- <related Jira ticket / backend dependency / feature flag>

## Notes
<Anything important for implementation.>
```

---

## 2. Old / Existing Feature Development

Use when extending or modifying an existing feature/module.

### Jira title

`[Enhancement] <Existing feature> - <change>`

### Required sections

```markdown
## Type
Existing Feature Development

## Existing Behavior
<What the application does today.>

## Requested Change
<Exactly what should change.>

## Expected Behavior After Change
<New expected behavior.>

## Impacted Feature / Module
<Feature/module/screen name.>

## Known Files (Optional)
- <path>
- <path>

## UI / Figma
Figma: <Figma URL if UI changes>

## Acceptance Criteria
- [ ] Existing behavior remains unchanged where not explicitly modified.
- [ ] <new behavior>
- [ ] Regression scenario is covered.

## Dependencies
<Related tickets/API/backend/feature flags.>

## Notes
<Migration or backward-compatibility considerations.>
```

If there is no UI change, write `Figma: N/A - No UI change` rather than forcing a Figma link.

---

## 3. Bug Fix

Use when correcting behavior that is not working as intended.

### Jira title

`[Bug] <Short problem description>`

### Required sections

```markdown
## Type
Bug

## Problem
<What is wrong?>

## Steps to Reproduce
1. <step>
2. <step>
3. <step>

## Actual Behavior
<What happens today?>

## Expected Behavior
<What should happen?>

## Affected Environment
- Brand: <Nissan / Infiniti / both>
- Region: <region or N/A>
- iOS: <version/range if relevant>
- App version/build: <if known>

## Evidence
<Screenshot/video/log/error message if available.>

## UI / Figma
Figma: <Figma URL if the fix changes UI>

## Acceptance Criteria
- [ ] Reproduction steps no longer reproduce the issue.
- [ ] Expected behavior works.
- [ ] Existing behavior is not regressed.
- [ ] Relevant regression test is added/updated where practical.

## Known Files (Optional)
- <path>

## Notes
<Additional technical context.>
```

For a visual bug, Figma is strongly recommended. For a non-UI bug, use `Figma: N/A - No UI change`.

---

## 4. Change / Maintenance Without UI

Use for technical changes, refactoring, API/model changes, configuration, performance, logging, test-only changes, dependency updates, or other work with no visual design requirement.

### Jira title

`[Tech] <Short technical change>`

### Required sections

```markdown
## Type
Technical Change / No UI

## Summary
<What technical change is required?>

## Reason
<Why is this change required?>

## Current Behavior / Implementation
<Relevant current behavior or limitation.>

## Requested Change
<Exact technical outcome.>

## Impacted Area
<Module/package/file/API/test area.>

## Known Files (Optional)
- <path>

## Acceptance Criteria
- [ ] <technical outcome>
- [ ] Existing behavior remains intact where required.
- [ ] Tests are updated/added where applicable.
- [ ] No UI change.

## UI / Figma
Figma: N/A - No UI change

## Dependencies
<Related tickets or technical dependencies.>

## Notes
<Implementation constraints, compatibility requirements, performance target, etc.>
```

---

## 5. UI-only Change

Use when the main purpose is visual/layout/content change to an existing screen.

### Jira title

`[UI] <Screen> - <visual change>`

Required minimum:

```markdown
## Type
UI Change

## Current UI
<Current behavior.>

## Requested UI
<Expected visual/interaction behavior.>

## Figma
Figma: <Figma URL>

## Acceptance Criteria
- [ ] Matches Figma in light mode.
- [ ] Matches Figma in dark mode where applicable.
- [ ] Existing design-system tokens/components are reused.
- [ ] Localization is supported.
- [ ] Accessibility is supported.
```

---

## 6. Recommended Jira ticket metadata

Where the Jira project supports these fields, use them consistently:

| Field | Recommendation |
|---|---|
| Type | Feature / Story / Bug / Task according to team Jira configuration |
| Title | Prefix with `[Feature]`, `[Enhancement]`, `[Bug]`, `[Tech]`, or `[UI]` |
| Priority | Set based on user/business impact |
| Assignee | Developer/owner when known |
| Labels | `ios`, `swift`, `swiftui`, `uikit`, plus feature/area labels |
| Brand | Nissan / Infiniti / Both when applicable |
| Region | Explicit region or `Global` |
| Figma | Put the link in the Jira ticket for UI work |
| Dependencies | Link related Jira tickets |
| Acceptance Criteria | Always include observable completion criteria |

## 7. What `NC-Powers` should do with each ticket

The workflow should first classify the ticket and then select the applicable discovery:

| Ticket type | Jira | Figma | Architecture | Tests | UI validation |
|---|---|---|---|---|---|
| New Development | Required | Required if UI | Required | Required | If UI |
| Existing Feature | Required | Required if UI | Required | Required | If UI |
| Bug | Required | If visual/UI bug | Required | Regression-focused | If UI |
| Tech / No UI | Required | N/A | Required | Required where applicable | No |
| UI Change | Required | Required | Required | Relevant interaction tests | Required |

## 8. Good vs poor ticket

### Good

A good ticket is specific enough that a developer can understand the expected outcome without guessing, and the Power can identify the relevant design, architecture, tests, and acceptance criteria directly from Jira.

### Poor

Avoid tickets such as:

```text
Fix garage screen
Update battery feature
Change API
Make it like Figma
```

These require excessive discovery and leave important behavior ambiguous.

Prefer:

```text
[Enhancement] Garage - Show charging status in vehicle card

Existing Behavior:
Vehicle cards show vehicle name and battery percentage.

Requested Change:
Show charging status below battery percentage when the vehicle is charging.

Figma:
<URL>

Acceptance Criteria:
- [ ] Charging vehicle shows charging status.
- [ ] Non-charging vehicle keeps the current layout.
- [ ] Loading/empty/error states remain unchanged.
- [ ] Light and dark modes match Figma.
- [ ] Accessibility identifier is available.
```

## 9. Rule for developers using NC-Powers

Use the simplest command for the required stage:

```text
Start XR-123
Implement XR-123
Validate XR-123
Create PR for XR-123
Review PR for XR-123
Complete XR-123
```

If you already know the exact files, symbols, test files, or PR number, provide them with the command. This reduces unnecessary repository discovery, but the Power still validates the supplied context and discovers missing dependencies when required.
