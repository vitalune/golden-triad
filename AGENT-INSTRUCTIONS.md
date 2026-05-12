# Setup Instructions for Your Coding Agent

You are an AI coding agent. The user has handed you this file to set up the **golden-triad** multi-agent coding workflow in their environment. Follow these steps in order. Stop after Step 6. Do not start using the workflow on real work; this file is setup only.

---

## What you're installing

The golden-triad workflow uses three AI agents on one codebase:

- **Implementer** (default: Claude Code) writes code on a feature branch.
- **First reviewer** (default: Codex) reads the diff, writes findings to `review.md`.
- **Second reviewer** (default: Hermes / Kimi K2.6) reads `review.md`, writes only what the first missed to `review2.md`.

The whole "framework" is two markdown files at the user's workspace root:

- `CLAUDE.md` — implementer brief.
- `AGENTS.md` — reviewer brief.

Plus a third reference doc the user can keep on hand:

- `WORKFLOW.md` — end-to-end walkthrough on one hypothetical ticket.

---

## Step 1 — Read the canonical files

The user has likely cloned or downloaded this repo locally. Look for these three files first:

- `CLAUDE.md`
- `AGENTS.md`
- `WORKFLOW.md`

Check the current working directory. If not there, check one level up (the user may have invoked you from inside a subdirectory of the repo). If still not found, ask the user where they cloned the repo and read from that path.

**Fallback:** if the user doesn't have the repo on disk (e.g., they only have this file pasted into your chat), fetch the files from GitHub raw:

- `https://raw.githubusercontent.com/vitalune/golden-triad/main/CLAUDE.md`
- `https://raw.githubusercontent.com/vitalune/golden-triad/main/AGENTS.md`
- `https://raw.githubusercontent.com/vitalune/golden-triad/main/WORKFLOW.md`

Use your web-fetch capability for this. If you don't have one and the user doesn't have the repo locally, ask them to paste the contents.

Read all three in full before continuing. The customization step depends on actually knowing what's in them; do not skim.

---

## Step 2 — Gather context from the user

Ask the following questions in one message. Wait for answers before proceeding.

1. **Workspace root path.** Where do you keep your projects? (e.g., `~/code/`, `~/dev/`, `~/projects/`.) Files will be placed here so they apply to all projects underneath. If you'd rather install for one specific project only, give that project's path instead.

2. **Ticket tracker.** Linear / Jira / GitHub Issues / Plane / other / none. The templates assume Linear; if you use something else, references will be adjusted.

3. **Code host.** GitHub / GitLab / Bitbucket / Codeberg / other. The templates assume GitHub.

4. **Agent lineup.** Keep the defaults (Claude Code + Codex + Hermes/Kimi K2.6), or substitute? If substituting, name your implementer, first reviewer, and second reviewer.

5. **Project-specific overrides (optional).** Any conventions that should land in a project-local `CLAUDE.md` override? E.g., preferred branch naming, test/lint commands, deploy hazards. Can skip if installing at workspace level for now and add later.

---

## Step 3 — Customize the templates

Based on the user's answers, prepare customized versions of `CLAUDE.md` and `AGENTS.md` (and optionally `WORKFLOW.md`):

- **If tracker isn't Linear:** Replace `mcp__linear-server__*` references with the user's tracker MCP equivalents, or with generic "your tracker's MCP" phrasing if the MCP names aren't known.
- **If code host isn't GitHub:** Replace `mcp__github__*` and `gh pr *` references with the user's equivalents, or with generic phrasing.
- **If agent lineup differs from defaults:** Find-and-replace `Claude Code`, `Codex`, and `Hermes / Kimi K2.6` with the user's chosen agent names. Preserve role assignments (implementer / first reviewer / second reviewer).
- **Otherwise:** Keep the templates as-is.

Do not change the structural sections (workflow steps, hard rules, handoff protocol, anti-patterns). Only swap names and tool references. The structure is the point.

---

## Step 4 — Place the files

- **Workspace install (recommended):** Write the customized `CLAUDE.md` and `AGENTS.md` to the workspace root the user provided. `WORKFLOW.md` is optional but useful as a desk reference; place it alongside if the user agrees.
- **Per-project install:** Write all three files to that project's root instead.

If a `CLAUDE.md` or `AGENTS.md` already exists at the target path, **do not overwrite silently.** Show the user a diff and ask before replacing.

---

## Step 5 — Update `.gitignore`

For every project that will use this workflow, add to its `.gitignore`:

```
review.md
review2.md
```

If installing at workspace level, you can offer to update a global gitignore (`~/.gitignore_global`, configured via `git config --global core.excludesfile`) so it covers every project automatically. **Ask before modifying global config.**

`plan.md` is **not** gitignored; it's a useful design artifact and lives in the repo intentionally.

---

## Step 6 — Verify and report

Confirm:

- Files exist at the expected paths and contain the customized contents.
- `.gitignore` entries are in place.
- The user understands the next action.

Then output a short summary:

- Where files were written.
- What customizations were applied.
- The exact next user action: in any project under the workspace, open the implementer agent and either say `"Work on <ticket-ID>"` to implement against an existing ticket, or `"<rough description>. Scope this for me as a ticket."` to start in scoping mode.

---

## Do not

- Do not start running the workflow on a real ticket. Setup only.
- Do not commit any changes to the user's repo. Writing the workflow files is fine; committing them is the user's call.
- Do not configure agent CLIs, MCP servers, API keys, or login flows. The user owns those.
- Do not modify any files outside the target paths or `.gitignore`.
- Do not skip Step 1. Step 3's customization is unsafe without it.
- Do not "improve" the templates. If you think a section is wrong, surface it as a question in your summary; don't unilaterally rewrite it.
