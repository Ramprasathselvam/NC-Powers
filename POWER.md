# POWER — NissanConnect iOS Ticket Workflow

Orchestrates the existing skills for:
**Jira → Figma → iOS → Test Cases → Validation → PR → Code Review → Done.**

This file sequences skills and enforces gates. It does NOT restate skill
internals — each stage delegates to the skill that owns it (see the skill's
SKILL.md under `.kiro/skills/`).

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
- Jira access uses the environment's Atlassian integration (Atlassian MCP /
  `forge` power); Figma uses the `figma` power. Confirm the tool resolves before
  relying on it; if unavailable, ask the user to paste the data.

---

## Command 1 — `Implement <JIRA-KEY>`

Runs intake → design → discovery → plan → build → tests → evidence, then STOPS.

Steps:
1. `jira-to-ios`: read the ticket and its status.
2. Check status. If status is **To Do**, ask verbatim and WAIT:
   > Jira issue <JIRA-KEY> is currently To Do.
   > I need to move it to In Progress before modifying implementation code.
   > Proceed? [Yes/No]
   - On explicit **Yes**: [GATE: jira-write] via `jira-complete`, transition
     To Do → In Progress. Then continue.
   - On **No**: stop immediately. Make no code or Jira changes.
   - If already In Progress (or beyond), continue without transitioning.
3. `figma-to-ios`: inspect the linked Figma; produce the design mapping + gaps.
4. `ios-architecture`: inspect existing architecture; locate module/factory/
   adapter/stack; report patterns to mirror.
5. Create an implementation plan and confirm scope/approach with the user.
6. `implement`: write source per steering. [GATE: file-write]
7. `implement`: author relevant test cases in the target's style. [GATE: file-write]
8. Capture implementation evidence (only what actually happened): files changed,
   what was built/authored. Do NOT claim tests passed — none were run here.
9. `jira-complete`: add an evidence-based comment to <JIRA-KEY>. [GATE: jira-write]
   The comment states what was implemented + files; it must not fabricate results.
10. STOP.

Must NOT do automatically: validate, create a PR, push a branch, perform code
review, or move Jira to Done/In Review.

---

## Command 2 — `Validate <JIRA-KEY>`

Separate command. Runs the quality gates and reports; never opens a PR or moves
Jira on its own.

Delegates to `validate`. Coverage (run only what applies; report what ran):
- Build.
- Unit tests (`bundle exec fastlane unit_test`).
- UI tests when applicable.
- Acceptance criteria (from `jira-to-ios`).
- Figma comparison (from `figma-to-ios` mapping).
- Navigation / state.
- Loading / success / empty / error states.
- Localization (`L10n`, correct tables).
- Accessibility (identifiers via `AccessibilityProperties`/`AccessibilityHelper`).
- Lint / static analysis (`fastlane lint` / `danger_lint`). Note: Danger fails
  the PR gate on ANY SwiftLint issue (warnings included), so "green" = 0 issues.

Rules:
- Report actual results only. If a check did not run, say so; do not infer.
- If validation FAILS: report root cause and STOP. Do NOT move Jira to In Review.
  Hand back to `Implement <JIRA-KEY>` for fixes.
- If validation PASSES: report results. Moving In Progress → In Review is a
  SEPARATE gated action — ask the user before any such Jira write via
  `jira-complete`. Do not transition on your own.
- A brand/region `configure` (destructive; `reset_git_repo`) is gated and needs a
  clean tree first (workflow.md).

---

## Command 3 — `Create PR for <JIRA-KEY>`

Delegates to `pr-create`. Requires that validation has passed; confirm the
validation status with the user (it is not auto-known between commands).

Steps and gates:
1. Confirm validation passed for <JIRA-KEY> (ask the user to confirm results).
2. Inspect the working tree; stage only intended files; flag any `.env*`/secret.
3. Determine branch + base from the real convention: `features/<group>/<JIRA-KEY>`
   off the group's base (e.g. `features/<group>/base_develop`). Ask the user for
   the current feature group and base branch — do not guess. [GATE: branch]
4. Commit (emoji + imperative + `[<JIRA-KEY>]`); if the pre-commit SwiftLint hook
   rejects, fix lint and retry — never `--no-verify`. [GATE: commit]
5. **Immediately before pushing/opening the PR, require explicit user permission.**
   [GATE: push-pr] Then push `-u` and open the PR filling the template
   (Jira/Spec/Figma links, light+dark proof, SOLID checklist, lint-count table).

Never push to master/develop/release. Never force-push or amend pushed commits.
Never merge. Never transition Jira here.

---

## Command 4 — `Review PR for <JIRA-KEY>`

Delegates to `code-review`. Must inspect the ACTUAL PR diff.

Steps:
1. Fetch the real PR diff (`gh` / GitHub) for <JIRA-KEY>. If it can't be fetched,
   stop — do not review from memory or fabricate findings.
2. Review by concern against steering (VIPER boundaries, no hardcoded tokens,
   correct test style, brand/region gating, SOLID, lint thresholds, a11y).
3. Report findings in chat.
4. Posting review comments/approval to GitHub is EXTERNAL — require explicit user
   permission immediately before posting. [GATE: github-post] Default is
   report-only; never post silently.

---

## Command 5 — `Complete <JIRA-KEY>`

Delegates to `jira-complete`. Moves **In Review → Done**.

Steps:
1. Confirm the PR is merged/approved as required.
2. **Immediately before moving In Review → Done, require explicit user permission.**
   [GATE: jira-write] State the exact transition.
3. On **Yes**: add a completion comment linking the PR, then transition. Report
   what changed. On **No**: stop.

Never transition without the user-confirmed target status. Never fabricate the
comment or PR link. This is the only command that closes the ticket.

---

## Gate summary
| Gate | Command | Guards |
| --- | --- | --- |
| jira-write (To Do→In Progress) | Implement | first code change |
| file-write | Implement | source + test files |
| jira-write (comment) | Implement | evidence comment |
| region configure | Validate | destructive tree reset |
| (ask) In Progress→In Review | Validate | only after pass, separate ask |
| branch / commit / push-pr | Create PR | git writes; push-pr asked immediately before |
| github-post | Review PR | external review comments |
| jira-write (In Review→Done) | Complete | asked immediately before transition |

Read-only stages (Figma inspect, architecture discovery, lint/test runs, PR diff
read) need no gate but still must report only real results.
