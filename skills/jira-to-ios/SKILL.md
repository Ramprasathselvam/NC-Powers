---
name: jira-to-ios
description: >-
  Turn a Jira ticket (usually given as a key, e.g. XR-123) into a structured,
  iOS-oriented requirements brief for the (NC/NCI) app before any code
  is written. Read-only intake stage. Does not write code, branches, or Jira changes.
activation:
  - "Start <XR-KEY>"
  - "requirements for <XR-KEY>"
  - "summarize <XR-KEY>"
  - "what does <XR-KEY> need"
  - "begin development for <XR-KEY>"
tools:
  - jira-mcp-read
  - filesystem-read
gates: []
---

# jira-to-ios

## Purpose
Convert a Jira ticket into an actionable iOS requirements brief for the
(NC/NCI) app: goal, ticket type, acceptance criteria, in/out of scope,
brand/region applicability, linked design/spec references, and the likely
implementation area in this repository. This is the **intake step** — it produces
understanding, not code.

The typical trigger is a developer handing over a Jira key (for example
`Start XR-123`). The ticket key is the entry point; Jira is the source of truth.

## Relationship to other skills
This skill is read-only intake and feeds the later stages:
- **figma-to-ios** — owns all Figma/design analysis and token/component mapping.
  Do NOT inspect Figma or map design tokens here. When the ticket is UI work,
  surface the Figma link found in Jira and hand it to figma-to-ios; do not
  duplicate that analysis in this brief.
- **ios-architecture** — owns deep placement discovery (which VIPER module,
  adapter, navigation entry, networking stack). This skill may point at a likely
  area from ticket text, but defer exact placement to ios-architecture.
- **implement** — the build step. Only runs on explicit `Implement <XR-KEY>`.

## Jira source and access
- Read the ticket via the Jira MCP (`jira` server) in **read scope only**.
- Resolve the Jira cloud id first (`getAccessibleAtlassianResources` /
  `getVisibleJiraProjects`) when it is not already known, then fetch the issue
  with `getJiraIssue` (include the `comment` field and `renderedFields` expand
  when acceptance criteria or design links may live in comments/description).
- Use `searchJiraIssuesUsingJql` only when the user references a ticket by
  something other than a key (e.g. "the latest battery bug").
- The project/key prefix in this program is typically `XR-<number>`; accept any
  valid Jira key the user provides.

## Efficient inputs
The Jira ticket is the source of truth. The user may additionally provide known
context to reduce discovery:

```text
Start XR-123
Known files:
- NC/NCI/Onboarding/Authentication/User Interface/AuthenticationViewController.swift
Known symbols:
- AuthenticationViewController.showLogin(_:)
```

Treat supplied paths/symbols as high-confidence starting points, not as a
complete change list. Validate them before use.

## Inputs
- Jira ticket key (preferred entry point, e.g. `XR-123`).
- Optional known files/symbols supplied by the developer.
- Optional target feature/screen name.

## Ticket types
Classify the ticket into one of:
- New Development / New Feature
- Existing Feature Development / Enhancement
- Bug
- Technical Change / Maintenance without UI
- UI-only Change

For UI work, the Figma link should be present in the Jira ticket. Read the Figma
URL from Jira and pass it to figma-to-ios rather than asking the developer to
paste it again. If UI work has no Figma link, report the gap and ask whether to
continue without Figma.

## Workflow
1. Resolve the Jira cloud id if unknown, then fetch the ticket via Jira MCP
   (read-only): summary, description, issue type, status, acceptance criteria,
   labels/components, attachments, comments, and any linked Figma / spec /
   Confluence URLs.
2. Extract acceptance criteria explicitly. If they are not stated, do not invent
   them — record them as Open Questions.
3. Classify the ticket type (new / enhancement / bug / technical-no-UI / UI).
4. Determine **brand/region applicability**. Behavior in this codebase is
   conditional per target — never assume a feature is universal. Reference the
   real gating surface:
   - `AppTarget` values: `nissan`, `infiniti`, `gom`, `gomInfiniti`, `gomJp`,
     `nissanAus`, `nissanInd` (from `AppPlist.appTargetName`).
   - `KamereonConf.shared` region/environment (`eu`, `eu2`, `gcc`, …).
   - `AppConfiguration.isOneIDEligible` (OneID vs legacy auth/networking path).
   Note which brands/regions the ticket applies to and any gating implied.
5. Identify the **likely implementation area** in the repo from the ticket text,
   without doing full placement discovery (that is ios-architecture's job):
   - App feature/screen → likely under `NC/NCI/<Feature>/` as a VIPER-MVP module
     (View/Presenter/Interactor + boundary protocols + `<Feature>Factory.swift`,
     with `Business Logic` / `User Interface` / `*Adapter` subfolders, e.g.
     `NC/NCI/Onboarding/Authentication/`).
   - API / data work → likely a `Frameworks/*Kit` (e.g. `KamereonAPI`,
     `VehicleManagementKit`, `UserManagementKit`, `DomainKit`, `NCEDomain`,
     `NotificationsKit`, `CarFinderKit`).
   - Navigation change → `NC/NCI/AppRouter`, `Coordinator.swift`, or
     `StoryboardScene` entries.
   - Watch / widget / intent target → `NC/NCIWatch Watch App`, `NC/NCIWidget`,
     `NC/NCWidget`, `NC/NCIntent(UI)` (remember app-layer tokens are not shared
     there).
6. Note any target that is a separate framework, since some `Frameworks/*` build
   at an older Swift version — flag it as an implementation constraint rather
   than resolving it here.
7. Produce the requirements brief (below). No code, no branch, no Jira change.

## Expected output
A **requirements brief in chat**. No files created. Feeds figma-to-ios,
ios-architecture, and implement.

Recommended structure:

### Ticket
- Key, summary, status, issue type

### Goal
- One-paragraph statement of the intended outcome

### Acceptance criteria
- Bullet list exactly as stated in Jira (or Open Questions if missing)

### Type
- New / Enhancement / Bug / Technical-no-UI / UI

### Scope
- In scope
- Out of scope

### Brand / Region
- Applicable `AppTarget`(s) and region/env gating; note OneID eligibility if relevant

### Linked design / spec
- Figma URL (hand to figma-to-ios) / spec / Confluence links found in Jira

### Likely implementation area
- Candidate module/Kit/navigation entry (defer exact placement to ios-architecture)

### Known files / symbols
- Developer-supplied or clearly implied, validated against the repo

### Open questions
- Genuine ambiguities or missing acceptance criteria requiring human input

## Constraints
- Read-only. Do not transition the ticket, comment, assign, log work, or edit any
  Jira field. Jira writes are out of scope here (only jira-complete may mutate Jira).
- Do not write code, create branches, or edit source files.
- Do not perform Figma/design analysis or token mapping — that is figma-to-ios.
- Do not perform deep placement discovery or make runtime claims that cannot be
  verified from code — defer that to ios-architecture.
- If acceptance criteria are missing, surface them as Open Questions — do not
  invent requirements.
- Do not assume a feature applies to all brands/regions; state the applicable
  `AppTarget`/region gating or list it as an Open Question.
- Do not ask the user to repeat a Figma URL that is already present in Jira.

## Failure handling
### Ticket not found / no access
Report and stop. Do not fabricate ticket contents.

### No acceptance criteria in the ticket
List Open Questions rather than proceeding with invented requirements.

### Jira MCP unavailable
Report that Jira access is unavailable and ask the user to paste the ticket
details (summary, description, acceptance criteria, links).

### Cloud id / project ambiguous
Resolve via `getAccessibleAtlassianResources` / `getVisibleJiraProjects`; if
still ambiguous, ask which site/project before fetching.

### UI ticket has no Figma link
Report the missing design input and ask whether to continue without Figma; do
not attempt to reconstruct the design.

### Brand/region not stated
Flag it as an Open Question; do not assume universal applicability.

## Required tools/MCP
### Jira (Atlassian) MCP — read scope only
- `getAccessibleAtlassianResources` / `getVisibleJiraProjects` — resolve cloud id / project
- `getJiraIssue` — fetch ticket details (with `comment` field / `renderedFields` as needed)
- `searchJiraIssuesUsingJql` — locate a ticket when no key is given
- `getJiraIssueRemoteIssueLinks` — surface linked Figma/spec/Confluence resources

Do not use Jira write tools (`editJiraIssue`, `transitionJiraIssue`,
`addCommentToJiraIssue`, `addWorklogToJiraIssue`, etc.) in this skill.

### Repository read operations
Filesystem/read tools to relate the ticket to real code:
- Features: `NC/NCI/<Feature>/` (VIPER-MVP modules, `*Factory.swift`)
- Frameworks/APIs: `Frameworks/*Kit`
- Navigation: `NC/NCI/AppRouter`, `NC/NCI/Coordinator.swift`
- Brand/region gating: `AppTarget` / `KamereonConf` / `AppConfiguration` usages

## Permission gates
None (read-only). Jira writes are out of scope here; only jira-complete may
mutate Jira.

## Source alignment
The ticket-type taxonomy and read-only intake flow follow the team's Jira-driven
workflow. The brand/region gating (`AppTarget`, `KamereonConf`,
`AppConfiguration.isOneIDEligible`), VIPER-MVP module layout under `NC/NCI/`,
and `Frameworks/*Kit` structure are grounded in the (NC/NCI) repository
as of this skill's last update.
