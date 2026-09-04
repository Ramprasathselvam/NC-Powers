# NC-Powers

Reusable Kiro Powers and Agent Skills for iOS development workflows.

## Purpose

`NC-Powers` is the shared, team-level repository for reusable Kiro workflow assets. It is intentionally separated from individual iOS application repositories so the workflow can be versioned, reviewed, and maintained centrally.

The repository is designed to support a controlled development lifecycle such as:

```text
Jira → Figma → iOS Implementation → Test Cases → Validation → PR → Review → Done
```

## Repository Structure

```text
NC-Powers/
├── plugin.json
├── POWER.md
├── skills/
│   ├── jira-to-ios/
│   ├── figma-to-ios/
│   ├── implementation-workflow/
│   ├── testcase-generation-validation/
│   ├── validation/
│   └── code-review/
├── mcp/
│   ├── jira.example.json
│   └── figma.example.json
├── docs/
│   ├── architecture.md
│   ├── installation.md
│   ├── contribution.md
│   └── workflow.md
├── examples/
│   └── README.md
└── LICENSE
```

> `POWER.md`, Steering files, and Skills generated/validated in Kiro should be added without changing their project-specific content unless the Power maintainer intentionally updates the reusable behavior.

## Design Principles

### Reusable

Power and Skills should work across multiple iOS repositories and should not depend on application-specific class names, paths, or implementation details.

### Project-specific knowledge stays with the project

Each iOS application should keep its own `.kiro/steering/` configuration for architecture, folder structure, coding conventions, design system, testing conventions, and other repository-specific rules.

### Explicit approval for external or destructive actions

The workflow should require explicit user approval before actions such as Jira status transitions, pushing branches, creating pull requests, posting external review comments, or completing Jira issues when the workflow requires it.

### Evidence-based execution

Never claim that a build, test, screenshot, validation step, Jira update, or PR action succeeded unless it was actually performed and verified.

### Small, focused Skills

Each Skill should have one clear responsibility. The Power should orchestrate Skills rather than duplicate their detailed procedures.

## Example project integration

A consuming iOS project can keep project-specific configuration under:

```text
.kiro/
├── steering/
└── skills/
```

The reusable Power from this repository supplies shared workflow behavior, while project steering supplies application-specific context.

## MCP and Secrets

MCP files in this repository are examples/templates only. Do not commit access tokens, passwords, API keys, or other secrets. Authentication should be configured in the team's Kiro/MCP environment according to organizational security policies.

## Contribution

Changes to shared Skills and Power behavior should be reviewed carefully because they can affect multiple consuming projects.

See [`docs/contribution.md`](docs/contribution.md) for the recommended contribution process.
