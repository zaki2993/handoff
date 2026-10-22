# handoff

Hit your limit in one AI coding agent? Move your session to another one without losing your work.

Works on Linux with:
- Claude Code (`claude`)
- Codex (`codex`)
- OpenCode (`opencode`)
- Antigravity CLI (`agy`)
- pi (`pi`)

## Install

You need Python 3. Git is recommended (see the tip below). No other dependencies.

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

When your agent hits its limit:

1. Open a terminal in your project folder (the same folder you were working in with the old agent).
2. Run `handoff` with the name of the agent you want to move to:
   ```
   handoff codex
   ```
   You can use `claude`, `codex`, `opencode`, `agy` or `pi`. You don't need to say which agent you're coming from, handoff finds it.
3. The new agent opens and waits. If you want a different model, pick it with `/model`.
4. Type `go`. The agent reads what the old one did and continues the work.

To skip the wait and start right away: `handoff codex --go`

**Tip: work in a git repo.** Git records every file change, even the ones you or a script made, so the new agent sees exactly what changed. Without git, it only has the old chat to go on. No git yet? Run `git init` in your project folder.

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
| `--clean` | Delete the `.handoff/` folder in this project (asks you to type `y` first) |

## Notes

- `.handoff/` is added to `.git/info/exclude`, so your chats are never committed.
- Old files in `.handoff/` are never deleted for you. Run `handoff --clean` when you don't need them anymore.
- Long tool outputs are cut in the middle (the start and the end are kept, since errors are usually at the end).
- Respects `CODEX_HOME`, `CLAUDE_CONFIG_DIR`, `XDG_DATA_HOME`, `OPENCODE_DB`, `PI_CODING_AGENT_DIR` and `PI_CODING_AGENT_SESSION_DIR` if you set them.
