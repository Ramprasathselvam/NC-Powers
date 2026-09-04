# NC-Powers

**NissanConnect Kiro Power for Jira-driven iOS development.**

`NC-Powers` is a team-managed Kiro Power repository created specifically for the NissanConnect iOS codebase. It centralizes the project's workflow, Skills, Steering knowledge, and integration configuration so the team can maintain and evolve them in one place.

## Development lifecycle

```text
Jira → Figma → Architecture → Implement → Test Cases
                                      ↓
                                   Validate
                                      ↓
                                  Create PR
                                      ↓
                                  Review PR
                                      ↓
                                  Complete
```

Each stage is intentionally separated. A command does not automatically trigger the next command.

## Commands

| Command | Responsibility | Writes |
|---|---|---|
| `Start <XR-KEY>` | Read Jira requirements | No |
| `Implement <XR-KEY>` | Discover, plan, implement source and tests, record evidence | Code/test + gated Jira |
| `Validate <XR-KEY>` | Run quality and acceptance checks | No source/git/Jira |
| `Create PR for <XR-KEY>` | Branch, commit, push and create PR | Git/GitHub, all gated |
| `Review PR for <XR-KEY>` | Review the actual PR diff | No by default; GitHub posting gated |
| `Complete <XR-KEY>` | Complete Jira after PR completion | Jira, gated |

## Efficient usage

You do not need to provide every detail to every command. Jira is the primary source of truth for requirements, and the Figma link should be stored in Jira for UI work.

If you already know the implementation area, provide exact context to reduce unnecessary discovery:

```text
Implement XR-123

Known files:
- NC/NCI/Features/Garage/GarageViewModel.swift
- NC/NCI/Features/Garage/GarageViewController.swift

Known symbols:
- GarageViewModel.loadVehicles()
```

The Power validates supplied paths and reads them first. It searches only for dependencies that are still needed; it never assumes the supplied files are the complete change set.

For PR review, use the PR number when known:

```text
Review PR #456 for XR-123
```

This allows GitHub MCP to focus on the actual PR, changed files, CI, and review discussion instead of scanning the repository unnecessarily.

## Jira ticket standard

See **[Jira Ticket Format](docs/jira-ticket-format.md)** for the team standard. It covers:

- New Development / New Feature
- Existing Feature Development / Enhancement
- Bug
- Technical Change / Maintenance without UI
- UI-only Change

A good Jira ticket should contain a clear summary, current/requested behavior, acceptance criteria, scope, dependencies, and brand/region applicability where relevant.

### Figma rule

For UI work, **add the Figma link to the Jira ticket**. `NC-Powers` reads that link from Jira during intake and Figma mapping. Developers do not need to paste the same Figma URL again when running `Implement <XR-KEY>`.

If UI work has no Figma link, the workflow reports the missing design input instead of inventing a design.

For non-UI work, use:

```text
Figma: N/A - No UI change
```

## Repository structure

```text
NC-Powers/
├── plugin.json
├── POWER.md
├── mcp.json
├── skills/
│   ├── jira-to-ios/
│   ├── figma-to-ios/
│   ├── ios-architecture/
│   ├── implement/
│   ├── validate/
│   ├── pr-create/
│   ├── code-review/
│   └── jira-complete/
├── steering/
│   ├── product.md
│   ├── architecture.md
│   ├── structure.md
│   ├── design-system.md
│   ├── swift.md
│   ├── swiftui.md
│   ├── uikit.md
│   ├── testing.md
│   ├── tech.md
│   └── workflow.md
├── mcp/
│   ├── jira.example.json
│   ├── figma.example.json
│   └── github.example.json
├── docs/
│   ├── architecture.md
│   ├── workflow.md
│   ├── jira-ticket-format.md
│   ├── installation.md
│   └── contribution.md
├── examples/
│   └── README.md
└── LICENSE
```

## Why Steering is here

This repository is intentionally project-specific. The `steering/` files capture NissanConnect knowledge that the Skills need in order to make correct decisions, including VIPER-style architecture, AdapterCenter/networking, Nissan/Infiniti brand and region behavior, Swift/SwiftUI/UIKit conventions, design-system tokens, localization, tests, Git, PR, Jira, and region-switch rules.

Keeping this knowledge beside the Power makes the repository a single source of truth for the current team workflow.

## MCP and secrets

The root `mcp.json` bundles the GitHub MCP Server configuration used by the Power. `mcp/*.example.json` files remain examples for Jira, Figma, and GitHub integrations.

Never commit Jira/Figma/GitHub credentials, API tokens, passwords, certificates, `.env` files, or other secrets. Supply authentication through the team's approved local/environment configuration.

### GitHub MCP

The bundled GitHub MCP is intended to make repository and PR workflows more direct. Prefer exact repository, branch, PR, changed-file, CI, and review lookups over broad searches. The token is supplied through `GITHUB_PERSONAL_ACCESS_TOKEN` and is never stored in this repository.

## Approval and safety model

External or destructive actions require explicit approval at the point of action. Approval is never inferred or reused for another action.

```text
Jira To Do → In Progress     → ask first
Source/test file write       → ask first
Branch creation              → ask first
Commit                       → ask first
Push / PR creation           → ask immediately before
GitHub review/comment        → ask immediately before
In Review → Done             → ask immediately before
```

The workflow must never silently move Jira statuses, push protected branches, create a PR, post GitHub review comments, merge a PR, mark a Jira ticket Done, or fabricate test/build/screenshot/lint/Jira/PR evidence.

## Documentation

- [Architecture](docs/architecture.md)
- [Workflow](docs/workflow.md)
- [Jira Ticket Format](docs/jira-ticket-format.md)
- [Installation](docs/installation.md)
- [Contribution guide](docs/contribution.md)

## Contribution

Treat changes to `POWER.md`, Skills, Steering, MCP configuration, and ticket-format documentation as team-level changes. Validate affected workflows and approval gates before adoption because a change can affect every developer using `NC-Powers`.
