---
inclusion: fileMatch
fileMatchPattern: ['NC/NCI/**/*.swift']
---

# UIKit Patterns

Applies to the app layer (`NC/NCI`). Frameworks do NOT use `BaseViewController`,
the app's SwiftGen tokens (`Asset`/`FontFamily`/`L10n`), or `AccessibilityProperties`.

- Subclass `BaseViewController` / `BaseTableViewController` (NC/NCI/Commons).
  Base provides back button (`setupBackButton`), right bar buttons, and a
  `MaterialActivityIndicatorView` loading indicator
  (`setupActivityIndicator`/`showLoadingIndicator`/`hideLoadingIndicator`).
- `handleBackAction()` is notification-aware: it checks
  `AdapterCenter.shared?.isFromNotifications` and may call
  `AppRouter.shared.delegate?.switchToNotification()`.
- Instantiate VCs programmatically via SwiftGen scenes
  (`StoryboardScene.<Board>.<scene>.instantiate()`), then push/present.
- RTL-aware where relevant (`isRTLLayout` in BaseViewController).

## Reusable components (NC/NCI/Commons, Commons/Component)
- Programmatic `@IBDesignable` subclass, e.g. `CustomRadiusButton.swift`
  (`@IBInspectable var cornerRadius`).
- Xib-backed view: `init` calls `xibSetup()` which does
  `Bundle.main.loadNibNamed(...)`; configured via a view-model struct + a
  delegate. Example: `NC/NCI/Commons/Component/NCErrorView/NCErrorView.swift`
  (`NCErrorViewModel`, `NCErrorViewDelegate`).
- Naming: `NC`/`NCI` prefix for app components; `Custom` prefix for bespoke variants.

## Accessibility
Set identifiers via `AccessibilityHelper` typed setters (e.g.
`label.setAccessibilityIdentifierToLabel(identifierText:)`) using per-screen
`AccessibilityProperties` String enums (e.g. `AccountCreation.continueButton`).
Dynamic Type is NOT adopted — fonts are fixed size.
