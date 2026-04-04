---
description: Manage WezTerm panes and launch Claude Code. Split, close, list panes, and start new Claude Code instances.
tools: [Bash]
---

# WezTerm Pane Management

## List Panes

```bash
wezterm cli list
```

Use `PANEID` to identify panes. `SIZE` and `TITLE` show current layout.

## Split Pane

```bash
wezterm cli split-pane [direction] --pane-id <target> [options]
```

**Direction:**
- `--right` / `--left` / `--top` / `--bottom` (default)

**Options:**
- `--pane-id <ID>` — target pane to split (defaults to current)
- `--cwd <path>` — working directory for new pane
- `--percent <N>` — size as percentage
- `--top-level` — split the entire window instead of active pane

Returns the new pane ID.

## Close Pane

```bash
wezterm cli kill-pane --pane-id <ID>
```

## Launch Claude Code

### 1. Send Command

```bash
PANE_ID=$(wezterm cli split-pane --right --pane-id <target> --cwd <directory>)
sleep 2
wezterm cli send-text --pane-id $PANE_ID 'claude --dangerously-load-development-channels server:claude-peers --dangerously-skip-permissions'
printf '\r' | wezterm cli send-text --pane-id $PANE_ID --no-paste
```

### 2. Handle Dialogs

Poll for dialogs and the prompt. Two dialogs may appear depending on settings:

1. **Permission prompt** — "Yes, I accept" (may be skipped if `skipDangerousModePermissionPrompt: true` is set)
2. **Local development dialog** — "local development"

Handle both by polling in a loop until the `❯` prompt appears:

```bash
for i in $(seq 1 60); do
  PANE_TEXT=$(wezterm cli get-text --pane-id $PANE_ID 2>/dev/null)

  # Permission prompt: select "Yes, I accept" and confirm
  if echo "$PANE_TEXT" | grep -q "Yes, I accept"; then
    printf '\x1bOB' | wezterm cli send-text --pane-id $PANE_ID --no-paste
    sleep 0.5
    printf '\r' | wezterm cli send-text --pane-id $PANE_ID --no-paste
    sleep 2
    continue
  fi

  # Local development dialog: confirm with Enter
  if echo "$PANE_TEXT" | grep -q "local development"; then
    printf '\r' | wezterm cli send-text --pane-id $PANE_ID --no-paste
    sleep 2
    continue
  fi

  # Check if Claude Code is ready (dialogs are already handled above,
  # so ❯ here means the input prompt)
  if echo "$PANE_TEXT" | grep -q '❯'; then
    break
  fi

  sleep 1
done
```

## Key Input Tips

- Without `--no-paste`, input is wrapped in bracketed paste mode
- Down arrow: `printf '\x1bOB' | wezterm cli send-text --pane-id $PANE_ID --no-paste`
- Up arrow: `printf '\x1bOA' | wezterm cli send-text --pane-id $PANE_ID --no-paste`
- Enter: `printf '\r' | wezterm cli send-text --pane-id $PANE_ID --no-paste`

## Other Operations

```bash
wezterm cli activate-pane --pane-id <ID>      # Focus a pane
wezterm cli get-text --pane-id <ID>           # Read pane content
wezterm cli send-text --pane-id <ID> 'text'   # Send text to pane
```
