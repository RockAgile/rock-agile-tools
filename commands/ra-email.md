You are composing and sending a professional Rock Agile email.

## Step 1: Fetch the latest email standards

Before doing anything else, fetch the current Rock Agile email standards using the WebFetch tool:

URL: `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/standards/email-standards.md`

Prompt: "Return the full contents of this markdown file verbatim. Do not summarize or modify."

These standards define the voice, banned words, HTML template, and formatting rules you must follow. If the fetch fails, tell the user and stop - do not proceed without the current standards.

## Step 2: Gather email details

If the user provided content or context with the command invocation, use it. Otherwise, ask for it.

You need these pieces:

- **To** (optional) - recipient email address(es). If not provided, use a placeholder address in the format `placeholder-{name}@rockagile.io` (e.g., `placeholder-ian@rockagile.io`). If no name is available, use `placeholder-recipient@rockagile.io`. This bounces safely on Rock Agile's servers if accidentally sent. Do NOT block on missing recipients.
- **CC** (optional) - CC recipients
- **Subject** - email subject line
- **Content** - the body of the email. This can be rough notes, bullet points, or polished prose. You will format it.

If only the subject and content are provided, proceed immediately - do not ask for the recipient.

## Step 3: Format the email

Take the raw content and format it into professional HTML following the email standards you just fetched:

1. Write an opening line if one isn't provided
2. Organize content into sections with bold headings (black for content sections, red for action sections per the standards)
3. Use dash-prefixed lists for bullet-style content
4. Use italic sub-topics where appropriate
5. Write a closing line with a clear next step if one isn't provided
6. Do NOT append a signature - Gmail auto-appends the user's configured signature

### Formatting rules

- Match the Rock Agile brand voice from the standards: direct, confident, honest, warm. Don't make it sound corporate.
- If the raw content is already well-written, preserve the wording. Don't rewrite things that don't need rewriting.
- Do NOT add content that wasn't provided or implied. If something is missing, ask - don't fill in.
- Headings should be the same font size as body text - just bold, with color per the standards (black for content, red for action).
- Keep paragraphs short. Break up walls of text.
- Follow all banned word rules from the standards.

## Step 4: Create Gmail draft immediately

Do NOT ask the user to review the email in the console. Create the Gmail draft immediately after formatting. The user will review the rendered email directly in Gmail where they can see the actual styling.

Use the Gmail `create_draft` tool with:
- `htmlBody` field set to the complete formatted HTML
- `subject` field set to the email subject
- `to` field set to recipient addresses (or placeholder per Step 2)

After creating the draft, tell the user:
- "Draft created in Gmail. Open Gmail to review and send."
- If a placeholder recipient was used, mention it so the user remembers to swap it before sending.
- If you made any notable formatting decisions, mention them briefly.

## Tips

- If the user says "send" instead of "draft", remind them that this creates a draft for review - it does not send directly.
- If the user pastes a reply to an existing thread, ask for the thread context so the email makes sense.
- If the user provides an argument with the command, treat it as the raw email content or subject to get started.
