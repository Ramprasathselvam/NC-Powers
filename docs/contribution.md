# Contribution Guide

`NC-Powers` is shared infrastructure for the NissanConnect iOS team. Changes to the Power, Skills, or Steering can affect every developer using the workflow, so contributions should be focused, evidence-based, and reviewed.

## Before changing the Power

1. Identify which command or lifecycle stage is affected.
2. Keep orchestration in `POWER.md`; keep detailed procedures in the owning Skill.
3. Do not duplicate the same rule across Power, Skills, and Steering unless the duplication is intentional and necessary for safety.
4. Preserve explicit approval gates for Jira, source-control, GitHub, and destructive region actions.
5. Preserve the rule that approval for one action does not authorize another action.
6. Never add credentials or secrets.

## Before changing a Skill

- Keep one clear responsibility per Skill.
- Read the relevant Steering before changing project assumptions.
- Report actual evidence only.
- Keep read-only Skills read-only.
- Do not silently expand a Skill to perform the next lifecycle stage.
- Keep external writes behind explicit approval.

## Before changing Steering

Steering is project-specific and should describe facts or conventions of the NissanConnect codebase/team. Prefer concrete, verified examples over generic advice.

When a convention changes in the actual iOS repository, update the corresponding Steering deliberately and review any Skills that depend on it.

## Validation

Before proposing a shared workflow change:

- Test the affected Skill independently where possible.
- Exercise the Power with a representative Jira issue.
- Verify failure, cancellation, and missing-tool paths.
- Verify that the workflow stops at required approval gates.
- Confirm that no approval is inferred from a previous action.
- Confirm that documentation matches the implementation.

## Versioning

Use deliberate version changes for releases that alter shared behavior. Communicate breaking workflow changes to the team before adoption.

## Security

Do not commit Jira/Figma tokens, passwords, certificates, private keys, `.env` files, or other sensitive configuration.
