# Installation

NC-Powers is intended to be consumed by Kiro-enabled iOS repositories.

## Recommended setup

1. Add/install the Power through the team's approved Kiro Power/plugin mechanism.
2. Configure Jira and Figma MCP integrations in the local/team Kiro environment.
3. Keep application-specific `.kiro/steering/` files in the consuming iOS repository.
4. Keep only genuinely project-specific Skills in the consuming repository.
5. Verify the Power with a non-destructive Jira ticket before wider team adoption.

## Repository-specific steering

A consuming application should normally contain:

```text
.kiro/
├── steering/
└── skills/        # optional project-specific Skills
```

The shared Power should not require copying project-specific steering into this repository.

## Security

Never commit Jira tokens, Figma tokens, passwords, certificates, or other secrets. Configure credentials through the approved Kiro/MCP environment.
