# handoff

Hit your limit in one AI coding agent? Move your session to another one without losing your work.

Works on Linux with:
- Claude Code (`claude`)
- Codex (`codex`)
- OpenCode (`opencode`)
- Antigravity CLI (`agy`)

## Install

You need Python 3 and git. No other dependencies.

```
git clone https://github.com/zaki2993/handoff.git ~/handoff
chmod +x ~/handoff/handoff
mkdir -p ~/.local/bin
ln -s ~/handoff/handoff ~/.local/bin/handoff
```

If `handoff` is not found after this, add `~/.local/bin` to your PATH (most distros already do).

Check it works:

```
handoff --help
```

## Use

Run it from your project folder (the same folder where you started the old agent):

```
handoff codex
```

Change `codex` to the agent you want to switch to: `claude`, `codex`, `opencode` or `agy`.

The new agent waits so you can pick a model with `/model`. Type `go` to start. Use `handoff codex --go` to skip the wait.

**Tip: use it in a git repo.** Git shows exactly what changed on disk, even changes made by scripts or by you. Without git, the new agent only has the chat transcript to go on. If your project has no git yet, run `git init` in it.

## Example

You're working in Claude Code and hit the limit:

```
cd ~/projects/myapp
handoff codex
```

```
from: claude  (last active 2026-10-22 14:30)
saved: .handoff/2026-10-22_14-31-05_claude-to-codex.md
starting codex...
```

Codex opens and replies:

```
Ready. Pick a model with /model if you want, then type go.
```

Pick a model from the `/model` menu (or skip it), then type `go`. Codex reads what Claude did, writes a summary in `.handoff/HANDOFF.md` and continues the task.

Codex hits its limit too? Run `handoff opencode`. OpenCode gets the work from both Claude and Codex.

## How it works

1. Finds the newest session of the old agent for this folder (agents save their chats on your disk).
2. Saves a file in `.handoff/` with:
   - your messages, word for word
   - the old agent's last reply
   - `git status` and the full `git diff HEAD` (staged + unstaged changes)
   - the full chat
3. Starts the new agent, which waits for you to pick a model and type `go`.
4. The agent reads that file, writes a summary in `.handoff/HANDOFF.md`, and continues.

Your exact messages are saved as-is, so rules like "don't touch the database code" are never lost, even if the summary misses them.

## Options

| Option | What it does |
|---|---|
| `--from claude` | Pick the old agent yourself (default: the newest session in this folder) |
| `--go` | Start working right away, skip the wait to pick a model |
| `--no-launch` | Only save the file and print the prompt, don't start the new agent |

## Notes

- `.handoff/` is added to `.git/info/exclude`, so your chats are never committed.
- Long tool outputs are cut in the middle (the start and the end are kept, since errors are usually at the end).
- Respects `CODEX_HOME`, `CLAUDE_CONFIG_DIR`, `XDG_DATA_HOME` and `OPENCODE_DB` if you set them.
