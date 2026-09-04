# POWER — NissanConnect iOS Ticket Workflow

Orchestrates the existing skills for:
**Jira → Figma → iOS → Test Cases → Validation → PR → Code Review → Done.**

This Power is project-specific to the NissanConnect iOS team. It is optimized to
use Jira as the source of ticket/design context and GitHub MCP for direct PR,
diff, changed-file, CI, branch, and review information where available.

## Context efficiency rules

The workflow must minimize unnecessary discovery without sacrificing correctness.

### Context priority
Use context in this order:
1. Explicit user-provided files, directories, symbols, test files, PR number, or Figma node.
2. Verified context/evidence produced by an earlier NC-Powers stage.
3. Jira ticket data and links, including the Figma link stored in Jira.
4. Targeted repository/GitHub/Figma search.
5. Broad repository discovery only when the above are insufficient.

### Known-file optimization
A developer may provide known files or symbols:

```text
Implement XR-123

Known files:
- NC/NCI/Features/Garage/GarageViewModel.swift
- NC/NCI/Features/Garage/GarageViewController.swift

Known symbols:
- GarageViewModel.loadVehicles()
```

When provided:
- Validate the paths/symbols first.
- Read those targets before broad search.
- Search only for missing dependencies, protocol conformances, navigation,
  factory wiring, API models, tests, or other evidence needed for correctness.
- Never assume supplied files are the complete change set.
- Never skip required architecture/design validation just to save tool calls.

### Cross-stage context reuse
Do not repeat discovery that was already completed by a previous stage in the
same workflow. Preserve and reuse verified requirements, Figma mapping,
architecture findings, known files, symbols, test targets, and evidence.

If persistent task artifacts are supported by the local setup, prefer a compact
per-ticket context such as:

```text
.kiro/nc-powers/XR-123/
├── context.md
├── implementation.md
├── validation.md
└── evidence.md
```

Do not create these files unless the local project/workflow supports them; the
source repository must not be polluted with temporary artifacts unnecessarily.

### GitHub MCP optimization
For PR work, prefer exact GitHub lookups over repository-wide searches:
- `Review PR #456` → fetch PR metadata + actual diff/changed files + CI/reviews as needed.
- If a changed file is already known, inspect that exact file/patch first.
- Use commit/branch/PR references supplied by the user when available.
- Do not search the entire repository when the PR itself identifies the scope.

## Jira ticket standard

`docs/jira-ticket-format.md` defines the team ticket format for:
- New Development / New Feature
- Existing Feature Development / Enhancement
- Bug
- Technical Change / Maintenance without UI
- UI-only Change

For UI work, the **Figma link belongs in Jira** and is the source of truth for
the design workflow. Developers do not need to paste the Figma URL again when
running `Implement <XR-KEY>` if Jira already contains it.

The Jira intake skill classifies the ticket, extracts acceptance criteria and
Figma/Spec links, and reports missing information instead of inventing it.

## Owning skills
| Stage | Skill |
| --- | --- |
| Requirements intake / Jira read | `jira-to-ios` |
| Design mapping | `figma-to-ios` |
| Architecture discovery | `ios-architecture` |
| Build + test authoring | `implement` |
| Quality gates | `validate` |
| Branch/commit/PR | `pr-create` |
| PR diff review | `code-review` |
| Jira transition + comment | `jira-complete` |

## Absolute rules (apply to every command)
- Never infer approval. Approval for one action does NOT approve any other.
- Wait for an explicit `Yes` before any gated step; on `No`, stop immediately.
- Never claim a build/test/review passed unless it actually ran in this session.
- Never fabricate Jira comments, screenshots, test results, or PR results.
- Only `jira-complete` may write to Jira; only `pr-create` may write to git/GitHub.
- Each command is independent. No command auto-triggers another.
- Jira access uses the environment's Atlassian integration; Figma uses the Figma
  integration; GitHub may use the bundled GitHub MCP. Confirm the required tool
  resolves before relying on it; if unavailable, report the limitation.

## Command 1 — `Implement <JIRA-KEY>`

Runs intake → design → discovery → plan → build → tests → evidence, then STOPS.

Steps:
1. `jira-to-ios`: read the ticket, classify its type, and read its status.
2. Check status. If status is **To Do**, ask verbatim and WAIT:
   > Jira issue <JIRA-KEY> is currently To Do.
   > I need to move it to In Progress before modifying implementation code.
   > Proceed? [Yes/No]
   - On explicit **Yes**: transition To Do → In Progress through the Jira write gate.
   - On **No**: stop immediately. Make no code or Jira changes.
   - If already In Progress (or beyond), continue without transitioning.
3. `figma-to-ios`: inspect the Figma link from Jira when UI work applies.
4. `ios-architecture`: inspect existing architecture; locate module/factory,
   adapter/stack; report patterns to mirror.
5. Create an implementation plan and confirm scope/approach with the user.
6. `implement`: write source per steering. [GATE: file-write]
7. `implement`: author relevant test cases in the target's style. [GATE: file-write]
8. Capture implementation evidence (only what actually happened).
9. Add an evidence-based Jira comment through `jira-complete`. [GATE: jira-write]
10. STOP.

Must NOT do automatically: validate, create a PR, push a branch, perform code
review, or move Jira to Done/In Review.

## Command 2 — `Validate <JIRA-KEY>`

Separate command. Runs quality and acceptance gates and reports; never opens a
PR or moves Jira on its own.

Coverage (run only what applies; report what ran): build, unit/UI tests, acceptance
criteria, Figma comparison for UI tickets, navigation/state, loading/success/
empty/error states, localization, accessibility, lint/static analysis, and
brand/region behavior.

If validation fails, report root cause and STOP. If it passes, moving
In Progress → In Review remains a separate gated Jira action.

## Command 3 — `Create PR for <JIRA-KEY>`

Requires successful validation confirmation. Determine branch/base from actual
repo convention. Stage only intended files, create the required commit, and
immediately before push/PR require explicit permission. Never push protected
branches, force-push, amend pushed commits, merge, or transition Jira here.

Use GitHub MCP for PR creation/readback when available; otherwise use the
approved GitHub CLI/integration.

## Command 4 — `Review PR for <JIRA-KEY>`

Review the ACTUAL PR diff. Prefer direct GitHub MCP PR/changed-file/CI/review
lookups and exact paths over broad repository discovery.

Report findings grouped by severity with file/line evidence. Posting comments or
approval to GitHub is external and requires explicit permission immediately before
posting.

## Command 5 — `Complete <JIRA-KEY>`

Confirm PR completion and require explicit permission immediately before the
Jira `In Review → Done` transition. Add the evidence-based completion comment
and transition only on explicit `Yes`.

## Gate summary
| Gate | Command | Guards |
| --- | --- | --- |
| jira-write (To Do→In Progress) | Implement | first code change |
| file-write | Implement | source + test files |
| jira-write (comment) | Implement | evidence comment |
| region configure | Validate | destructive tree reset |
| In Progress→In Review | Validate | only after pass, separate ask |
| branch / commit / push-pr | Create PR | git writes; push-pr asked immediately before |
| github-post | Review PR | external review comments |
| jira-write (In Review→Done) | Complete | asked immediately before transition |

Read-only stages need no gate but still must report only real results.
