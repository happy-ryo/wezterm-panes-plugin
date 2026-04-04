# wezterm-panes

A Claude Code plugin for managing WezTerm panes and launching Claude Code instances.

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

## License

MIT
