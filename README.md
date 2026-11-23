# architecture-map

A [Claude Code](https://code.claude.com) skill that keeps a short **architecture map** of your
codebase in `ARCHITECTURE.md`.

- **Before a task**, Claude reads only the parts of the map the task needs (a reading table at
  the top tells it which), so it reuses existing systems instead of building duplicates.
- **After a task**, Claude updates only the sections the task changed, so the map stays true.
- **No map yet?** Claude surveys the repo and writes a first, short version.

It works for any language or project type: games, web apps, services, tools.

## Install

Pick one.

**A. Copy into a project (shared with everyone who clones the repo)**

Copy `skills/architecture-map/` into your project as `.claude/skills/architecture-map/` and
commit it. Claude Code picks it up in the next session. Run it as `/architecture-map`.

**B. Install for yourself, in every project (plugin)**

In a terminal:

```bash
claude plugin marketplace add tihonovgena/architecture-map[README.md](README.md)
claude plugin install architecture-map@architecture-map
```

Or inside Claude Code: `/plugin marketplace add tihonovgena/architecture-map`, then
`/plugin install architecture-map@architecture-map`. Run it as
`/architecture-map:architecture-map`. Update later with `/plugin marketplace update`.

**C. Copy for yourself, in every project**

Copy `skills/architecture-map/` to `~/.claude/skills/architecture-map/`.

## Set up a project

1. Add this line to the project's `CLAUDE.md` (or `AGENTS.md`). Claude picks skills by their
   description, and this line makes sure it runs on every task:

   ```markdown
   - Use the architecture-map skill at the start and end of every task (map: `docs/ARCHITECTURE.md`).
   ```

2. No map yet? Ask Claude: *"Create the architecture map for this project."*
   Review the first version; it gets better with every task.

Already have an `ARCHITECTURE.md`? The skill follows your existing structure. If it has no
"How to read this file" table, ask Claude to add one; that table is what saves the tokens.

## What a map looks like

```markdown
**How to read this file (don't read it all):** always read §1, §2 and the last section.
Then read only the §3 sections for the systems your task touches.

| § | What | Read when the task touches… |
|---|---|---|
| 1 | Layout | always |
| 2 | Runtime flow | always |
| 3.1 | Auth | login, sessions, tokens, `AuthService` |
| 3.2 | Billing | invoices, Stripe webhooks, `BillingJob` |
| 4 | Known gaps and gotchas | always |
```

The full template is in [`skills/architecture-map/SKILL.md`](skills/architecture-map/SKILL.md).

## Tips

- Override anything in your `CLAUDE.md`: a different path, which sections are "always", task
  types that skip the update. Project instructions win over the skill.
- Keep system sections short (~40 lines). Move deep detail to `docs/architecture/<system>.md`.
- Ask now and then: *"Check the architecture map against the code and fix what's out of date."*

## License

MIT, see [LICENSE](LICENSE).
