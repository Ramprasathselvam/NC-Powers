---
inclusion: always
---

# Repository Structure

```
NC.xcworkspace          # open this
NC/
  NCI/                  # main app source
    AppRouter/          # AppRouter + AppRouterDelegate (deep-link/push routing)
    Coordinator.swift   # app-lifecycle/notification/alert coordinator (singleton)
    TabBar/             # MainTabBarController + tab features (Dashboard, Navigation,
                        #   NissanStore, Vehicle List, Add Vehicle, Assistance)
    Onboarding/         # Auth, OneID, account creation, vehicle compatibility
    User Profile/       # Profile; SoftwareUpdate (SwiftUI island)
    Commons/            # base classes, reusable components, SwiftGen output,
                        #   Accessibility, Wireframe, SwiftUI components
    Extensions/         # UIColor+MyCMSColor, UIFonts+NCI, ...
    Resources/          # Localization/<lang>.lproj, Fonts, Info.plist
  NCTests/              # main-app unit tests (XCTest)
  NCWidget/             # WidgetKit extension (SwiftUI)
  NCIntent/ NCIntentUI/ # Siri Intents extensions
  NCIWatch Watch App/   # watchOS app (SwiftUI + MVVM)
Frameworks/             # feature + platform frameworks (each an .xcodeproj)
  KamereonAPI/          # Kamereon endpoints (Router/Network/Controller/Boundaries)
  MyCMSFoundation/      # networking base (Requestable, Controllable), utilities
  NCEDomain/            # domain models/adapters + AdapterCenter (service locator)
  TestKit/              # shared test mocks/stubs (Mockable, SessionManagerMock)
  SRP/                  # Secure Remote Password auth
  + feature Kits (CarFinderKit, DrivingAnalysisKit, DestinationSendToCarKit,
    NotificationsKit, RemoteControlVehicleSettingsKit, RemoteDataWipeKit,
    RemoteVehicleSettingsTransferKit, UserManagementKit, VehicleManagementKit,
    AccessContactsKit, DomainKit)
fastlane/               # Fastfile + per-region .env files
Scripts/ hooks/ .github/# runlint, sonar, pre-commit hook, CI workflows
```

## Where things live
- ViewControllers: `NC/NCI/<Feature>/...ViewController.swift`; subclass
  `BaseViewController`/`BaseTableViewController` (Commons/).
- Presenter/Interactor/Boundaries: co-located per feature; assembled by a
  `<Feature>ModuleFactory`.
- "ViewModels": passive display structs in UIKit; real ObservableObject VMs only
  in SwiftUI islands (SoftwareUpdate, watch app).
- Models: network models (`Mappable`) in KamereonAPI; domain models (`Codable`)
  in NCEDomain.
- Networking: KamereonAPI (endpoints), MyCMSFoundation/Network (base),
  NCEDomain (OneID adapters).
- Tests: Frameworks/*/*Tests (Quick); NC/NCTests (XCTest); shared TestKit.
- SwiftGen output: `NC/NCI/Commons/SwiftGen/` (Asset, L10n, FontFamily);
  sources in `NC/NCI/Resources/`.

## New-code placement
- New app feature -> `NC/NCI/<Feature>/` (VIPER + module factory).
- New endpoint -> relevant `Frameworks/*Kit` (Router/Network/Controller/Boundaries).
- Shared reusable UI -> `NC/NCI/Commons` (prefix `NC`/`NCI`; `Custom` for variants).
