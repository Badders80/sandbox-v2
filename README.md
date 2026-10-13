# V2 working notes

This folder is the plan. It is not the product, and it is not a git repo of its own.

| Role | Path |
|---|---|
| Working plan | `/home/evo/sandbox/v2/PLAN.md` (this folder) |
| Locked brief | `/home/evo/evolution/engine_v2/OUTCOME.md`, then `CONTINUE.md` |
| Old brief, audit only | `/home/evo/evolution/engine_v2/DoD.md` |
| Forensic background | `/home/evo/sandbox/v2/background/` |
| Code, after the word **build** | `/home/evo/evolution/engine_v2/website` |

`engine_v2/` itself is not the repo. Do not `git init` it. The three brief files stay there.

## What to open

Planning: this folder.

Building, only after you say build: `evolution/engine_v2/website`, once that repo exists.

## Do not add instruction files

Do not add `CLAUDE.md`, `AGENTS.md`, or a skill file in this folder or in `engine_v2/`. The brief and `PLAN.md` are the instructions.

Parent files already on disk will still load, and they describe the old estate. For this work they are noise:

- `/home/evo/AGENTS.md` tells every session to block React until design tokens are locked, and to load `engine/SSOT/doc/CANONICAL_FACTS.md` on turn 1. The v2 outcome says tokens are not a gate, and the product numbers are in `OUTCOME.md`.
- `/home/evo/CLAUDE.md` and `/home/evo/evolution/CLAUDE.md` boot `evolution/engine/website`. That is the current live app.
- `/home/evo/sandbox/AGENTS.md` requires `just check`, a venv, and no imports from `evolution/`. This folder is markdown. Reading the brief is the work. Copying engine source into this folder is not.
- `/home/evo/evolution/docs/superpowers/plans/2026-10-09-v2-repo-setup-streamlining.md` is a different job: infra fixes on the old seven-repo engine. Leave it. Do not follow it.
- Orca was removed on 2026-10-13. Do not reinstall it, and do not follow any leftover note that says to.

`DoD.md` says the plan is `OUTCOME.md`. Where those two disagree, `OUTCOME.md` wins. The rulings are in `PLAN.md`.
