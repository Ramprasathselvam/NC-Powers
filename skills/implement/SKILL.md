---
name: implement
description: Implement an XR ticket in the repo's conventions (VIPER-MVP, networking stack, design tokens) and author matching tests. Stops at implementation — never validates, creates a PR, reviews, or moves Jira.
activation: "Implement <XR-KEY>"
tools: [filesystem-read, filesystem-search, filesystem-write, context-gatherer]
gates: [file-write-confirm]
---

# implement

## Purpose
Turn a ticket's requirements into code following the project's real patterns,
plus the tests that cover it. This is the build step only.

## Activation conditions
Explicit command: `Implement <XR-KEY>`. Nothing else in this skill runs
automatically after implementation.

## Inputs
- XR ticket key. Uses jira-to-ios output (requirements), figma-to-ios output
  (design mapping) and ios-architecture output (placement/patterns) if available;
  invokes them first if not.

## Workflow
1. Ensure requirements + placement are known (call jira-to-ios / ios-architecture
   / figma-to-ios as needed). Confirm the approach with the user (scope + which
   module/stack) before writing files.
2. Implement following steering:
   - App feature → `NC/NCI/<Feature>/` as VIPER-MVP (View/Presenter/Interactor +
     `<Feature>PresenterInput/Output`, `<Feature>InteractorInput/Output`, and a
     `<Feature>ModuleFactory` that wires `interactor.output = presenter` /
     `presenter.view = view`). Subclass `BaseViewController`/`BaseTableViewController`.
   - SwiftUI island → ObservableObject VM + View under the known island folders
     (swiftui.md).
   - New endpoint → in the relevant `Frameworks/*Kit` mirroring the neighboring
     stack (legacy Router/Network/Controller/Boundaries + PromiseKit + ObjectMapper,
     or OneID callbacks gated on `AppConfiguration.isOneIDEligible`).
   - Reuse design tokens (`Asset`/`FontFamily`/`UIFonts+NCI`/`L10n`); never
     hardcode. `Asset`/`L10n`/`FontFamily` are app-only — not for use in a Kit.
   - Set accessibility identifiers via `AccessibilityProperties` + `AccessibilityHelper`.
   - Respect per-target Swift version (some Frameworks are 4.2 — swift.md).
   - Respect brand/region gating (`KamereonConf`/`AppTarget`/`AppConfiguration`).
3. Author tests in the target's style (testing.md): Quick/Nimble/Mockingjay
   `<X>Spec` for Frameworks; XCTest `<Subject>Tests` for NC/NCTests. Use TestKit
   `Mockable`/`SessionManagerMock`; mock naming `Mock<Type>` (NCTests) vs
   `<Type>Mock` (Frameworks).
4. Report all files created/edited.

## Expected output
New/edited Swift source + test files. A summary of changes. NO validation run,
NO branch, NO commit, NO PR, NO Jira change.

## Constraints
- MUST NOT create a PR, run code review, or move the Jira issue. Those are the
  separate explicit commands (Validate / Create PR / Review PR / Complete).
- Do not modify unrelated files or existing tests unless the ticket requires it.
- Do not introduce a new DI container, reactive framework, or networking library.
- Do not hardcode strings/colors/fonts; if a design token is missing, stop and
  flag the gap (from figma-to-ios) rather than inventing one.
- Do not edit `.pbxproj` beyond necessity; if Xcode target membership must change,
  flag it for the user rather than guessing.

## Failure handling
- Requirements or design tokens missing → stop and surface, do not guess.
- No clear factory/adapter/stack to hook into → defer to ios-architecture.
- Change needs project-file surgery the agent cannot do safely → report and pause.

## Required tools/MCP
Filesystem read/search/write; context-gatherer for discovery.

## Permission gates
Confirm before creating/editing source and test files (shows the file list). If
any `.pbxproj` change is implied, require explicit confirmation.
