---
name: ios-architecture
description: Discover where a change belongs and how surrounding code works (VIPER module, factory, AdapterCenter, networking stack, navigation) before implementing. Read-only.
activation: "Where does <X> live", "how does <feature> work", "find the module for <X>", "which networking stack for <X>"
tools: [filesystem-read, filesystem-search, context-gatherer]
gates: none
---

# ios-architecture

## Purpose
Locate the correct place for a change and surface the exact patterns to mirror:
the VIPER module + boundary protocols + module factory, the relevant
`AdapterCenter` adapter, the navigation entry point, and — for API work — which
networking stack the neighboring code uses.

## Activation conditions
Before implementing in an unfamiliar area, or on explicit request. Also invoked
internally by implement.

## Inputs
- Feature/screen/behavior name, or a file path.

## Workflow
1. Locate the feature files and classify roles: View(Controller)/Presenter/
   Interactor/Boundaries/`<Feature>ModuleFactory` (see architecture.md).
2. Identify the `AdapterCenter` adapter(s) the feature depends on.
3. Identify the navigation entry (AppRouter/Coordinator/`StoryboardScene`).
4. For networking: report whether the neighboring Kit code uses the legacy
   Kamereon stack (Router/Network/Controller/Boundaries + PromiseKit + ObjectMapper)
   or the newer OneID callback stack gated on `AppConfiguration.isOneIDEligible`,
   so the stack choice is evidenced, not guessed.
5. Note the Swift version of the target (some Frameworks are 4.2 — see swift.md).
6. Produce a discovery report (files + roles + patterns/variations to follow).

## Expected output
A discovery report in chat with file:line references. No files created.

## Constraints
- Read-only. Do not modify code. Do not propose changes to files not read.
- Do not make runtime claims that cannot be verified from the code.
- Prefer reporting candidates over guessing a single owner when ambiguous.

## Failure handling
- Feature spans multiple Kits with no clear owner → list candidates.
- Symbol not found → report and broaden the search rather than assuming.

## Required tools/MCP
Filesystem read/search; may delegate deep traces to context-gatherer.

## Permission gates
None (read-only).
