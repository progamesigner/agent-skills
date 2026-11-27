---
name: dream
description: "Consolidate an agent's long-term memory the way sleep does: replay the recent journal, promote what was learned into durable notes, rewrite notes so they state only the current rules, archive what was removed, then report. Use for dream, 作夢, 整理記憶, /dream, and as the body of a scheduled dream job."
---

# dream

**Goal:** after a dream, an agent that reads only the memory's entrypoints gets a correct, current, non-contradictory picture, and nothing true has been lost.

This skill defines the procedure only. How a particular memory store is addressed (paths, tools, limits) lives in an adapter under `references/`, and how one machine or owner differs lives in the profile. Neither belongs in this file.

## Roles

The procedure works on these roles; the adapter says where each one lives and which tool reads or writes it.

| Role | What it holds |
|---|---|
| journal | dated entries of what happened in each session |
| durable notes | facts, decisions and conventions, one subject per note |
| feedback notes | corrections to the agent's own behaviour |
| indexes | the notes an agent reads to find everything else |
| core files | identity, rules and working memory; changed only by proposal |
| archive | removed text, kept verbatim as history |
| transcripts | what the owner actually said in each session; read only |

## Inputs

**Profile.** The scheduled job's prompt supplies it; an interactive dream without one uses the defaults.

| Field | Meaning | Default |
|---|---|---|
| adapter | which `references/<adapter>.md` maps the roles | `muninn` |
| scope | identity keys the adapter's tools need | the session's |
| owner | how the memory's owner is named in notes and the report | the user |
| entrypoints | notes an agent reads first; reorganized first | the adapter's root index notes |
| evidence | facts collected before the run (repo heads, deployed images); they outrank notes for what they cover, and nothing beyond them is inferred | none |
| transcripts | the owner's own messages from the period, each pointing to its place in the full session record | none: replay the journal only |
| protected notes | notes that jobs or skills read by path; they keep their path, title and heading structure | none |
| archive location | where removed text goes | the adapter's default |
| report | optional note for the full report | none: the journal entry is the report |
| local rules | extra permissions or prohibitions; they win over this file and the adapter | none |

Read the adapter before Step 0. If it is missing, stop.

**Mode.**

- **Scheduled:** unattended. Take `today` and the period from the job prompt verbatim (default: the last 7 days). Never ask; anything the rules cannot settle goes to *Needs owner*.
- **Interactive:** the period runs from the last dream to now unless the owner names one. Ask only what the rules cannot settle, in one batch at the end.
- **Dry run:** call no write tool; print every change you would make.

**Stop condition:** if the memory store cannot be reached, stop. A scheduled run ends with `STATUS: BLOCKED memory unreachable`.

## Step 0: Pick up the previous dream

Find the latest dream entry in the journal (the adapter gives its title and any older titles) in the two weeks before the period, or the previous report note. Check each item on its *Needs owner* list against the memory and later journal entries. Resolved items drop. Open items carry over and are not proposed again as new.

## Step 1: Replay

Read every journal entry in the period. Skip entries written by scheduled jobs, earlier dreams included. When the profile gives transcripts, read the owner's messages from the period's sessions too. They catch what the journal missed (a correction nobody wrote down) and show where a journal entry or note misreports what the owner said. When a message needs context (what it corrects, which option it picks, why), open the full record at its pointer and read around it, not the whole session. Extract, most valuable first:

1. **Owner corrections:** the owner said an answer, conclusion or approach was wrong, or overrode a default.
2. **Repeats:** the same gotcha hit twice, the same fact looked up again.
3. **Journal-only knowledge:** decisions and durable facts that no durable note holds yet.
4. **Open threads** marked carry-forward and still open.

## Step 2: Consolidate

For each extracted item:

- Search before writing, and update the existing note instead of creating a duplicate. Prefer a targeted edit to a full rewrite.
- A fact goes to the durable note that covers its subject; a behaviour correction goes to a feedback note. Quote the owner and cite the journal date.
- When the item overrides existing text, rewrite that text to the current rule and archive the old wording (Step 3). Never leave "Superseded", strikethrough or "see correction below" chains.
- List a new note in its index. When the target note has a changelog, add a line.

## Step 3: Reorganize and forget

**Scope, in order:** the entrypoints; notes modified since the period began; notes the period's corrections touch; stale notes met in Step 2.

| Situation | Action |
|---|---|
| A later rule overrides an earlier one (strikethrough, "Superseded", "Correction", 已被取代, 更正, a later dated entry or note that contradicts it) | Rewrite so the note states only the current rule. Keep its evidence anchors exactly: dates, who confirmed it, PR numbers, SHAs, message timestamps, page IDs. End with `(revised <today>; earlier wording archived)`. |
| Text has to go | Archive it verbatim first, then remove it, and leave a link to the archive note in the source. |
| The memory's own evidence makes a whole note obsolete | Retire it in place: mark it historical, with a first line that says why and names the successor. Do not delete or move it, so links keep resolving. Drop it from the live index or move it under a "Retired" heading. |
| Two notes are true duplicates | Keep one canonical note with everything unique from both; replace the other's body with a redirect to it. |
| A note is over the adapter's size limit, or a paragraph has grown past a few hundred words | Split by topic, one rule per bullet, governing rule first. The original path becomes a section index. |
| Mechanical drift | Fix malformed metadata, index lines pointing at missing notes, notes missing from their index, and links whose target was renamed (only when the new target is certain). |

**Archive rules.** One archive note per source note; append when it exists. Record where the text came from and when it was archived. The archive note's name must not collide with a live note's, because a same-name copy hijacks links. List each new archive note in the archive's index.

**Guard rails.**

- Truth comes only from the memory, the profile's evidence and its transcripts. For what the owner said or decided, transcripts outrank the journal and notes; for system state, evidence outranks all three. Do not correct facts from your own knowledge, do not call external systems, and do not record an inference the owner has not confirmed.
- Stale-looking text that nothing overrides ("pending", "not yet sent", a planned date now past) stays as written and goes to *Needs owner*. When "pending", "in progress" or "in review" has had no follow-up for more than 14 days, also add a dated line at the top of that note marking it a stale snapshot with no recorded follow-up, and say that this is the agent's judgement, not the owner cancelling it.
- Two notes disagree and nothing decides which wins: change neither; list both and the conflicting lines.
- Never drop an owner-confirmed decision, a "do not re-propose" instruction, an ID or a tool gotcha because it looks minor. When unsure whether text is superseded, keep it.
- The profile's protected notes keep their path, title and heading structure.
- Dated reviews, audits, plans and measurements are history: do not update them. Add a one-line dated banner when a reader could mistake one for current rules.

**Proposal only** (list under *Needs owner* with the note, the evidence and the exact change), unless the local rules grant it:

- Core files.
- Skills, including this one: propose the edit; the owner changes the skill's source.
- Deleting or renaming a note.

**Never:** copy a secret (token, password, key) from a transcript into memory, or quote more of a transcript than a note needs. Never persist, copy or move sensitive personal information (performance, compensation, health) about colleagues or candidates; flag where it sits instead. Never touch notes outside the agent's area of the store (the owner's own notes may share it), whatever the profile says.

## Step 4: Report

Append a journal entry with the adapter's dream title, in English (quote the owner in their own language). Sections, in this order:

1. **Lessons:** the 3 to 7 lessons that matter from the period, each with its journal date.
2. **Rewritten:** one line per note written or edited: the note; what was stale or contradictory; what it says now; the basis (which note or evidence).
3. **Archived:** one line per archive note: `archive <- source`, and what the removed text was about. "None" when nothing was archived.
4. **Previous open items:** each item carried from the last dream: resolved or still open, with a one-line reason. "None" when there were none.
5. **Needs owner:** numbered so the owner can answer by number; carried items first, marked `(carried)`. Each names the note, the question, a suggestion and the reason.

When the profile has a report note, it carries the full sections and the journal entry is a short version that links to it. A scheduled run then ends with the summary lines its job prompt asks for; an interactive run gives the owner the same sections in the reply. A dream with nothing to do still writes the entry and says so.
