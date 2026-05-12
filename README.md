# golden-triad

Three AI coding agents on one codebase. One implements. Two review. You ship.

The whole "framework" is two markdown files at your workspace root plus a convention for where artifacts go. No orchestration code, no daemons.

## The pattern

- **Claude Code** implements on a feature branch.
- **Codex** reviews the diff and writes findings to `review.md`.
- **Hermes (Kimi K2.6)** reads `review.md` and writes only what Codex missed to `review2.md`.
- **You** triage findings, drive fixes, own the merge.

Same-model self-review has documented blind spots. Cross-model review catches what one model misses on its own.

## Files

- [`CLAUDE.md`](./CLAUDE.md) tells Claude Code its job (implementer).
- [`AGENTS.md`](./AGENTS.md) tells Codex and Hermes theirs (reviewers).
- [`WORKFLOW.md`](./WORKFLOW.md) walks through one ticket end-to-end.
- [`AGENT-INSTRUCTIONS.md`](./AGENT-INSTRUCTIONS.md) is what you hand your coding agent to install the workflow.

## Install

Clone the repo and hand `AGENT-INSTRUCTIONS.md` to your coding agent. It'll ask a few setup questions (workspace path, tracker, code host, agent lineup) and install customized versions of the files in the right place.

```bash
git clone https://github.com/vitalune/golden-triad.git
cd golden-triad
# then open Claude Code (or whichever agent) and paste AGENT-INSTRUCTIONS.md
# say: "set this up"
```

## Adapting to your stack

Templates use Linear and GitHub as concrete examples. The pattern works with any tracker (Jira, GitHub Issues, Plane) and any code host. The agent lineup is swappable too: any implementer plus two reviewers from different model families. The one constraint that matters is that implementer and first reviewer shouldn't be the same model.

## License

MIT.
