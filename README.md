# wezterm-panes

A Claude Code plugin for managing WezTerm panes and launching Claude Code instances.

## Features

- List, split, and close WezTerm panes
- Launch Claude Code in new panes with automatic dialog handling
- Send keystrokes and text to panes

## Installation

Add to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "wezterm-panes-plugin": {
      "source": {
        "source": "github",
        "repo": "happy-ryo/wezterm-panes-plugin"
      }
    }
  },
  "enabledPlugins": {
    "wezterm-panes@wezterm-panes-plugin": true
  }
}
```

## Usage

```
/wezterm-panes:control
```

Or just ask Claude Code in natural language, e.g. "open a new pane to the right with Claude Code".

## License

MIT
