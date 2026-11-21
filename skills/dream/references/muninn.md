# Muninn adapter

How the dream roles map onto a [Muninn](https://github.com/progamesigner/muninn) vault. Every Muninn call carries the profile's scope keys (`agent`, `user`). Paths below assume the agents folder is `Agents`; generic note tools take the full path from the vault root, wrapper tools add the folder themselves.

## Roles

| Role | Where | Read | Write |
|---|---|---|---|
| journal | `Agents/diary/<YYYY-MM-DD>.md` | `read_memory_note`, `recall_memory_notes` | `append_diary_entry` only (never hand-written) |
| durable notes | `Agents/topics/<topic>/<fact>.md`, `Agents/projects/<name>/…`, `Agents/workspaces/<repo>/…` | `recall_memory_notes`, `read_memory_note(s)` | `edit_memory_note`, `write_memory_note(s)` |
| feedback notes | `Agents/feedback/<name>.md` | same | same |
| indexes | `Agents/MEMORY.md` (root), `Agents/*/INDEX.md`, each topic folder's `INDEX.md` | same | same (`MEMORY.md`: proposal only) |
| core files | `Agents/PERSONA.md`, `RULES.md`, `USER.md`, `PROMPT.md`, `MEMORY.md`, `HEARTBEAT.md`, `AGENTS.md` | `read_memory_note` | wrapper tools only, so proposal only |
| archive | `Agents/archives/<YYYY-MM>-cleanup/<area>/historical-<YYYY-MM>-<source-basename>.md` (default) | same | `write_memory_note(s)` |

- **Default entrypoints:** `Agents/MEMORY.md` and every `INDEX.md` it links.
- **Notes modified since a date:** `recall_memory_notes` with `modified_after` (filesystem mtime, so restores can back-date it).
- **Dream title:** `Muninn dream`. Older titles: `Muninn 作夢`, `Muninn 週整理`; their open-item lists were headed 〈待確認〉 or 待翰翰確認.
- **Archive details:** `<YYYY-MM>` comes from `today`; `<area>` is the source's folder under `topics/`, or `workspaces`, or `misc`. Frontmatter: `title`, `tags: [memory, historical]`, `source: <original path>, archived <today>`, `updated: <today>`, `status: archived`. List new archive notes in that archive's `INDEX.md` (create it with frontmatter if missing).
- **Retiring a note:** frontmatter `status: historical`; first body line `> **Historical (marked <today>).** <why>; successor: [[...]]`.
- **Dates in frontmatter:** `updated` is the evidence date. When you only reorganized a note, leave `updated` and set `reorganized: <today>`.
- **Outside the agents folder:** the owner's own notes may share the vault; never touch anything outside `Agents/`.

## Limits and tool quirks

- **Size:** keep every note at or under about 30 KB; larger writes are cut off mid-call. `MEMORY.md` ≤ 200 lines, `USER.md` ≤ 100, `RULES.md` ≤ 40 (enforced on write); report `MEMORY.md`'s line count against its cap.
- **Read back** after every `write_memory_note` and confirm the last line arrived. A write that reports a timeout has usually completed: read back before retrying.
- **`edit_memory_note`** cannot match a search string that contains a markdown link or a `[[wikilink]]`; anchor on plain text next to it, or rewrite the whole note. Inside its `replace_string`, `\"` is stored literally.
- **`list_memory_notes`:** prefer `glob` (vault-root paths such as `Agents/skills/*/SKILL.md`); `path_prefix` has returned nothing for paths that exist.
- **`rename_memory_note`** rewrites every incoming link, index lines included.
