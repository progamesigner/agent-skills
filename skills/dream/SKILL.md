---
name: dream
description: "Consolidate the Muninn memory vault the way sleep consolidates memory: replay the recent diary, promote what was learned into durable notes, rewrite notes so they state only the current rules, archive what was removed, then report. Use for dream, 作夢, 整理記憶, /dream, and as the body of a scheduled dream job."
---

# dream

**Goal:** after a dream, an agent that reads only the vault's entrypoints gets a correct, current, non-contradictory picture, and nothing true has been lost.

This skill is the only definition of a dream. Every machine that dreams installs it from the same repository, so scheduled and interactive dreams do the same thing. Differences between machines and vaults belong in the profile, never in this file.

## Inputs

**Profile.** The scheduled job's prompt supplies it. An interactive dream without one uses the defaults.

| Field | Meaning | Default |
|---|---|---|
| scope | `agent` / `user` for every Muninn call | the session's scope |
| owner | how the vault's owner is named in notes and the report | the user |
| entrypoints | notes an agent reads first; reorganized first | root `MEMORY.md` and every `INDEX.md` |
| evidence | facts collected before the run (repo heads, deployed images); they outrank notes for what they cover, and nothing beyond them is inferred | none |
| protected notes | notes that jobs or skills read by path; they keep their path, title and heading structure | none |
| archive | where removed text goes | `Agents/archives/<YYYY-MM>-cleanup/<area>/historical-<YYYY-MM>-<source-basename>.md` |
| report | optional note path for the full report | none: the diary entry is the report |
| local rules | extra permissions or prohibitions; they win over this file | none |

**Mode.**

- **Scheduled:** unattended. Take `today` and `recent_days` from the job prompt verbatim; the period is the last 7 days. Never ask; anything the rules cannot settle goes to *Needs owner*.
- **Interactive:** the period runs from the last dream to now unless the owner names one. Ask only what the rules cannot settle, in one batch at the end.
- **Dry run:** call no write tool; print every change you would make.

**Stop condition:** if Muninn is unreachable, stop. A scheduled run ends with `STATUS: BLOCKED muninn unreachable`.

## Step 0: Pick up the previous dream

Find the latest diary entry titled `Muninn dream` (older titles: `Muninn 作夢`, `Muninn 週整理`) in the two weeks before the period, or the previous report note if the profile has one. Check each item on its *Needs owner* list (older names: 〈待確認〉, 待翰翰確認) against the vault and later diary entries. Resolved items drop. Open items carry over and are not proposed again as new.

## Step 1: Replay

Read every `Agents/diary/<date>.md` in the period. Skip entries written by scheduled jobs, earlier dreams included. Extract, most valuable first:

1. **Owner corrections:** the owner said an answer, conclusion or approach was wrong, or overrode a default.
2. **Repeats:** the same gotcha hit twice, the same fact looked up again.
3. **Diary-only knowledge:** decisions and durable facts that no durable note holds yet.
4. **Open threads** marked carry-forward and still open.

## Step 2: Consolidate

For each extracted item:

- `recall_memory_notes` first, and update the existing note instead of creating a duplicate. Prefer `edit_memory_note` to a full rewrite.
- Destination: a fact goes to its `Agents/topics/<topic>/` note or the project or workspace section note that covers it; a behaviour correction goes to `Agents/feedback/`. Quote the owner and cite the diary date.
- When the item overrides existing text, rewrite that text to the current rule and archive the old wording (Step 3). Never leave "Superseded", strikethrough or "see correction below" chains.
- Add a new note to its `INDEX.md`. When the target note has a changelog, add a line.

## Step 3: Reorganize and forget

**Scope, in order:** the entrypoints; notes modified since the period began (`recall_memory_notes` with `modified_after`); notes the period's corrections touch; stale notes met in Step 2.

| Situation | Action |
|---|---|
| A later rule overrides an earlier one (strikethrough, "Superseded", "Correction", 已被取代, 更正, a later dated entry or note that contradicts it) | Rewrite so the note states only the current rule. Keep its evidence anchors exactly: dates, who confirmed it, PR numbers, SHAs, message timestamps, page IDs. End with `(revised <today>; earlier wording archived)`. |
| Text has to go | Archive it verbatim first (rules below), then remove it, and leave a link to the archive note in the source. |
| The vault's own evidence makes a whole note obsolete | Retire it in place: frontmatter `status: historical`, first body line `> **Historical (marked <today>).** <why>; successor: [[...]]`. Do not delete or move it, so links keep resolving. Drop it from the live INDEX section or move it under a "Retired" heading. |
| Two notes are true duplicates | Keep one canonical note with everything unique from both; replace the other's body with a redirect to it. |
| A note is over about 30 KB, or a paragraph has grown past a few hundred words | Split by topic, one rule per bullet, governing rule first. The original path becomes a section index. |
| Mechanical drift | Fix malformed frontmatter, INDEX lines pointing at missing notes, notes missing from their INDEX, and wikilinks whose target was renamed (only when the new target is certain). |

**Dates in frontmatter:** `updated` is the evidence date. When you only reorganized, leave `updated` and set `reorganized: <today>`.

**Archive rules.** Path from the profile; `<YYYY-MM>` from `today`; `<area>` is the source's folder under `topics/`, or `workspaces`, or `misc`. One archive note per source note; append when it exists. Frontmatter: `title`, `tags: [memory, historical]`, `source: <original path>, archived <today>`, `updated: <today>`, `status: archived`. The basename must be unique in the vault, because a same-name copy hijacks wikilinks. List each new archive note in that archive's `INDEX.md` (create it with frontmatter if missing).

**Guard rails.**

- Truth comes only from the vault and the profile's evidence. Do not correct facts from your own knowledge, do not call external systems, and do not record an inference the owner has not confirmed.
- Stale-looking text that nothing overrides ("pending", "not yet sent", a planned date now past) stays as written and goes to *Needs owner*. When "pending", "in progress" or "in review" has had no follow-up for more than 14 days, also add a dated line at the top of that note marking it a stale snapshot with no recorded follow-up, and say that this is the agent's judgement, not the owner cancelling it.
- Two notes disagree and nothing decides which wins: change neither; list both paths and the conflicting lines.
- Never drop an owner-confirmed decision, a "do not re-propose" instruction, an ID or a tool gotcha because it looks minor. When unsure whether text is superseded, keep it.
- The profile's protected notes keep their path, title and heading structure.
- Dated reviews, audits, plans and measurements are history: do not update them. Add a one-line dated banner when a reader could mistake one for current rules.

**Write mechanics.** Keep every note at or under about 30 KB; larger writes are cut off mid-call. After every `write_memory_note`, read the note back and confirm its last line arrived. A write that reports a timeout has usually completed: read back before retrying. `edit_memory_note` cannot match a search string that contains a markdown link or a `[[wikilink]]`; anchor on plain text next to it, or rewrite the whole note.

**Proposal only** (list under *Needs owner* with path, evidence and the exact change), unless the profile's local rules grant it:

- Core root files: `Agents/MEMORY.md` (also report its line count against the 200-line cap), `PERSONA.md`, `RULES.md`, `USER.md`, `PROMPT.md`, `HEARTBEAT.md`.
- Anything under `Agents/skills/`, and any change to a skill installed from a repository (propose the edit; the owner changes the repository).
- Deleting or renaming a note.

**Never:** persist, copy or move sensitive personal information (performance, compensation, health) about colleagues or candidates; flag where it sits instead. Never touch anything outside `Agents/` (the owner's own knowledge base may share the vault), whatever the profile says.

## Step 4: Report

`append_diary_entry` with title `Muninn dream`, in English (quote the owner in their own language). Sections, in this order:

1. **Lessons:** the 3 to 7 lessons that matter from the period, each with its diary date.
2. **Rewritten:** one line per note written or edited: path; what was stale or contradictory; what it says now; the basis (which note or evidence).
3. **Archived:** one line per archive note: `archive path <- source path`, and what the removed text was about. "None" when nothing was archived.
4. **Previous open items:** each item carried from the last dream: resolved or still open, with a one-line reason. "None" when there were none.
5. **Needs owner:** numbered so the owner can answer by number; carried items first, marked `(carried)`. Each names the path, the question, a suggestion and the reason.

When the profile has a report note, it carries the full sections and the diary entry is a short version that links to it. A scheduled run then ends with the summary lines its job prompt asks for; an interactive run gives the owner the same sections in the reply. A dream with nothing to do still writes the entry and says so.
