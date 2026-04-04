# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Claude Code plugin (`wezterm-panes`) that manages WezTerm terminal panes and launches Claude Code instances in new panes. Distributed as a Claude Code plugin via the `.claude-plugin/` directory.

## Architecture

- `.claude-plugin/plugin.json` — Plugin manifest (name, version, description)
- `skills/control/SKILL.md` — The skill definition that Claude Code loads when `/wezterm-panes:control` is invoked. Contains all WezTerm CLI commands for pane management (list, split, close, send-text, get-text) and the multi-step Claude Code launch sequence with dialog handling.
- `install.cmd` — Windows CMD bootstrap script for installing Claude Code (not part of the plugin itself)

## Key Concepts

- The plugin works by teaching Claude Code the WezTerm CLI commands via a skill markdown file — there is no compiled code or runtime.
- Panes are identified by `PANEID` from `wezterm cli list`.
- Launching Claude Code in a new pane requires: splitting a pane, sending the `claude` command, polling for the "local development" dialog with `wezterm cli get-text`, sending Enter to confirm, then polling for the `❯` prompt.
- Key input uses `--no-paste` flag to avoid bracketed paste mode; special keys use escape sequences (e.g., `\x1bOB` for down arrow).
