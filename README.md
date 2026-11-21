# agent-skills

Personal agent skills that carry no company data and work on any machine. Each skill is a folder under `skills/` with a `SKILL.md` and, when it needs them, its own `references/`. Every skill is self-contained, because `npx skills` installs only the skill folder.

## Install

```bash
npx skills add progamesigner/agent-skills -g -y
```

Install a subset with `-s <name>[,<name>…]`. Update with `npx skills update -g`.

## Skills

| Skill | What it does |
|---|---|
| `catch-up` | Reads a set of related sources (threads, issues, PRs, tickets, errors, mail, docs) and says what the real problem is, what matters, what is waiting on the user, and drafts their replies. |
| `dream` | Consolidates an agent's long-term memory: replays the journal, promotes durable facts, rewrites notes to the current rules, archives what was removed, then reports. Store-specific details live in an adapter (`references/muninn.md`). |
| `gcloud-remote-login` | Completes `gcloud auth login` on a machine whose browser the user cannot reach. |
| `obsidian-markdown-format` | Writes and edits Obsidian-flavored Markdown: wikilinks, embeds, callouts, tables, math and frontmatter. |
| `taiwanize` | Traditional Chinese (Taiwan) localization and EN↔zh-TW↔JA translation. |

Machine- and vault-specific settings (scope, protected notes, archive paths) belong in the caller's configuration, such as a scheduled job's prompt, not in the skills.
