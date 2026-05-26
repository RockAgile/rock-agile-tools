# Rock Agile Email Standards

This document defines the voice, formatting rules, and HTML template for Rock Agile client-facing emails.

Last updated: 2026-05-26

---

## 1. Voice and Tone

All Rock Agile emails must reflect the brand voice:

- **Direct** - say what we mean, no filler, no buzzwords
- **Confident but not arrogant** - we make specific claims plainly because we've earned them
- **Honest** - including about tradeoffs, limits, and what we're not the right fit for
- **Warm and human** - we're a firm, but we're people; long relationships are part of who we are

### Banned words and phrases

**Never use:** "synergize," "leverage," "empower," "streamline," or "solutions" without something concrete behind the word.

**Never use these AI-tell words/phrases:** "utilize" (use "use"), "delve," "nuanced," "ensure," "tapestry," or "ecosystem"/"landscape" as metaphors.

**Never use em-dashes** in drafted content. Use regular hyphens (-) instead.

**Never use these filler openers:** "Certainly," "Absolutely," "Great question," "Of course," "I'd be happy to," "I hope this finds you well."

**Never use these transitional fillers:** "That being said," "With that in mind," "It's worth noting that," "Furthermore," "Moreover."

**Never use rhetorical flourishes of the form** "the X it deserves/warrants/requires/needs" (e.g. "the focus it deserves," "the attention it warrants"). These are AI tells - just state the thing plainly.

**Never make claims we can't back up** with a number or a story.

---

## 2. Design Principles

- **No tables.** Tables render inconsistently across email clients and are fragile to maintain. Use only block-level elements (`<p>`, `<div>`, `<hr>`) with inline styles.
- **Outlook compatibility.** Many Rock Agile clients use Outlook, which renders HTML using Word's engine. Every element in this template must render correctly in both Outlook and modern email clients (Gmail, Apple Mail, etc.). When a CSS property is unreliable in Outlook, use the equivalent HTML attribute alongside it.
- **Inline styles only.** Gmail strips `<style>` blocks. All styles must be inline on the element.

---

## 3. Style Rules

### Structure

Emails follow a simple pattern:

1. **Brand accent** - a thin red horizontal rule at the top of every email
2. **Opening line** - one or two sentences of context or greeting
3. **Sections with bold headings** - each major topic gets a bold heading on its own line. Content/informational headings are black (#231F20). Action-oriented headings (Next Steps, Action Required, What We Need From You, etc.) are Rock Agile Red (#EF423C)
4. **Dash-prefixed lists** - use `- ` for list items under a heading (not bullets, not numbers unless order matters)
5. **Italic sub-topics** - use italics for secondary topic headers within a section (e.g., *On the spreadsheet categories:*)
6. **Closing** - a clear next step or call to action. No "please don't hesitate to reach out."

### Formatting Details

- **Top accent line** - a 3px Rock Agile Red `<hr>` at the top of the email container. Present on ALL emails (with or without section headings) for consistent brand presence.
- **Content headings** are bold text in black (#231F20) on their own line, followed by content. Same font size as body text - the weight distinguishes them.
- **Action headings** (Next Steps, Action Required, What We Need From You, etc.) are bold text in Rock Agile Red (#EF423C). Red signals "this is where you need to do something."
- **Body text** is 15px, dark gray (#231F20), with comfortable line-height (1.6).
- **Lists** use `- ` prefix. Each item gets its own line. No trailing periods unless the item is a full sentence.
- **Italic sub-topics** are italicized text followed by a colon, on their own line.
- **Links** use Rock Agile Blue (#5481C9).
- **Font stack**: `Arial, sans-serif` - matches Gmail's default "Sans Serif" font. Email clients don't reliably load custom fonts (Montserrat included), so we use the same sans-serif recipients see by default. The brand font (Montserrat) is reserved for documents and print where it can actually load.

---

## 4. HTML Template

```html
<div style="font-family:Arial,sans-serif;font-size:15px;color:#231F20;line-height:1.6;max-width:680px;">

  <!-- Top brand accent line -->
  <hr style="border:none;border-top:3px solid #EF423C;margin:0 0 24px 0;" color="#EF423C" size="3" noshade>

  <!-- Opening paragraph -->
  <p style="margin:0 0 16px 0;">
    {{opening}}
  </p>

  <!-- Content heading (black) -->
  <p style="margin:24px 0 8px 0;font-weight:bold;color:#231F20;">
    {{heading}}
  </p>
  <div style="margin:0 0 16px 0;">
    {{section content - paragraphs wrapped in <p> tags, lists as individual lines}}
  </div>

  <!-- Italic sub-topic (when needed within a section) -->
  <p style="margin:16px 0 8px 0;font-style:italic;color:#4B5A5A;">
    {{sub-topic label:}}
  </p>
  <div style="margin:0 0 16px 0;">
    {{sub-topic content}}
  </div>

  <!-- Action heading (red) - use for Next Steps, Action Required, etc. -->
  <p style="margin:24px 0 8px 0;font-weight:bold;color:#EF423C;">
    {{action heading}}
  </p>
  <div style="margin:0 0 16px 0;">
    {{action section content}}
  </div>

  <!-- Closing paragraph -->
  <p style="margin:24px 0 0 0;">
    {{closing}}
  </p>

</div>
```

### Headerless Emails

For short emails with no section headings (quick replies, single-topic messages), the template simplifies to just the accent line, body, and closing:

```html
<div style="font-family:Arial,sans-serif;font-size:15px;color:#231F20;line-height:1.6;max-width:680px;">

  <hr style="border:none;border-top:3px solid #EF423C;margin:0 0 24px 0;" color="#EF423C" size="3" noshade>

  <p style="margin:0 0 16px 0;">
    {{body content}}
  </p>

  <p style="margin:16px 0 0 0;">
    {{closing}}
  </p>

</div>
```

### Horizontal Rule Notes

The `<hr>` tag uses both CSS and HTML attributes for cross-client compatibility:
- `style="border:none;border-top:3px solid #EF423C;margin:0 0 24px 0;"` - for modern clients (Gmail, Apple Mail)
- `color="#EF423C" size="3" noshade` - HTML attributes for Outlook's Word rendering engine

### List Formatting

Lists use simple line breaks with dash prefixes. Do NOT use `<ul>/<li>` (inconsistent rendering across email clients).

```html
<div style="margin:0 0 16px 0;">
  - First item<br>
  - Second item<br>
  - Third item
</div>
```

### Link Formatting

```html
<a href="{{url}}" style="color:#5481C9;text-decoration:none;">{{text}}</a>
```

---

## 5. Signature

Gmail automatically appends the user's configured signature. **Do NOT include a signature in the generated HTML.** Gmail handles it.

---

## 6. Gmail Draft API Notes

When creating a draft via the Gmail API:

- Use the `htmlBody` field for HTML content
- Gmail will strip `<style>` blocks - all styles must be inline
- Gmail will strip `<script>` tags
- Gmail preserves inline styles on most elements
- The signature is appended automatically by Gmail
- If no recipient email is provided, use `placeholder-{name}@rockagile.io` (e.g., `placeholder-ian@rockagile.io`). If no name is available, use `placeholder-recipient@rockagile.io`. These bounce safely on Rock Agile's servers if accidentally sent.
