# NC-Powers Architecture

## Responsibility split

```text
NC-Powers repository
├── POWER.md       Shared workflow orchestration
├── skills/        Reusable task-specific capabilities
├── mcp/           Integration examples/templates
└── docs/          Usage and maintenance documentation

Consuming iOS repository
└── .kiro/
    ├── steering/  Project-specific knowledge and conventions
    └── skills/    Optional project-specific Skills
```

## Power

The Power coordinates the development lifecycle. It should describe the sequence and gates between stages rather than duplicate detailed Skill instructions.

## Skills

Skills should be focused and reusable. A Skill may depend on project steering for repository-specific details.

## Steering

Steering belongs in the consuming application repository when it describes application-specific architecture, folder structure, coding standards, design-system rules, or test conventions.

## MCP

MCP provides access to external systems such as Jira and Figma. Credentials must remain outside source control.

## Approval model

Actions that change external state should have explicit approval gates when defined by the Power workflow. Approval for one action does not implicitly authorize another action.
