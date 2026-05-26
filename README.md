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

---

## /ra-email

Compose a professionally styled Rock Agile email. You give Claude a subject and rough content; it formats the body in HTML following Rock Agile's brand standards (red accent line, bold headings, correct voice) and creates a draft in your Gmail account. You review and send from Gmail.

### What you need

- A Mac or Windows computer
- A Rock Agile Gmail account
- About 10 minutes for one-time setup

### Step 1: Install Claude Code

Download and install Claude Code from https://claude.com/claude-code. Sign in with your Anthropic account.

### Step 2: Connect Gmail

Inside Claude Code, connect your Gmail account so the email tool can create drafts on your behalf.

1. Open Claude Code
2. Open the connectors or integrations menu
3. Find "Gmail" and click connect
4. Sign in with your Rock Agile email when prompted
5. Approve the requested permissions (read/compose drafts)

This authorization is one-time. After that, Claude Code remembers your Gmail connection.

### Step 3: Install the Rock Agile commands

Open a terminal (Terminal.app on Mac, PowerShell on Windows) and paste this single command:

```bash
mkdir -p ~/.claude/commands && \
  curl -sSL https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/commands/ra-email.md \
    -o ~/.claude/commands/ra-email.md && \
  curl -sSL https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/commands/ra-update.md \
    -o ~/.claude/commands/ra-update.md && \
  echo "Rock Agile tools installed. Open Claude Code and type /ra-email to test."
```

This installs both `/ra-email` and `/ra-update` (the second one is what you'll use later to pull updates).

### Step 4: Try it

1. Open Claude Code
2. Type `/ra-email` and press enter
3. Claude will ask for the subject and content of an email
4. After a moment, a styled draft appears in your Gmail account
5. Open Gmail to review and send

---

## /ra-update

Pulls the latest version of every Rock Agile command from this repo and installs them locally. Run this any time you want to check for updates. Brand standards (voice, formatting) update automatically without this command - only the command logic itself needs `/ra-update`.

Type `/ra-update` in Claude Code. It does the rest.

---

## Troubleshooting

**`/ra-email` doesn't appear in Claude Code's command list.**
Restart Claude Code. The commands folder is scanned on startup.

**The email tool says it can't reach Gmail.**
The Gmail connection from Step 2 may have expired or been revoked. Reconnect Gmail in Claude Code's connectors menu.

**The email tool says it can't fetch the standards.**
You may not have an internet connection. The tool requires internet access to fetch the latest brand standards.

**Anything else.**
Contact john.epperson@rockagile.io.

---

## How it works

The hosted-thin-client model:

- Standards files are hosted in this repo. Commands fetch them via WebFetch at runtime, so the user always uses the current version.
- Commands themselves are tiny markdown files installed locally in `~/.claude/commands/`. They orchestrate the work (gather input, format, call MCP tools) but defer styling rules to the hosted standards.
- `/ra-update` re-fetches every command from this repo per `manifest.json`. Updates require no git knowledge from the user.

## Contributing

Email standards and command logic are updated by John Epperson. To request changes, contact john.epperson@rockagile.io.
