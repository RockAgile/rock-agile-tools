# Installing Rock Agile Tools

One-time setup. Takes about 10 minutes.

## What you need

- A Mac or Windows computer
- A Rock Agile Gmail account
- About 10 minutes

## Step 1: Install Claude Code

Download and install Claude Code from https://claude.com/claude-code. Sign in with your Anthropic account.

## Step 2: Connect Gmail

Inside Claude Code, you'll need to connect your Gmail account so the email tool can create drafts on your behalf.

1. Open Claude Code
2. Look for the connectors or integrations menu
3. Find "Gmail" and click connect
4. Sign in with your Rock Agile email when prompted
5. Approve the requested permissions (read/compose drafts)

This authorization is one-time. After that, Claude Code remembers your Gmail connection.

## Step 3: Install the Rock Agile commands

Open a terminal (Terminal.app on Mac, PowerShell on Windows) and paste this single command:

```bash
mkdir -p ~/.claude/commands && \
  curl -sSL https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/commands/ra-email.md \
    -o ~/.claude/commands/ra-email.md && \
  curl -sSL https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/commands/ra-update.md \
    -o ~/.claude/commands/ra-update.md && \
  echo "Rock Agile tools installed. Open Claude Code and type /ra-email to test."
```

That's it. Two small files (`ra-email.md` and `ra-update.md`) are placed in your Claude Code commands folder.

## Step 4: Try it

1. Open Claude Code
2. Type `/ra-email` and press enter
3. Claude will ask you for the subject and content of an email
4. After a moment, a styled draft will appear in your Gmail account
5. Open Gmail to review and send

## Keeping things up to date

Type `/ra-update` in Claude Code any time. It fetches the latest version of every Rock Agile command and installs them automatically. No git, no manual file editing.

## Troubleshooting

**`/ra-email` doesn't appear in Claude Code's command list.**
Restart Claude Code. The commands folder is scanned on startup.

**The email tool says it can't reach Gmail.**
The Gmail connection from Step 2 may have expired or been revoked. Reconnect Gmail in Claude Code's connectors menu.

**The email tool says it can't fetch the standards.**
You may not have an internet connection. The tool requires internet access to fetch the latest brand standards.

**Anything else.**
Contact john.epperson@rockagile.io.
