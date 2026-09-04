---
inclusion: always
---

# Tech Stack

## Versions
- Swift 5.0 for the app (NCI) and most targets. Some framework targets are
  Swift 4.2 — see swift.md for the exact list; check SWIFT_VERSION before using
  Swift-5-only syntax in a framework.
- Min iOS 16.0 (NCI app target); Pods/frameworks deploy target 13.0.
- watchOS 8.3. Ruby 3.0.0 (.ruby-version). Xcode: latest stable.

## Dependencies
- CocoaPods; open `NC.xcworkspace`. Run `pod install` after pulling.
  If SPM packages are stale/failing: `xcodebuild -resolvePackageDependencies`.
- Key libs: Alamofire, PromiseKit, ObjectMapper, SwiftGen, Firebase
  (Analytics/RemoteConfig/Crashlytics/Messaging), GoogleMaps, GoogleMLKit,
  Lottie, APNGKit, Charts, JWTDecode. Tests: Quick, Nimble, Mockingjay.

## Build / validation commands
- Configure env: `bundle exec fastlane configure --env <environment-brand-region>`
  (e.g. `preprod-uat-ncs-eur`). This rewrites project files and runs
  `reset_git_repo(force: true)` — destructive to the working tree.
- Lint: `bundle exec fastlane lint` (SwiftLint HTML) and
  `bundle exec fastlane danger_lint` (Danger, fails PR on any issue).
- Unit tests: `bundle exec fastlane unit_test` (scheme NCI, Debug,
  device "iPhone 17" — the device is pinned in the Fastfile and must exist
  locally or be overridden).
- SwiftGen: `brew install swiftgen`.

## Pointers
Architecture -> architecture.md. Networking/testing/design details load per file
type (see uikit.md, swiftui.md, testing.md, design-system.md). Git/PR and region
switching are manual steering (workflow.md).
