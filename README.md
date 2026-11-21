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

Machine- and vault-specific settings (scope, protected notes, archive paths) belong in the caller's configuration, such as a scheduled job's prompt, not in the skills.
