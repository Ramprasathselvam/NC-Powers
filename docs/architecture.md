# NC-Powers Architecture

`NC-Powers` is a project-specific Kiro Power for the NissanConnect iOS codebase. It keeps the workflow orchestration, Skills, and the project knowledge required by those Skills in one repository.

## Responsibility split

```text
NC-Powers repository
├── POWER.md       Workflow orchestration and approval gates
├── skills/        Focused workflow capabilities
├── steering/      NissanConnect project/team knowledge
├── mcp/           Integration examples/templates
└── docs/          Usage and maintenance documentation

Actual iOS source repository
└── Application code, tests, Xcode configuration, etc.
```

## Power

`POWER.md` coordinates the lifecycle and delegates detailed work to Skills. It defines command boundaries and approval gates without duplicating every Skill procedure.

## Skills

Skills have focused responsibilities such as Jira intake, Figma mapping, architecture discovery, implementation, validation, PR creation, code review, and Jira completion.

A Skill should use the Steering as its source of project-specific conventions instead of guessing repository patterns.

## Steering

The `steering/` directory intentionally lives in `NC-Powers` because this repository is maintained specifically for the NissanConnect iOS project/team. It contains project knowledge such as:

- Product and brand/region behavior
- VIPER-style MVP architecture
- Project structure and target conventions
- Swift, SwiftUI, and UIKit rules
- Design-system and localization rules
- Testing conventions
- Git/PR/Jira workflow
- Technology and networking conventions

The Steering is therefore part of the Power's project context, not a generic iOS ruleset.

## MCP

MCP provides access to external systems such as Jira and Figma. Credentials must remain outside source control.

## Approval model

Actions that change source control or external systems are gated according to `POWER.md`. Approval is action-specific and must never be inferred or reused for a different action.

## Evidence model

The Power and Skills report only actions and results that were actually observed. They must never fabricate build/test results, screenshots, Jira updates, PR links, review findings, or lint counts.
