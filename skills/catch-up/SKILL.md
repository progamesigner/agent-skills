---
name: catch-up
description: "Catch the user up on a set of related sources (chat threads, issues, pull requests, tickets, error reports, emails, documents, or pasted text): read every one in full, then say what the real problem is, what matters, what is waiting on the user, and draft the replies they owe. Read-only; sends nothing without confirmation. Use when the user hands over one or more links or pastes about the same matter and wants to know what is going on and what they have to do."
---

# catch-up

The user hands over one or more sources, usually about the same matter from different angles: a chat thread, the issue it spawned, an error report, a ticket. Save them the reading. Tell them what the real problem is, what matters, and exactly what is waiting on them.

**Languages:** write the catch-up in the user's language. Write each reply draft in the language of the person it answers, in the register of that conversation.

## 1. Read everything, completely

- Route each source to the tool this agent has for it (the chat, issue tracker, wiki, error tracker, mail or document connector). Read it whole: every reply in a thread, every comment on an issue or pull request, page properties and comments, an error's first and last seen, event count, affected users and release. Skim a diff only when the problem is in the code.
- A source you cannot open (no tool, no access, not found): say so in the output and work with the rest. Never guess its content from the URL or from what others say about it.
- Follow a link found inside a source one hop only, and only when the answer to "what is the problem" depends on it. Say which extra sources you opened.
- Find the user in each system (their chat user, their account on the tracker) so you can spot what is addressed to them: mentions, direct questions, review requests, assignments, asks left unanswered.
- Before calling a question unanswered, search the same place for a later message from the user to the asker. People often answer as a new message instead of in the thread.

## 2. Think before writing

- **Connect the sources.** They usually describe one problem; find it. When they turn out to be unrelated, say so and handle them separately.
- **Separate fact from claim.** "X said the cache is the cause" is a claim; a stack trace or a merged fix is a fact. Cite which source each point comes from.
- **Find the current state:** who is on it, what was tried, what was decided, what is still open, and since when. The latest message often overrides the first.
- **Find what is waiting on the user specifically**, not everything that is open. Someone else's open task is context, unless the user is the one who should push it; then it is a follow-up action.
- **When you know the user's role**, say for each action whether they should do it themselves, hand it to someone (name the owner only when the sources make it obvious), or raise it to someone above. A lead or manager often should not fix it personally.
- On people topics (performance, conflict, blame in a thread), report what was said and never judge a person on the user's behalf.

## 3. Output

Use these sections, with the headings written in the user's language. Lead with the point, keep each bullet to one or two lines, and make the whole thing readable in under a minute. Every point traces to a source; drop a point that has none.

1. **The real problem:** one or two sentences: what is actually wrong, who or what it affects, and how serious it is now.
2. **Summary:** one line per source (title or short name, link): what it says and its current state.
3. **What you should know:** the 3 to 5 facts or risks that change the user's judgement or next move, most important first, each with its source.
4. **What you need to handle:** each action, who should do it, and why now (a deadline, or who it blocks). Say plainly when nothing is waiting on the user.
5. **What you need to reply to:** where and to whom, what they asked or are waiting for, and a draft ready to paste. Leave the section out when there is nothing to answer.
6. **Open questions:** what the sources cannot settle and the user has to add or ask. Leave it out when there is none.

**Reply drafts** answer the question asked, state the decision or next step and its owner, and stop. Format each in the target's own syntax (the chat tool's message format, Markdown for issues and pull requests).

## 4. After the output

- Nothing is sent, posted, commented or written by default. When the user says to send a draft or record something, show the final text and the target once, get a one-line OK, do it, and check that it landed.
- Offer natural follow-ons in one line when they fit, such as opening a ticket for an action that needs one, writing an incident report for an outage, or reviewing a pull request that needs review. Don't start them unasked.
