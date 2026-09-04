---
inclusion: always
---

# Product

NissanConnect Services (NCS) and MyINFINITI — native iOS connected-car apps
(repo: OneId_EVO; README: "NissanConnect Europe").

## Purpose
Let Nissan/Infiniti owners monitor and remotely control their vehicles from
iPhone, Apple Watch, home-screen widgets, and Siri.

## Brands & regions
- Brands: Nissan (NCS), Infiniti.
- Regions: EUR, GOM-UAE, GOM-AUS, GOM-THAI (Asia), UKR, IND.
- One codebase, multiple brand/region app targets: NCI, NC-AUS, NC-ASEAN,
  IN-UKR, IN-UAE, NC-GCC, NC-IND (see Podfile).
- Brand/region behavior is conditional and gated through `KamereonConf`,
  `AppTarget`, and `AppConfiguration`. Never assume a feature is universal.

## Major features
Onboarding & identity (OneID + SRP), vehicle management (add/remove, VIN
compatibility), remote control & settings (HVAC/climate, charging, lock,
horn/lights, geofencing), Find My Car, driving analysis, send-to-car navigation,
notifications, OTA software updates, Nissan Store, roadside assistance, remote
data wipe. Companion surfaces: watchOS app, WidgetKit widgets, Siri Intents.

## Business context
- Backend is the Renault-Nissan-Mitsubishi Kamereon platform (Core + BFF).
- Jira project prefix is XR (acmsviseo.atlassian.net).
- Translations are managed via Transifex.
