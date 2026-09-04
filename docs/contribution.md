# Contribution Guide

NC-Powers is shared infrastructure for a large iOS team. Changes can affect multiple consuming repositories, so keep contributions focused and reviewable.

## Before changing a Skill or Power

1. Identify whether the change is generic or project-specific.
2. Prefer reusable behavior over application-specific assumptions.
3. Check whether the rule belongs in a Skill, Power, or consuming project's Steering.
4. Avoid duplicating instructions across layers.
5. Preserve explicit approval gates for external or destructive actions.
6. Never add secrets or credentials.

## Validation

Before proposing a shared workflow change:

- Test the affected Skill independently where possible.
- Test the Power workflow with a representative Jira issue.
- Verify failure and cancellation paths.
- Verify that the workflow stops at required approval gates.
- Confirm that documentation matches the implementation.

## Versioning

Use deliberate version changes for releases that alter shared behavior. Communicate breaking workflow changes to consuming teams before adoption.
