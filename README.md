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
ln -s ~/handoff/handoff ~/.local/bin/handoff
```

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

Codex opens, reads what Claude did, writes a summary in `.handoff/HANDOFF.md` and continues the task.

Codex hits its limit too? Run `handoff opencode`. OpenCode gets the work from both Claude and Codex.

## How it works

1. Finds the newest session of the old agent for this folder (agents save their chats on your disk).
2. Saves a file in `.handoff/` with:
   - your messages, word for word
   - the old agent's last reply
   - `git status` and `git diff --stat`
   - the full chat
3. Starts the new agent and asks it to read that file, write a summary in `.handoff/HANDOFF.md`, and continue.

Your exact messages are saved as-is, so rules like "don't touch the database code" are never lost, even if the summary misses them.

## Options

| Option | What it does |
|---|---|
| `--from claude` | Pick the old agent yourself (default: the newest session in this folder) |
| `--no-launch` | Only save the file and print the prompt, don't start the new agent |

## Notes

- `.handoff/` is added to `.git/info/exclude`, so your chats are never committed.
- For Antigravity, tool outputs are not saved (only your messages, its replies and the tools it called).
