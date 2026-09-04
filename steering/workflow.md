---
inclusion: manual
---

# Git, PR, Jira & Region Workflow

## Branches (BRANCH.md, README)
- Feature base from develop: `features/xxxx/base`; task/bug sub-branch:
  `features/xxxx/XR-###`. Release: `release/x.x.x`. SDSM/Market from develop.
  Hotfix from master. Never push directly to master/develop/release.

## Commits (CONTRIBUTING.md)
Emoji + present-tense imperative; reference issues/Jira at end, e.g.
`:bug: Fix cache invalidation. Related to #1024 [XR-442]`.

## PR (.github/pull_request_template.md)
- Title: `[XR-000] <desc>`. Fill: Jira link, Spec link (+section), Figma link,
  before/after validation proof (light & dark), SOLID checklist, and the
  SwiftLint error/warning before/after count table.
- Merge by leads with >=1 reviewer approval.
- CI PR gate runs `unit_test` + `danger_lint` (Danger fails on any lint issue).

## Region matrix (verified against fastlane/.env.* files)
| App / Region       | PREPROD                      | PROD                          |
| ------------------ | ---------------------------- | ----------------------------- |
| EU                 | preprod-uat-ncs-eur          | appstore-ncs-eur              |
| NC-GOM (UAE)       | preprod-uat-ncs-gom-uae      | appstore-ncs-gom-uae          |
| NC-AUS             | preprod-uat-ncs-gom-aus      | appstore-ncs-gom-aus          |
| Thai/JP/Phil       | preprod-uat-ncs-gom-thai     | appstore-ncs-gom-thai         |
| Infiniti-GOM (UAE) | preprod-uat-infiniti-gom-uae | appstore-infiniti-gom-uae     |
| Infiniti-UKR       | preprod-uat-infiniti-ukr     | appstore-infiniti-ukr         |

Switch sequence: `configure --env <matrix-value>` -> `pod install` ->
(only if SPM stale/failing) `xcodebuild -resolvePackageDependencies`.

WARNING: `configure` runs `reset_git_repo(force: true)` and rewrites xcconfig,
app identifiers, team, and strips localizations — destructive to the working
tree. Require a clean tree first and confirm before running. Always keep the
space in `--env <value>`. (The step-by-step execution belongs in the
region-switch Skill; this section is reference for the correct env values.)
