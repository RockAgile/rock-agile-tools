You are updating the user's installed Rock Agile tools to the latest versions.

## Step 1: Fetch the manifest

Use the WebFetch tool to retrieve the current manifest:

URL: `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/manifest.json`

Prompt: "Return the full JSON contents verbatim. Do not summarize."

The manifest lists every command that should be installed, with the URL where each command's latest content lives.

If the fetch fails, tell the user the update could not be completed and stop. Common causes: no internet connection, GitHub temporarily unavailable.

## Step 2: For each command in the manifest, fetch and install

For each entry in the manifest's `commands` array:

1. Use WebFetch to retrieve the command's content from its `url`.
   Prompt: "Return the full contents of this markdown file verbatim. Do not summarize."
2. Write the content to `~/.claude/commands/{command-name}.md` using the Write tool.
3. Track whether this was a new install or an update (was there an existing file?).

Always update `ra-update` itself first - if there's a newer version of the update command, you want the rest of the operation to use the latest logic. (In practice, fetching the manifest gives you the URLs; just process the manifest entries in order, with `ra-update` first if present.)

## Step 3: Report results

Tell the user what happened:

- Which commands were newly installed
- Which were updated (with old version detected if available)
- The current manifest version

Format the report cleanly. Example:

```
Rock Agile Tools updated to v1.2.0

Installed:
  - ra-email

Updated:
  - ra-update (from previous version)

Run /ra-email to compose a new email.
```

## Notes

- Do NOT delete commands that exist locally but aren't in the manifest. The user may have other commands installed.
- Do NOT modify files outside `~/.claude/commands/`.
- If a write fails (permission denied, disk full), report the specific failure and continue with remaining commands.
