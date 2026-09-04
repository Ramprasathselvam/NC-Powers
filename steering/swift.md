---
inclusion: always
---

# Swift Conventions

## Linting (.swiftlint.yml — PR-blocking via Danger; pre-commit hook blocks errors)
line_length 400; function_body_length 100; file_length 800/1000;
type_body_length 500/1000; cyclomatic_complexity 30/40; type_name min 4.
Excluded: Pods, Carthage, vendor, Commons/SwiftGen, Frameworks/SRP.
Disabled: trailing_whitespace, colon, comma, control_statement,
non_optional_string_data_conversion. Format with XCFormat.

## Swift version per target
App/most targets: 5.0. Swift 4.2 targets (verify SWIFT_VERSION before using
newer syntax): MyCMSFoundation, NotificationsKit, DestinationSendToCarKit,
AccessContactsKit, DrivingAnalysisKit, RemoteControlVehicleSettingsKit,
RemoteVehicleSettingsTransferKit (and the URITemplate pod).

## Do not hardcode (app layer, NC/NCI)
- Strings: `L10n.<Table>.<key>` (SwiftGen). Add to the correct feature .strings
  table, not Localizable by default. (design-system.md, testing.md have detail.)
- Colors: `Asset.<name>.color`; theme-resolve via `getColorBasedOnAppTheme()`.
- Fonts: `UIFont.<brand><weight>Font(ofSize:)` (UIFonts+NCI) or `FontFamily`.
- Accessibility IDs: `AccessibilityProperties` enums + `AccessibilityHelper` setters.
- Note: these SwiftGen tokens are app-only. `Frameworks/*` have their own
  resources — do not use `Asset`/`L10n`/`FontFamily` inside a Kit.

## Naming
- Reusable app UI: `NC`/`NCI` prefix; bespoke variants: `Custom` prefix.
- Boundary protocols: `<Feature>PresenterInput/Output`,
  `<Feature>InteractorInput/Output`.
- Prefer `struct` value types for models. New features follow SOLID (PR checklist).
