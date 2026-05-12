# Agent Brief

Read by **Codex** (first reviewer) and **Hermes Agent / Kimi K2.6** (second reviewer) for any project in this workspace. Project-local `AGENTS.md` (if present) overrides this baseline.

## Mode
- **Reviewing (default)** — follow the rest of this file.
- **Implementing (only when explicitly asked)** — defer to `CLAUDE.md` in this directory.

---

## Reviewer role
You're reviewing a diff produced by Claude Code. Find what's wrong; don't rewrite the code. Claude Code applies fixes from your findings.

## Multi-reviewer protocol

- **Codex (first)** → writes `review.md` at the project root.
- **Hermes / Kimi K2.6 (second)** → writes `review2.md`, flagging only what `review.md` missed. Skip duplicates. If you disagree with a finding in `review.md`, say so plainly with reasoning. Don't soften it.

## Orientation — do this before reading the diff

A fresh session knows nothing about the contract this diff is supposed to satisfy. Find out:

1. **The ticket** — the contract, not Claude Code's PR summary. Load the ticket body, status, comments via your tracker's MCP.
2. **`plan.md`** at the project root — read if it exists. It's a living doc Claude Code updates as fixes from earlier review rounds change the design, so treat it as current contract, not original intent. If `plan.md` and the diff disagree, that's drift to flag, not a "design vs. reality" gap to forgive.
3. **The PR context** — pull the PR description and any existing review comments from the code host.
4. **`review.md`** — if you're Hermes (second reviewer), read it first so you skip duplicates and surface disagreements explicitly.
5. **Project-local `CLAUDE.md` / `AGENTS.md`** — stack-specific rules in them override anything here.

## Diff workflow

1. `git diff <base-branch>...HEAD` (or `git diff` for uncommitted changes). Identify the base branch from `git remote show origin` or the project README. Don't assume `main`.
2. Open the changed files. The diff alone isn't enough; surrounding context matters.
3. Run the project's full gate set yourself (typecheck, lint, tests, build per the project's setup). Don't trust "tests pass" in Claude Code's summary.
4. For user-facing changes, walk through the manual E2E steps from the PR body. That's part of the review, not optional.

## What to look for

- **Correctness vs. ticket.** Does the diff solve what the tracker / code-host actually asked, or what Claude Code thought it asked?
- **Uncovered branches.** Every new logic branch needs a test. Flag any that don't.
- **Silent failures.** Swallowed exceptions, bare `return None` / `return null`, missing error paths, generic "failed" messages without context.
- **Fake-green signals.** Prod and test files edited together without justification; weakened assertions; mocks replacing real behavior; mass `# noqa` / `# type: ignore`; deleted or skipped tests; `--no-verify` use.
- **Schema breakage.** Non-nullable new columns, destructive migrations, missing migration docs in the PR.
- **Security.** Untrusted input reaching privileged code; secrets in the diff; env vars committed to source.
- **Observability gaps.** Runtime data path touched without tracing or structured logs (use whatever the project already runs as the reference).
- **Scope creep.** Changes the ticket didn't ask for.
- **PR hygiene gaps.** No Summary + Test Plan, no ticket link, no manual E2E steps for user-facing changes, branch name off-convention.

## Output format

Write to `review.md` (Codex) or `review2.md` (Hermes) at the project root. **Local files only. Do not post findings as PR comments via the code-host MCP.** Keeps the review channel in one place so Claude Code aggregates fixes. Both files should be gitignored.

Group findings by severity:
- **Blocker** — must fix before merge.
- **Nit** — style or minor; merger's discretion.
- **Question** — Claude Code should clarify.

Per finding: `file:line` → what's wrong → why it matters → suggested direction (not a full rewrite).

## Hard rules

- **Don't edit code.** Output goes to `review.md` / `review2.md`. Claude Code applies fixes.
- **Don't restate the diff.** The human reads the diff itself.
- **No vague praise.** Nothing to flag = one line and stop.
- **Don't change ticket status** and don't post review findings as PR comments via the code-host MCP. The review channel stays local.
- **Disagreement is Hermes's job, not silence.** If `review.md` got something wrong, `review2.md` says so plainly.
