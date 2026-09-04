---
inclusion: fileMatch
fileMatchPattern: ['**/*Tests/**/*.swift', '**/*Spec.swift', 'Frameworks/TestKit/**/*.swift']
---

# Testing

Style is split by target. Run with `bundle exec fastlane unit_test`.

## Framework tests (Frameworks/*/*Tests) — Quick + Nimble + Mockingjay
- `final class <X>Spec: QuickSpec { override func spec() { ... } }`.
- `beforeEach`, nested `describe/context/it`, Nimble `expect(...)`,
  `expect(err).to(matchError(APIError.noInternetConnection))`, async via
  `waitUntil { done in ... }`. Arrange/Act/Assert comments.
- Example: `Frameworks/DrivingAnalysisKit/DrivingAnalysisKitTests/DrivingSummary/
  DrivingSummaryInteractorSpec.swift`.
- File naming: mostly `<X>Spec.swift`; some suites use `<X>Tests.swift` but still
  subclass QuickSpec.

## Main-app tests (NC/NCTests) — XCTest
- `final class <Subject>Tests: XCTestCase`, `setUp()`, `func test<Behavior>()`,
  `XCTAssert`. Example: `NC/NCTests/Onboarding/AccountCreationPresenterTests.swift`.

## Mocks / stubs
- Shared `TestKit`: `Mockable` (loadJSONStub, mockNetwork, `addStub(uri:method:
  filename:)`), `StubUtils`, `SessionManagerMock`. HTTP stubbing via Mockingjay
  (`MockingjayProtocol.addStub(matcher: http(.get, uri:...), builder:)`).
- JSON fixtures named descriptively (e.g. `get_trip_history_daily`).
- Mock naming (hard rule): `Mock<Type>` in NC/NCTests
  (e.g. `MockVehicle`, `MockVehicleListAdapter`); `<Type>Mock` in Frameworks
  tests (e.g. `PresenterMock`, `SessionManagerMock`). No Fake*/Spy*.

## Rules
Do not modify application source to make a test pass. Do not use Quick in
NCTests or the wrong file naming for the target.
