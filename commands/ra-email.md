You are composing and sending a professional Rock Agile email.

## IMPORTANT: This command requires user interaction

This command has multiple decision points that require user input. Do NOT skip ahead, assume answers, or run this in a subagent/background task. You MUST stop and wait for the user's response at each decision point marked with **ASK THE USER**.

Specifically, you must always ask the user about the **signature title** (Step 4) and the **tagline toggle** (Step 5), even if you have a confident inferred answer. The user has explicitly asked to be prompted on these every time. "I picked it from context" is not an acceptable substitute - present the menu and wait.

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

**ASK THE USER.** This step is mandatory. Do not skip even if the title seems obvious from context.

Scan the email content to compute a suggested title from the config's `default_title` + `available_titles`:

- Technical / implementation / runbook / migration / code-level content → `Principal Developer` or `Principal Engineer` (prefer whichever is in available_titles)
- Proposal / strategy / contract / business / scoping content → `CEO`
- Mixed or unclear → fall back to `default_title`

Present the suggestion + alternatives as a short numbered menu and **wait for the user's response**:

```
Suggested signature title: Principal Developer

  1. Principal Developer (suggested)
  2. Principal Engineer
  3. CEO
  4. Other (type one)

Press enter to accept the suggestion, or pick a number.
```

Always include "Other" so the user can type something one-off (Founder, Partner, Advisor, etc.) without polluting their config.

Do not proceed to Step 5 until the user has responded.

## Step 5: Tagline toggle (per-email)

**ASK THE USER.** This step is mandatory.

```
Include "Back-Office & ERP Specialists" tagline under your name? (default: {{config_default}})  [y/n/enter for default]
```

Use the config's `include_tagline_by_default` as the default value shown. User can override per-email. Do not persist the override to the config.

Do not proceed to Step 6 until the user has responded.

## Step 6: Format the email body

Take the raw content and format it into professional HTML following the email standards. Key structural rules:

1. **Opening line(s)** - one or two sentences, no heading. **No `<hr>` at the very top of the email** - a red rule with nothing above it reads as orphaned.
2. **Red `<hr>` section divider**
3. **First major section** - heading + content
4. **Red `<hr>` section divider**
5. **Second major section** - heading + content (repeat for additional sections)
6. **Red `<hr>` section divider** before the closing
7. **Closing line** with a clear next step, no heading
8. **Signature** (built in Step 7) - no `<hr>` between the closing and the signature; the signature has its own visual structure
9. Do NOT include "Thanks, John" or "Thanks, [name]" - the signature replaces the typed name. The closing paragraph is followed by "Thanks," on its own line and then the signature block.

**Headerless / single-paragraph emails:** no `<hr>` rules in the body at all. Just opening + closing + signature.

### Heading colors (importance-ranked)

Pick the most important section heading - color it RED. Pick the second most important - color it BLUE. Everything else is BLACK. This is mechanical, not keyword-driven. Always have a red heading and (if applicable) a blue heading - don't leave colors off because "nothing feels urgent enough."

- **Most important: RED** (`color:#EF423C;font-weight:bold;`)
- **Second most important: BLUE** (`color:#5481C9;font-weight:bold;`)
- **Tertiary and beyond: BLACK** (`color:#231F20;font-weight:bold;`)
- **Single-section emails:** that one heading is RED.
- **No-heading emails:** no colored headings needed.

### Inline sub-topic labels

`Downtime window:`, `Pre-flight checks - `, etc. Use **bold** (not italic). Bold matches heading weight and reads more confident.

### Lists

Dash-prefixed lines for unordered. `<ol>` for ordered. Avoid `<ul>/<li>` (inconsistent across clients).

### Section-divider HR markup

Every section divider uses the same form as the top brand accent:

```html
<hr style="border:none;border-top:3px solid #EF423C;margin:24px 0;" color="#EF423C" size="3" noshade>
```

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
- **List the heading-color choices explicitly.** For example: "Red heading: 'The plan, step by step'. Blue heading: 'What to expect'." This gives the user a chance to redirect if the importance ranking is wrong.
- Mention banned-word rewrites or em-dash substitutions if any happened.

## Tips

- If the user says "send" instead of "draft", remind them that this creates a draft for review - it does not send directly.
- If the user pastes a reply to an existing thread, ask for the thread context so the email makes sense.
- If the user provides an argument with the command, treat it as the raw email content or subject to get started.
- If Gmail's auto-signature is configured for this account, remind the user to disable it in Gmail settings - the email already includes a signature and they would otherwise double up.
