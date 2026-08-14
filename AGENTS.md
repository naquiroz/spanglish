# AGENTS.md

## Cursor Cloud specific instructions

`spanglish` is an **Agent Plugin** (skills + a session-start hook), not a deployable
app. There are **no services, servers, ports, or databases** — "running" it means
executing the hook and linting the docs.

### Runtimes

- **Python 3** (stdlib only — no `pip install`, no lockfiles) runs the hook.
- **Node / `npx`** is only needed for the markdown lint. Global `npm install -g` fails
  in this VM (no root on the npm prefix); use `npx` instead, which caches under `~/.npm`.

### Lint (mirrors CI)

CI (`.github/workflows/markdownlint.yml`) runs `markdownlint-cli2`. Reproduce locally
from the repo root:

```bash
npx --yes markdownlint-cli2
```

Config (`.markdownlint-cli2.jsonc`) already sets the `**/*.md` globs, so no args are
needed.

### Core runtime: the SessionStart hook

`scripts/spanglish-session-start` reads `~/.spanglish.json` (user home, **not** in the
repo) and prints JSON that the host client injects as conversation context.

- Without `~/.spanglish.json` it prints `{}` and injects nothing. Create it via the
  `/spanglish-setup` skill, or by hand: `{"level":"soft|mid","overrides":{}}`.
- The output shape is chosen by client env var: `CURSOR_PLUGIN_ROOT` →
  `additional_context`; `COPILOT_CLI` → `additionalContext`;
  `CLAUDE_PLUGIN_ROOT` / `PLUGIN_ROOT` / `CODEX_HOME` → nested `hookSpecificOutput`.

Exercise it end-to-end:

```bash
printf '{"level":"mid","overrides":{}}' > ~/.spanglish.json
CURSOR_PLUGIN_ROOT=/workspace python3 scripts/spanglish-session-start
```

### Notes

- `skills/spanglish/evals/evals.json` is **data only** — behavioral eval cases, no
  in-repo runner.
- There is nothing to build; this repo is content (markdown skills + manifests) plus
  the one Python hook script.
