---
inclusion: fileMatch
fileMatchPattern: ['NC/NCI/User Profile/SoftwareUpdate/**/*.swift', 'NC/NCI/Commons/SwiftUI/**/*.swift', 'NC/NCI/Commons/SwiftUIComponents/**/*.swift', 'NC/NCI/Commons/Component/SwiftUIComponents/**/*.swift', 'NC/NCWidget/**/*.swift', 'NC/NCIWatch Watch App/**/*.swift']
---

# SwiftUI Patterns

SwiftUI is used as islands in the UIKit app, and is native on watchOS and widgets.
Place new SwiftUI islands under the folders above so this guidance applies.

- MVVM: `class <Feature>ViewModel: ObservableObject` with `@Published`
  properties. Example: `NC/NCI/User Profile/SoftwareUpdate/ViewModel/
  SoftwareUpdateDashboardViewModel.swift` + `.../View/SoftwareUpdateDashboardView.swift`.
- State: `@State` for local view state; `@StateObject`/`@ObservedObject` to bind VMs.
- Bridge to UIKit via `UIHostingController` (see `Coordinator.countdownHostingController`)
  and the `NavigationRouter: ObservableObject` helper in Commons.
- Watch app: SwiftUI views under `Views/` + `ObservableObject` handlers under
  `Handlers/`.
- Widgets: `@main struct NCWidgetBundle: WidgetBundle` composing `Widget`s
  (NC/NCWidget); each has a `StaticConfiguration` and a SwiftUI EntryView.
- For app-layer islands (`NC/NCI`) reuse tokens per design-system.md. Note the
  watch app and widgets have their own local tokens (e.g. `NCIWatch Watch App/
  Colors+Extensions.swift`), not the app's SwiftGen `Asset`.
