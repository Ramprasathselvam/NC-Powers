# NC-Powers

**NissanConnect Kiro Power for Jira-driven iOS development.**

`NC-Powers` is a team-managed Kiro Power repository created specifically for the NissanConnect iOS codebase. It centralizes the project's workflow, Skills, Steering knowledge, and integration templates so the team can maintain and evolve them in one place.

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

## Repository structure

```text
NC-Powers/
├── plugin.json
├── POWER.md
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
│   ├── installation.md
│   ├── contribution.md
│   └── workflow.md
├── examples/
│   └── README.md
└── LICENSE
```

## Why Steering is here

Unlike a generic iOS Skill library, this repository is intentionally project-specific. The `steering/` files capture NissanConnect knowledge that the Skills need in order to make correct decisions, including:

- VIPER-style MVP architecture and module-factory patterns
- AdapterCenter and networking conventions
- Nissan/Infiniti brand and region behavior
- Swift, SwiftUI, and UIKit conventions
- Design-system tokens and localization
- Test conventions and mock naming
- Git, PR, Jira, and region-switch rules

Keeping this knowledge beside the Power makes the repository a single source of truth for the current team workflow.

If a future team wants a generic iOS Power, the reusable Skills can be extracted and the project-specific Steering can remain separate.

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

The workflow must never silently:

- move Jira statuses
- push to protected branches
- create a PR
- post GitHub review comments
- merge a PR
- mark a Jira ticket Done
- fabricate test, build, screenshot, lint, Jira, or PR evidence

## Project-specific rules

The Skills are deliberately backed by the Steering in this repository. They should not guess application architecture, design tokens, networking stacks, target configuration, or brand/region behavior. When repository evidence is insufficient, the workflow should stop and surface the gap.

## MCP and secrets

The `mcp/*.example.json` files are configuration examples for Jira, Figma, and GitHub integrations. Never commit Jira/Figma/GitHub credentials, API tokens, passwords, certificates, `.env` files, or other secrets. Configure authentication through the team's approved Kiro/MCP environment.

### GitHub MCP

`mcp/github.example.json` provides a GitHub MCP Server example for repository, branch, pull request, review, and related GitHub operations. The GitHub token must be supplied only through the local/team-approved environment and must never be committed to this repository.

## Documentation

- [Architecture](docs/architecture.md)
- [Workflow](docs/workflow.md)
- [Installation](docs/installation.md)
- [Contribution guide](docs/contribution.md)

## Contribution

Treat changes to `POWER.md`, Skills, Steering, and MCP integration examples as team-level changes. Validate affected workflows and approval gates before adoption because a change can affect every developer using `NC-Powers`.
