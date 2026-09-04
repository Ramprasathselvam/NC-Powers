---
inclusion: auto
---

# Architecture

UIKit-first, VIPER-style MVP. No reactive MVVM in the UIKit layer (no
RxSwift/Combine view binding). ObservableObject MVVM is used only in SwiftUI
islands and the watch app.

## VIPER-MVP flow
View ⇄ Presenter ⇄ Interactor, wired by a per-feature module factory.
- Boundary protocols split: `<Feature>PresenterInput` (VC→Presenter),
  `<Feature>PresenterOutput` (Presenter→VC), `<Feature>InteractorInput/Output`.
  Example co-locating them: `NC/NCI/Onboarding/VehicleCompatibility/
  VehicleCompatibilityBoundaries.swift`.
- VC holds `<Feature>PresenterInput?` and conforms to `<Feature>PresenterOutput`.
- Factory assembles + cross-wires. Canonical example
  `NC/NCI/TabBar/Vehicle List/VehicleManagementModuleFactory.swift`:
  pulls adapter from `AdapterCenter.shared`, builds interactor+presenter,
  sets `interactor.output = presenter` and `presenter.view = view`.

## Dependency access — AdapterCenter service locator
`Frameworks/NCEDomain/NCEDomain/AdapterCenter.swift`: `static var shared`,
owns per-feature adapters (`hvac`, `battery`, `vehicleList`, ...). Rebuilds
adapters on `vehicleDidChange(vin:)` via `resetFeatureAdapters()`. No DI container.
Constructor injection happens at the module-factory boundary. Other singletons:
`AuthManager.shared`, `UserDefaultsManager.shared`, `KamereonConf.shared`,
`Logger.shared`, `MyCMSBannerMessage.shared`.

## Networking (two coexisting stacks)
- Legacy Kamereon (dominant): 4 layers per feature — Router / Network / Controller
  / Boundaries — with PromiseKit + ObjectMapper. Base: `MyCMSFoundation/Network/
  Requestable.swift` (Alamofire Session + cert pinning) and `Controllable.swift`
  (`performRequest(mapper:)`, `ControllableError.mappingFailed`). Example endpoint:
  `Frameworks/KamereonAPI/.../Vehicle List/Core/VehicleListRouter.swift`
  (enum: `method`/`path`/`taskIdentifier`/`asURLRequest`), `VehicleListController.swift`.
- Newer OneID: raw Alamofire + `Result` callbacks in NCEDomain adapters, gated on
  `AppConfiguration.isOneIDEligible`, decoding `KamereonErrorResponse`.
- When adding an endpoint, mirror the stack the neighboring code in that Kit uses.

## Navigation
- `AppRouter.shared` (thin) forwards to `AppRouterDelegate` = `MainTabBarController`
  for deep-link/push routing.
- `Coordinator.shared` handles app lifecycle, theming, config, and
  push-notification alerting (not a screen-flow child-coordinator tree).
- Screen-to-screen: SwiftGen `StoryboardScene.X.y.instantiate()` + push/present;
  typed `StoryboardSegue` identifiers; `Commons/Wireframe` helpers
  (`SegueCoordinator`, `SegueHandlerType`, `Routable`).

## Errors
`KamereonKit.APIError` enum + `KamereonErrorResponse` server decoding;
PromiseKit `.catch`/`reject`; `ControllableError.mappingFailed`. Surface via
`MyCMSBannerMessage` or `UIAlertController` with `L10n` strings.
