---
name: obsidian-markdown-format
description: "Write and edit Obsidian-flavored Markdown correctly: wikilinks, embeds, callouts, tables, math, tags, task lists and frontmatter properties. Use when creating, editing or reformatting a note meant for an Obsidian vault, or any Markdown file that already uses wikilinks or callouts."
---

# obsidian-markdown-format

**Goal:** the note renders correctly in Obsidian, its links resolve, and an edit changes nothing the task did not ask for.

## Links

- Link to another note in the vault with a wikilink: `[[Note Name]]`. Prefer it over a Markdown link `[text](Note%20Name.md)`.
- Point at part of a note or change the shown text when needed:
  - heading: `[[Note Name#Heading]]`
  - block: `[[Note Name#^block-id]]`
  - display text: `[[Note Name|display text]]`
- Use the note's file name without `.md`. Add the folder path only when two notes share the same name.
- Do not invent a link target. When the note name is uncertain, write plain text and say so.
- Use ordinary Markdown links `[text](https://…)` for external URLs.

## Embeds

- Embed a note, a section or a block with `!` in front of a wikilink: `![[Note Name]]`, `![[Note Name#Heading]]`, `![[Note Name#^block-id]]`.
- Embed an attachment the same way: `![[diagram.png]]`. Set an image's width with `![[diagram.png|300]]`.

## Callouts

- Write a callout as a blockquote whose first line is `> [!type] Optional title`. Every following line of the callout starts with `>`.
- Common types: `note`, `tip`, `info`, `warning`, `danger`, `example`, `quote`. The type is case-insensitive.
- Make a callout foldable with `+` (open) or `-` (closed) after the type: `> [!note]- Details`.

## Tables

- Leave one blank line before every table. Without it, the table can render as plain text.
- Give every table a header row and a delimiter row.
- Keep cells short. Escape a literal pipe as `\|`, also inside a wikilink alias: `[[Note Name\|display text]]`.

## Math

- Inline math: `$...$`.
- Display math: `$$` on its own line, the formula, then `$$` on its own line.
- Do not use `\(...\)` or `\[...\]`. Obsidian does not render them.

## Other syntax

- **Frontmatter:** YAML between `---` lines at the very top of the note. Keep it first, keep its keys, and keep each value's type (list, date, text).
- **Tags:** `#tag` or `#parent/child` in the body, or a `tags` list in frontmatter. A tag cannot contain spaces or consist of digits only.
- **Tasks:** `- [ ] open` and `- [x] done`.
- **Highlight:** `==text==`. **Comment** (hidden in reading view): `%%text%%`.
- **Block ID:** add ` ^block-id` at the end of a paragraph or list item so other notes can link to it.

## Editing rules

- Change only what the task asks. Keep unrelated formatting, links, embeds, callouts, tags and frontmatter as they are.
- Never convert a wikilink to a Markdown link, or the reverse, unless asked.
- Keep headings, lists and code fences valid Markdown.

## Check before you finish

- Every new link to a note in the vault is a wikilink, and its target exists or is marked as uncertain.
- Every table has a blank line before it.
- Math uses `$...$` or `$$` blocks only.
- Callout lines all start with `>`.
- Frontmatter is still the first thing in the note and still valid YAML.
