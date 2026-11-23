---
name: architecture-map
description: Keeps a short architecture map (ARCHITECTURE.md) of a codebase so the agent reuses existing systems instead of duplicating them. Use at the start of any coding task to read only the sections the task needs, at the end of a task to update the sections it changed, and when asked to create, update, check or trim an architecture doc.
---

# Architecture map

`ARCHITECTURE.md` is a map of the codebase for agents and people: which systems exist, where
they live, how they connect, and what is surprising about them. Read it before you build, so
you extend what exists instead of adding a second copy. Update it after, so the next task
starts from a true map.

The skill has three modes: **Read** (start of a task), **Update** (end of a task), and
**Create** (no map yet).

## Find the map

1. A path named in the project's instructions (CLAUDE.md, AGENTS.md) wins.
2. Otherwise look for `docs/ARCHITECTURE.md`, then `ARCHITECTURE.md` at the root
   (match the folder and file name case-insensitively).
3. If there is none, say so in one line and offer Create mode. Don't create it in the middle
   of an unrelated task unless the user asked.

If the project's instructions say how to use the map (which sections to read, when to skip
the update), follow them. They override this skill.

## Mode 1: Read (start of a task)

1. Read the top of the file: the intro and the **"How to read this file"** table.
2. Read the sections the table marks **always** (usually layout, runtime flow, gotchas).
3. Pick the system sections whose "Read when" column matches the task: feature names,
   class or file names, commands. Read only those. If you're not sure where something is
   covered, search the file for the name instead of reading it all.
4. Before you add a new class, module, service or system, check the map. If something
   already does the job, extend it. If the plan would duplicate it, change the plan and say why.
5. If the map disagrees with the code, trust the code. Note the mismatch and fix it in
   Update mode.

No table? List the headings (`grep -n '^#' ARCHITECTURE.md`), pick from those, and offer
to add a table.

## Mode 2: Update (end of a task)

Update when the task changed structure: added, removed, renamed or moved a system, module,
public API, data flow, setting, dependency rule, or test location. Also add a gotcha when you
found a surprising fact, even in a small task.

Skip when nothing structural changed (a fix inside one function, tuning values, visual-only
work, docs-only work) or when the task is investigate-only. Say "no update needed" in the
report.

How to update:

- **Only the sections the task changed.** Don't rewrite, reorder or restyle other sections.
- **Current state, not history.** Replace a sentence that became wrong; don't append
  "since v2 this is…". A task or ticket ID is fine once per change, as a reference, never a
  changelog.
- **New system:** add a subsection under Systems (template below) and a row in the table.
  Fill "Read when" with the words a future task would contain: feature names, class names,
  commands, file names.
- **Removed or renamed:** update or delete the section and its table row, then search the
  whole file for the old name.
- **Gotcha:** one bullet in the gotchas section: the fact, then what it means or what to do.
- **Exact names:** write class, file, function and setting names exactly as in the code, so
  they can be searched. Don't paste code. Don't explain what clear code already says.
- **Size:** aim for at most ~40 lines per system section. When a section grows past ~80,
  trim it, or move the detail into `docs/architecture/<system>.md` and link it from a short
  section. A long map gets skimmed, which defeats it.
- **Check:** every heading has a table row and every row has a heading.
- **Report** in your final summary: `Architecture map: updated §3.4, §6` or
  `Architecture map: no update needed`.

## Mode 3: Create (no map yet)

1. **Survey, don't read everything.** Top-level folders; build and package files
   (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `*.csproj`, `*.uproject`,
   `pom.xml`…); entry points (main, server start, app or game startup); README and agent
   instruction files; `git log --stat -n 50` for the active areas. Sample a few files per
   module.
2. **Find the systems.** A system has its own job and an API other code calls: a service,
   a subsystem, a module, a feature folder, a pipeline stage. Typically 5–15 in a project.
3. **Write it with the template below.** Verify every name you write against the code. Mark
   anything you inferred but couldn't confirm with `(unverified)`.
4. **Keep the first version short** (under ~300 lines). It gets better through updates.
5. **Offer** to add a line to the project's agent instructions (CLAUDE.md or AGENTS.md):
   `Use the architecture-map skill at the start and end of every task.` Ask before editing
   that file.

## Template

```markdown
# <Project> Architecture

<2–3 sentences: what the project is and what it's built with.>

Read this before adding a system. If something below already does what you need, extend it
instead of adding a new one.

**How to read this file (don't read it all):** always read §1, §2 and the last section. Then
read only the §3 sections for the systems your task touches.

| § | What | Read when the task touches… |
|---|---|---|
| 1 | Layout | always |
| 2 | Runtime flow | always |
| 3.1 | <System> | <feature words, class names, commands> |
| 4 | <Optional: config, data, schemas, assets> | <when> |
| 5 | Known gaps and gotchas | always |

When you add a section, add a row here.

## 1. Layout

<Table: folder or module → purpose. Then the dependency rules: what may use what.>

## 2. Runtime flow

<A short diagram or numbered list from startup to the main loop or request handling to
shutdown, naming the systems involved.>

## 3. Systems

### 3.1 <System> (`path/to/system`)

<One paragraph: what it owns and where it starts.>

- **API:** the main classes and functions other code calls.
- **Uses / used by:** the systems it depends on and the ones that depend on it.
- **Settings:** where it's configured, with defaults that matter.
- **Rules:** invariants and "don't" lines (e.g. "all X goes through this; don't call Y elsewhere").
- **Tests:** where they are and how to run them.

## 4. <Optional section>

## 5. Known gaps and gotchas

These are facts about the current code. Fix them only when a task touches them.

- <fact> → <what it means or what to do>.
```

## Working with an existing map

If the map already has its own structure, follow it. Don't restructure it unless the user asks.
You can suggest a missing reading table or gotchas section once.

## Don't

- Don't read the whole map when the table says which parts you need.
- Don't copy in coding rules (they belong in CLAUDE.md / AGENTS.md), README text, or API
  reference. Link to them.
- Don't write names you haven't checked in the code.
