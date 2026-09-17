---
name: ios-architecture
description: >-
  Discover where a change belongs and how surrounding code works in the
  OneId_EVO (NC/NCI) app — VIPER-MVP module, factory, per-feature adapter,
  AdapterCenter shared state, navigation entry, and which networking stack a Kit
  uses — before implementing. Read-only.
activation:
  - "Where does <X> live"
  - "how does <feature> work"
  - "find the module for <X>"
  - "which networking stack for <X>"
  - "how is <feature> wired"
tools:
  - filesystem-read
  - filesystem-search
  - context-gatherer
gates: []
---

# ios-architecture

## Purpose
Locate the correct place for a change in the OneId_EVO (NC/NCI) codebase and
surface the exact patterns to mirror: the VIPER-MVP module + boundary protocols +
`<Feature>Factory`, the per-feature `<Feature>Adapter` and any `AdapterCenter`
shared state it reads, the navigation entry point, and — for API work — which
networking stack the neighboring Kit uses (legacy Kamereon vs OneID). Output is a
discovery report, not code.

## Activation conditions
Before implementing in an unfamiliar area, or on explicit request. Also invoked
internally by implement to ground placement before writing files.

## Inputs
- Feature/screen/behavior name, or a file path, or a symbol.

## Project architecture facts (verified in this repo)
Map findings to these real patterns; report file:line evidence, never assume.

### VIPER-MVP feature modules (`NC/NCI/<Feature>/`)
Features are VIPER-MVP with roles split across dedicated files and subfolders.
Reference module: `NC/NCI/Onboarding/Authentication/`:
- **Factory:** `<Feature>Factory.swift` — wires the module (e.g. `AuthFactory.swift`,
  `AppRouter/LaunchFactory.swift`). Note: the factory file is named
  `<Feature>Factory`, NOT `<Feature>ModuleFactory`.
- **User Interface/** — `<Feature>ViewController.swift`, `<Feature>Presenter.swift`,
  and the presenter boundary protocols as separate files:
  `<Feature>PresenterInput.swift` / `<Feature>PresenterOutput.swift`.
- **Business Logic/** — `<Feature>Interactor.swift` and its boundaries
  `<Feature>InteractorInput.swift` / `<Feature>InteractorOutput.swift`, plus
  supporting handlers (e.g. `AuthNetworkHandler.swift`).
- **`<Feature>Adapter/`** — the feature's own adapter (e.g.
  `AuthAdapter/AuthAdapter.swift`).
- Sub-flows get their own VIPER subfolder with `Boundaries` / `Interactor` /
  `Presenter` (e.g. `Authentication/OneID/`).
- View controllers typically subclass `BaseViewController` /
  `BaseTableViewController` (in `NC/NCI/Commons`).

When classifying, identify each role by file and report the wiring the factory
performs (e.g. `interactor.output = presenter`, `presenter.view = view`).

### AdapterCenter — shared runtime state (not a per-feature adapter)
`AdapterCenter.shared` is a global singleton holding cross-feature vehicle/session
state (e.g. `vin`, `healthStatus`, `userProfileDetails`, and vehicle-capability
flags such as `isCCS2Vehicle`, `isXFKVehicle`, `isSecondaryUser`). It is read
widely from `Coordinator.swift`, `Coordinator+Notification.swift`,
`SceneDelegate.swift`, `AppRouter/*`, and presenters.

Distinguish the two "adapter" concepts in your report:
- **`AdapterCenter.shared`** — shared app-wide state a feature reads/writes.
- **`<Feature>Adapter`** — the feature-local adapter class inside the module.

Identify which `AdapterCenter` fields the feature depends on and which
`<Feature>Adapter` it owns.

### Navigation
- `AppRouter.shared` drives context-based routing via `processContext(_:)`,
  using `AppContext` / `AppActivity` (`NC/NCI/AppRouter/`). The launch flow is
  itself a VIPER module in `AppRouter/` (`Launch*` files).
- `Coordinator.swift` (+ `Coordinator+Notification.swift`) handles
  notification/deeplink-driven navigation and theming.
- Storyboard transitions use SwiftGen `StoryboardSegue` / `StoryboardScene`
  (e.g. `StoryboardSegue.Main.mainTab`) and custom segues in `Commons`
  (`ReplaceRootSegue`, `StatusBarColorSegue`, etc.).
- Tab structure lives under `NC/NCI/TabBar/`.

Report the concrete entry point a change should hook into.

### Networking stacks (report which one the neighbor uses)
Two stacks coexist; the choice is gated on `AppConfiguration.isOneIDEligible`
(and brand/region via `KamereonConf`). State the evidence, do not guess.

- **Legacy Kamereon stack** — per-endpoint folders in
  `Frameworks/KamereonAPI/KamereonAPI/<Endpoint>/`, each with a `Core/`
  containing: `<Endpoint>Router.swift`, `<Endpoint>Network.swift` +
  `<Endpoint>NetworkBoundaries.swift`, `<Endpoint>Controller.swift` +
  `<Endpoint>ControllerBoundaries.swift`, and `*NetworkDataModel` response
  models. Uses **PromiseKit** + **ObjectMapper**. Reference:
  `Frameworks/KamereonAPI/KamereonAPI/Vehicle List/`. Many endpoints also have
  `Commons/` and `BFF/` subfolders.
- **OneID callback stack** — under `NC/NCI/Onboarding/*/OneID/` and
  `Frameworks/NCEDomain/NCEDomain/OneID/`, callback-based, taken when
  `AppConfiguration.isOneIDEligible` is true.

For API work, report which stack the neighboring code uses and the exact files to
mirror.

### Swift version per target (real constraint)
Framework build configs are genuinely mixed: some `Frameworks/*` projects build
at `SWIFT_VERSION = 4.2`, others at `5.0`. When the change lands in a Framework,
read that project's `project.pbxproj` and report its Swift version, because
4.2 vs 5.0 affects the syntax the implementation may use.

### Brand/region gating
Behavior is conditional per target. Note relevant gating the feature already uses:
`AppTarget` (`nissan`/`infiniti`/`gom`/`gomInfiniti`/`gomJp`/`nissanAus`/
`nissanInd` via `AppPlist.appTargetName`), `KamereonConf.shared` region/env
(`eu`/`eu2`/`gcc`), and `AppConfiguration.isOneIDEligible`.

### Platform targets
Watch (`NC/NCIWatch Watch App`), widgets (`NC/NCIWidget`, `NC/NCWidget`), and
intents (`NC/NCIntent`, `NC/NCIntentUI`) are separate targets and do NOT share the
app-layer `Asset`/`FontFamily`/`TextStyle`/`L10n`. If the change targets one of
these, report that boundary.

## Workflow
1. Locate the feature files and classify roles: View(Controller) / Presenter /
   Interactor / boundary protocols / `<Feature>Factory`. Report the factory wiring.
2. Identify the feature's `<Feature>Adapter` and which `AdapterCenter.shared`
   fields it reads/writes.
3. Identify the navigation entry (`AppRouter.processContext` / `Coordinator` /
   `StoryboardSegue`/`StoryboardScene`).
4. For networking: report whether the neighboring Kit uses the legacy Kamereon
   stack (`Router`/`Network`/`Controller` + boundaries + PromiseKit + ObjectMapper)
   or the OneID callback stack gated on `AppConfiguration.isOneIDEligible` — with
   file evidence.
5. Note the Swift version of the target when it is a Framework (4.2 vs 5.0).
6. Note brand/region gating already present in the feature.
7. Produce a discovery report (files + roles + patterns/variations to follow).

## Expected output
A **discovery report in chat** with `file:line` references. No files created.

Recommended structure:
- **Feature module** — files by role + factory wiring
- **Adapters/state** — `<Feature>Adapter` + `AdapterCenter.shared` fields used
- **Navigation entry** — the concrete hook point
- **Networking** — which stack + reference files (or N/A)
- **Constraints** — Swift version (if Framework), brand/region gating, target boundary
- **Candidates / ambiguities** — when ownership is unclear

## Constraints
- Read-only. Do not modify code. Do not propose changes to files not read.
- Do not make runtime claims that cannot be verified from the code.
- Do not conflate `AdapterCenter.shared` (shared state) with a `<Feature>Adapter`
  (feature-local class) — report them distinctly.
- Prefer reporting candidates over guessing a single owner when ambiguous.
- Do not choose a networking stack by name alone; cite the neighboring files.

## Failure handling
- Feature spans multiple Kits with no clear owner → list candidates with evidence.
- Symbol not found → report and broaden the search rather than assuming.
- Networking stack ambiguous → report both and the gating condition
  (`AppConfiguration.isOneIDEligible`) rather than picking one.
- Deep cross-file trace needed → delegate to context-gatherer, then summarize.

## Required tools/MCP
Filesystem read/search; may delegate deep traces to context-gatherer.

## Permission gates
None (read-only).

## Source alignment
The VIPER-MVP layout (`<Feature>Factory`, split boundary files, `Business Logic`/
`User Interface`/`<Feature>Adapter` subfolders), `AdapterCenter.shared` shared
state, `AppRouter`/`Coordinator`/`StoryboardSegue` navigation, the legacy Kamereon
per-endpoint `Core` stack (PromiseKit + ObjectMapper) vs the OneID callback stack
gated on `AppConfiguration.isOneIDEligible`, the mixed Swift 4.2/5.0 framework
targets, and brand/region gating are all grounded in the OneId_EVO (NC/NCI)
repository as of this skill's last update.
