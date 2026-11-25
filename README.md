# GStack

Five Markdown system prompts that bring gstack's review modes into claude.ai
Projects, plus a Claude Code hook that blocks tool use in this repo unless
gstack is installed globally.

## What it does and who uses it

Each file in `chat-prompts/` is a system prompt to paste into a claude.ai
Project. They cover office hours (idea shaping), CEO review (scope and
premise challenge), eng review (architecture review), investigate (root-cause
debugging) and a combined "gstack-chat" that picks a mode. The repo has no
other consumers that it documents.

## Status

Dormant. The repo has no build, test, CI or deploy configuration and deploys
to nothing.

## Layout

| Path | Purpose |
| --- | --- |
| `chat-prompts/*.md` | The five prompts (75 to 139 lines each). |
| `CLAUDE.md` | Instructions for Claude Code in this repo: check that gstack is installed, and stop if it is not. |
| `.claude/settings.json` | Registers one `PreToolUse` hook with an empty matcher, so it runs before every tool call. |
| `.claude/hooks/check-gstack.sh` | Denies every tool call if `$HOME/.claude/skills/gstack/bin` is missing; otherwise prints `{}`. |
| `.gitignore` | Ignores `.claude/settings.local.json` and `.DS_Store`. |

## Run locally

Nothing to run. To use a prompt, open a claude.ai Project, paste the contents
of one file from `chat-prompts/` into its instructions, and start a chat.

To use the Claude Code hook, gstack must exist at `~/.claude/skills/gstack`.
Install it by hand (run by a human):

```bash
git clone --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack
cd ~/.claude/skills/gstack && ./setup --team
```

Then restart Claude Code.

## Test

There are no tests and no test command. To check the hook by hand, run
`bash .claude/hooks/check-gstack.sh` from the repo root. Without gstack
installed it prints a deny JSON object and an install message to stderr. With
gstack installed it prints `{}`.

## Deploy

Nothing is deployed. There is no deploy command or configuration in the repo.

## Environment variables

None are set or required by the repo. The hook reads `$HOME`.

## Do not break

- `.claude/settings.json`: the `PreToolUse` hook with empty `matcher`, which
  runs `.claude/hooks/check-gstack.sh` before every tool call in this repo.
- `.claude/hooks/check-gstack.sh`: the gstack directory check and its deny
  output. It must stay executable (mode 100755 in git).
- `CLAUDE.md`: the gstack check command, `test -d ~/.claude/skills/gstack/bin`.

## Known gaps

- The hook blocks all tool use in this repo when gstack is missing, including
  edits to these Markdown prompts. The repo has no code that uses gstack, so
  this is a heavy gate for five text files. Changing it is a behaviour change
  and needs an owner's decision.
- No canonical copy is named. Similar gstack-chat material may live elsewhere;
  decide which copy is the source before editing prompts in more than one
  place.
