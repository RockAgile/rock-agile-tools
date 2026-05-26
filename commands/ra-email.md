You are composing and sending a professional Rock Agile email.

## Step 1: Fetch the latest email standards

Before doing anything else, fetch the current Rock Agile email standards using the WebFetch tool:

URL: `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/standards/email-standards.md`

Prompt: "Return the full contents of this markdown file verbatim. Do not summarize or modify."

These standards define the voice, banned words, HTML template, and formatting rules you must follow. If the fetch fails, tell the user and stop - do not proceed without the current standards.

## Step 2: Ensure the signature config exists

Read `~/.claude/rock-agile-signature.json`.

If it exists, parse it and continue.

If it does not exist, this is a first-run setup. Prompt the user for:

- **Name** (e.g., "Jane Doe")
- **Email** (e.g., "jane.doe@rockagile.io")
- **Phone** (formatted like "(xxx) xxx-xxxx" - we will derive the E.164 version automatically)
- **Website** (default: "rockagile.io")
- **Default title** (e.g., "Principal Developer", "CEO")
- **Other titles they sometimes use** (comma-separated list - can be empty)
- **Include "Back-Office & ERP Specialists" tagline by default?** (y/n, default n)

Save the answers to `~/.claude/rock-agile-signature.json` in this format:

```json
{
  "name": "Jane Doe",
  "email": "jane.doe@rockagile.io",
  "phone": "(214) 555-1234",
  "phone_e164": "+12145551234",
  "website": "rockagile.io",
  "default_title": "Principal Developer",
  "available_titles": ["Principal Developer", "CEO"],
  "include_tagline_by_default": false
}
```

Derive `phone_e164` from the human phone by stripping non-digits and prepending `+1` (assume US until/unless we add other countries).

Tell the user the config was saved and proceed.

## Step 3: Gather email details

If the user provided content or context with the command invocation, use it. Otherwise, ask for it.

You need:

- **To** (optional) - recipient email address(es). If not provided, use a placeholder address in the format `placeholder-{name}@rockagile.io` (e.g., `placeholder-ian@rockagile.io`). If no name is available, use `placeholder-recipient@rockagile.io`. This bounces safely on Rock Agile's servers if accidentally sent. Do NOT block on missing recipients.
- **CC** (optional)
- **Subject**
- **Content** - rough notes, bullet points, or polished prose

If only the subject and content are provided, proceed immediately - do not ask for the recipient.

## Step 4: Choose the title for the signature

Scan the email content to suggest a sensible title from the config's `default_title` + `available_titles`:

- Technical / implementation / runbook / migration / code-level content → `Principal Developer` or `Principal Engineer` (prefer whichever is in available_titles)
- Proposal / strategy / contract / business / scoping content → `CEO`
- Mixed or unclear → fall back to `default_title`

Present the suggestion + alternatives as a short numbered menu and ask the user to confirm or override:

```
Suggested signature title: Principal Developer

  1. Principal Developer (suggested)
  2. Principal Engineer
  3. CEO
  4. Other (type one)

Press enter to accept the suggestion, or pick a number.
```

Always include "Other" so the user can type something one-off (Founder, Partner, Advisor, etc.) without polluting their config.

## Step 5: Tagline toggle (per-email)

Ask:

```
Include "Back-Office & ERP Specialists" tagline under your name? (default: {{config_default}})
```

Use the config's `include_tagline_by_default` as the default. User can override per-email. Do not persist the override to the config.

## Step 6: Format the email body

Take the raw content and format it into professional HTML following the email standards. Key rules from the standards:

1. Top red `<hr>` brand accent
2. Opening line
3. Section headings - **default color is BLACK**. Use **RED** only for sections with urgency or exceptional importance (Action Required, What We Need From You, etc.). Use **BLUE** only as a contrast color when paired with a red section in the same email. Do NOT default to red or blue.
4. Inline sub-topic labels (e.g., `Downtime window:`, `Pre-flight checks -`) - use **bold**, not italic. Bold reads more confident and matches the heading weight.
5. Dash-prefixed lists OR numbered `<ol>` lists (both acceptable; numbers when order matters)
6. Closing line with a clear next step
7. Do NOT include "Thanks, John" or similar - the signature (built in Step 7) replaces the sign-off

### Formatting rules

- Match the Rock Agile brand voice: direct, confident, honest, warm. Don't make it sound corporate.
- If the raw content is already well-written, preserve the wording.
- Do NOT add content that wasn't provided or implied. If something is missing, ask.
- Keep paragraphs short. Break up walls of text.
- Follow all banned word rules from the standards.

## Step 7: Build the signature

Pick the template based on the tagline toggle from Step 5:

- Tagline off: WebFetch `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/signatures/template.html`
- Tagline on: WebFetch `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/signatures/template-tagline.html`

(Also check for a per-user override first: `https://raw.githubusercontent.com/RockAgile/rock-agile-tools/main/signatures/{email-prefix}.html` where `email-prefix` is the part of the user's email before `@`. If the override exists, use it instead of the template. This is the escape hatch for fully-custom signatures.)

Interpolate placeholders using the config + chosen title:

- `{{NAME}}` ← `config.name`
- `{{TITLE_UPPER}}` ← the chosen title, uppercased
- `{{EMAIL}}` ← `config.email`
- `{{PHONE}}` ← `config.phone` (display format)
- `{{PHONE_E164}}` ← `config.phone_e164` (used in the `tel:` href)
- `{{WEBSITE}}` ← `config.website`

Append the resulting signature HTML at the end of the email body. The email should end with:

```
Thanks,

[signature block]
```

(Plain "Thanks," followed by the signature - no name on the "Thanks" line, since the signature has it.)

## Step 8: Sanity check before drafting

Scan the entire HTML body (including the signature) for these and fix before creating the draft:

- **Em-dashes (`—`)** - replace with regular hyphen-space-hyphen-space (` - `) or rewrite the sentence. Em-dashes are a hard ban from CLAUDE.md global preferences AND email-standards.md. If you find any, fix them silently. Do not ship em-dashes.
- **Banned words from the standards** - "synergize," "leverage," "empower," "streamline," "solutions" without something concrete, "utilize," "delve," "nuanced," "ensure," "tapestry," "ecosystem" or "landscape" as metaphors. If any are present, rewrite that sentence.
- **Filler openers** - "Certainly," "Absolutely," "Great question," "Of course," "I'd be happy to," "I hope this finds you well." Strike them.
- **Transitional fillers** - "That being said," "With that in mind," "It's worth noting that," "Furthermore," "Moreover." Strike them or replace with cleaner transitions.

This step is non-negotiable. The other-session Claude has been known to slip on em-dashes specifically. Belt-and-suspenders.

## Step 9: Create the Gmail draft

Do NOT ask the user to review the email in the console. Create the Gmail draft immediately. The user reviews in Gmail.

Use the Gmail `create_draft` tool with:
- `htmlBody` ← the complete HTML body (formatted email + signature)
- `subject` ← the email subject
- `to` ← recipient addresses (or placeholder per Step 3)
- `cc` ← CC addresses if provided

After creating the draft, tell the user:

- "Draft created in Gmail. Open Gmail to review and send."
- If a placeholder recipient was used, mention it so the user remembers to swap it before sending.
- If you made any notable choices (title, tagline on/off, banned-word rewrites), mention them in one or two short sentences.

## Tips

- If the user says "send" instead of "draft", remind them that this creates a draft for review - it does not send directly.
- If the user pastes a reply to an existing thread, ask for the thread context so the email makes sense.
- If the user provides an argument with the command, treat it as the raw email content or subject to get started.
- If Gmail's auto-signature is configured for this account, remind the user to disable it in Gmail settings - the email already includes a signature and they would otherwise double up.
