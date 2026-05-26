# Rock Agile Tools

Slash commands and brand standards for Rock Agile team members using Claude Code.

## What's here

- `commands/` - Claude Code slash commands. Each one is a self-contained skill that lives in `~/.claude/commands/` on the user's machine.
- `standards/` - Brand standards (voice, formatting, HTML templates). Commands fetch these directly from this repo at runtime, so updates flow through automatically.
- `manifest.json` - Lists every command available for install. Used by `/ra-update` to keep installations current.

## Available commands

| Command | Purpose |
|---------|---------|
| `/ra-email` | Compose a professionally styled email and create a Gmail draft |
| `/ra-update` | Update all installed Rock Agile commands to the latest version |

## Installing

See [INSTALL.md](INSTALL.md) for setup instructions.

## Updating

Once installed, run `/ra-update` in Claude Code to pull the latest versions of every command. Brand standards (voice, formatting, etc.) update automatically - no command needed.

## How it works

The hosted-thin-client model:

- Standards files are hosted in this repo. Commands fetch them via WebFetch at runtime, so the user always uses the current version.
- Commands themselves are tiny markdown files installed locally in `~/.claude/commands/`. They orchestrate the work (gather input, format, call MCP tools) but defer styling rules to the hosted standards.
- `/ra-update` re-fetches every command from this repo per `manifest.json`. Updates require no git knowledge from the user.

## Contributing

Email standards and command logic are updated by John Epperson. To request changes, contact john.epperson@rockagile.io.
