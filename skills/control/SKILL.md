---
name: control
description: "Manage WezTerm panes and launch Claude Code instances via wezterm cli. Split, close, list panes, send text/keystrokes, and read pane content. Handles the full Claude Code launch sequence including dialog automation (permission prompt, local development dialog) and polling for readiness. Use this skill whenever the user wants to: split or manage WezTerm panes, launch Claude Code in a new pane, run multiple Claude instances side by side, send commands or keystrokes to another pane, read pane output, or arrange terminal layout with wezterm cli."
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

The default launch command includes `--dangerously-skip-permissions` so the new instance can operate autonomously, and `--dangerously-load-development-channels server:claude-peers` for peer communication. Adjust these flags if the user requests different behavior.

```bash
PANE_ID=$(wezterm cli split-pane --right --pane-id <target> --cwd <directory>)
```

Wait for the shell to be ready before sending the command — poll for a shell prompt instead of using a fixed sleep:

```bash
for i in $(seq 1 10); do
  PANE_TEXT=$(wezterm cli get-text --pane-id $PANE_ID 2>/dev/null)
  if echo "$PANE_TEXT" | grep -qE '(\$|#|>|❯)'; then
    break
  fi
  sleep 1
done
wezterm cli send-text --pane-id $PANE_ID 'claude --dangerously-load-development-channels server:claude-peers --dangerously-skip-permissions'
printf '\r' | wezterm cli send-text --pane-id $PANE_ID --no-paste
```

### 2. Handle Dialogs

Poll for dialogs and the prompt. Two dialogs may appear depending on settings:

1. **Permission prompt** — "Yes, I accept" (may be skipped if `skipDangerousModePermissionPrompt: true` is set)
2. **Local development dialog** — "local development"

Handle both by polling in a loop until the `❯` prompt appears. The permission prompt needs a down arrow to select "Yes, I accept" and then Enter to confirm. Send the down arrow each time the prompt is detected — this is safe even if the cursor is already on the right option.

```bash
LAUNCH_OK=false
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
    LAUNCH_OK=true
    break
  fi

  sleep 1
done

if [ "$LAUNCH_OK" != "true" ]; then
  echo "Timed out waiting for Claude Code to start in pane $PANE_ID"
fi
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
