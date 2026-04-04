# wezterm-panes

A Claude Code plugin for managing WezTerm panes and launching Claude Code instances.

## Prerequisites

- [WezTerm](https://wezfurlong.org/wezterm/) installed with `wezterm` available in your PATH
- [Claude Code](https://claude.ai/code) installed

## Features

- List, split, and close WezTerm panes
- Launch Claude Code in new panes with automatic dialog handling
- Send keystrokes and text to panes

## Installation

### Using CLI commands

```
/plugin marketplace add happy-ryo/wezterm-panes-plugin
/plugin install wezterm-panes@happy-ryo
```

### Manual configuration

Add to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "happy-ryo": {
      "source": {
        "source": "github",
        "repo": "happy-ryo/wezterm-panes-plugin"
      }
    }
  },
  "enabledPlugins": {
    "wezterm-panes@happy-ryo": true
  }
}
```

## Uninstallation

```
/plugin uninstall wezterm-panes@happy-ryo
```

## Usage

```
/wezterm-panes:control
```

Or just ask Claude Code in natural language, e.g. "open a new pane to the right with Claude Code".

### Available Operations

- **List panes** — Show all WezTerm panes with their IDs, sizes, and titles
- **Split pane** — Create a new pane in any direction (right, left, top, bottom)
- **Close pane** — Kill a specific pane by ID
- **Launch Claude Code** — Split a new pane and start Claude Code with automatic dialog handling
- **Send text/keys** — Send text or keystrokes (arrows, Enter, etc.) to a specific pane
- **Read pane content** — Get the text content of a pane

## License

MIT
