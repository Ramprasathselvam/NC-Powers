# Installation

`NC-Powers` is a project-specific Kiro Power for the NissanConnect iOS team.

## Recommended setup

1. Install/import `NC-Powers` through the team's approved Kiro Power/plugin mechanism.
2. Make the Power's Skills available to the Kiro environment used with the NissanConnect iOS repository.
3. Keep the `NC-Powers/steering/` files with this Power; they contain the project/team context required by the Skills.
4. Configure Jira and Figma integrations in the team's approved Kiro/MCP environment.
5. Verify the setup with a non-destructive Jira ticket before wider team adoption.

## What belongs in NC-Powers

```text
NC-Powers/
├── POWER.md       # workflow + gates
├── skills/        # workflow capabilities
├── steering/      # NissanConnect project/team knowledge
├── mcp/           # integration examples
└── docs/          # documentation
```

The actual iOS repository remains the source of truth for application code, tests, Xcode configuration, and runtime assets.

If a consuming repository later requires additional local Kiro context, it may keep its own `.kiro/steering/` or `.kiro/skills/` files. Those are extensions, not replacements for the project Steering maintained here.

## Security

Never commit Jira tokens, Figma tokens, passwords, certificates, private keys, `.env` files, or other secrets. Configure credentials through the approved Kiro/MCP environment.

## Verification checklist

Before team rollout, verify:

- Jira read access works.
- Figma read access works where required.
- Skills can read the project Steering.
- `Implement` stops after implementation evidence.
- `Validate` does not create a PR or modify Jira.
- `Create PR` asks before branch/commit/push actions.
- `Review PR` is report-only unless GitHub posting is explicitly approved.
- `Complete` asks immediately before `In Review → Done`.
