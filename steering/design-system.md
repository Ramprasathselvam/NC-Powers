---
inclusion: fileMatch
fileMatchPattern: ['NC/NCI/**/*.swift']
---

# Design System

Tokens are SwiftGen-generated in `NC/NCI/Commons/SwiftGen/`. There is no formal
spacing scale — spacing is per-screen (literal constants / per-screen `Constants`
enums).

## Fonts
- `NC/NCI/Commons/SwiftGen/Fonts.swift` -> `FontFamily` (NissanBrand,
  NissanBrandW01Bold/Light, InfinitiBrand, SFProDisplay, SFProText).
- Prefer the wrappers in `NC/NCI/Extensions/UIFonts+NCI.swift`, e.g.
  `UIFont.nissanRegularFont(ofSize:)`, `UIFont.sfptextSemiBoldFont(ofSize:)`.
  Or `FontFamily.SFProText.semibold.font(size:)`. All have system fallbacks.
- Fonts registered at runtime via `FontFamily.registerAllCustomFonts()`.

## Colors
- `Asset.<name>.color` from generated `Commons/SwiftGen/Assets.swift`
  (e.g. `Asset.ncBlue`, `Asset.ncTitleBlack`, `Asset.ncBannerBackground`).
- `NC/NCI/Extensions/UIColor+MyCMSColor.swift` adds helpers incl.
  `getColorBasedOnAppTheme()` (resolves light/dark from UserDefaults
  `appearanceSetting`). Brand switching lives in `Brand.swift`.

## Rule
Match a Figma value to an existing `Asset`/`FontFamily` token. If none exists,
flag it as a gap for a human — do not hardcode a literal or invent a token.
