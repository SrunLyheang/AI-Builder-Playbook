---
name: plan-app
description: Plan an app before any code is written. Interviews the user in four short rounds, then writes the context/ files, the entry-point file (CLAUDE.md or AGENTS.md) and an ordered build plan. Use when the user has an app idea, says "I want to build...", "help me plan my app", or asks to start coding a new project that has no context/ folder.
model: opus
---

# Plan App

Turn a rough app idea into context files and a build plan that a coding
agent can execute without guessing. Every gap left in a plan becomes an
assumption the agent makes later, so the gaps are closed here, in
conversation, and the answers are written where later sessions read them.

Planning is the step that deserves the strongest model. The frontmatter
sets it in Claude Code only for the turn that invokes the skill; if the
session is on a small model, say so once and suggest switching for the
interview.

## Rules

- Write no application code, scaffold nothing, install nothing. Output
  is Markdown in `context/` plus the entry-point file.
- The user decides. Never silently pick a stack, a feature, or a scope
  cut. Anything left undecided goes under Open Questions in the tracker
  with a working assumption, never into a file as if it were settled.
- Keep it cheap: ask each round's questions together, do not repeat back
  what the user said, and do not re-read a file you just wrote.

## Step 0: Orient

List the working directory.

- `context/` exists: read the files there. Each file that exists is a
  finished round; resume at the first round whose file is missing. If
  everything exists, ask whether the user wants a revision or meant
  `spec-unit`.
- Code but no `context/`: read the manifest and the folder layout, state
  what they already answer (stack, structure, auth library), and
  interview only on the rest.
- Empty: start fresh.

Then read `references/rounds.md`. It holds, for each round, what must be
settled, when to push back, and the skeleton of the file to write.

Ask for the idea in two or three sentences and how much app-building
experience the user has. Explain a term in one line only when they are
new to it.

## Step 1: Four rounds

| Round | Topics | Writes |
| ----- | ------ | ------ |
| 1 Product | product, users, core flow, features and scope | `context/project-overview.md` |
| 2 System | data, auth, risks, stack, folder boundaries, invariants | `context/architecture.md` |
| 3 Design | theme, tokens, components, layout (skip without a UI) | `context/ui-context.md` |
| 4 Rules | conventions, verify commands, protected files, git | `context/code-standards.md` and the entry point |

For each round:

1. **Ask the round's open questions in one message**, numbered. Skip
   what earlier answers or existing code already settle. Next to every
   question the user may not have an answer for, put your recommended
   answer and the reason in one line, so they can reply "ok" to it. Use
   a structured question tool for multiple-choice items if the agent
   has one.
2. **Follow up in one message** on every answer that is still vague,
   contradicts an earlier one, or makes version one too big. An answer
   is vague when two developers could build different things from it
   ("manage their projects", "a nice dashboard", "some kind of login").
   For each: say what is unclear, ask one sharper question (what the
   user clicks, what appears, what is stored), and give your recommended
   answer. Repeat until every answer is concrete or the user has heard
   the concern and chosen anyway.
3. **When the user does not know** (common for stack, database, auth,
   hosting), give two or three options with one line of trade-off each,
   recommend one for reasons tied to this project, and say why the
   others lose here. If they still cannot choose, use the recommendation
   as the working assumption.
4. **Write the round's file** from its skeleton, then give a summary of
   at most three lines and move straight to the next round. The user
   reviews all files together at the end.

The written files are the saved progress; there is no separate notes
file. If a later answer changes an earlier file, edit that file.

A new feature raised at any point gets one check before it goes in: what
need it serves and where it sits in the core flow. Then give a verdict
with the reason (version one, later, or never) and a smaller alternative
when one covers the same need.

## Step 2: Build plan and tracker

Break the build into units and write `context/specs/00-build-plan.md`.

A unit is one scoped, verifiable piece of work that fits in a single
session: "project sidebar with tabs and empty states, placeholder data"
is a unit; "build the dashboard" is a phase. Each unit produces one
result a person can check, stays inside one system boundary, and needs
no decision that belongs to another unit.

Order: dependencies first; auth before the features it protects; a
route before the UI that calls it; a screen with placeholder data before
real data; each package installed in the unit that first needs it. Put
the riskiest core piece early. Merge neighbours that have no standalone
result between them, and split anything that crosses a boundary.

Then write `context/progress-tracker.md`: phase "Planning complete",
Next Up set to unit 01, open questions with their working assumptions,
and each significant decision with its reason and what it beat.

## Step 3: Review and hand off

In one message: list the files written, show the ordered unit list, list
the open questions, and ask the user to read the files and name any
corrections. Apply corrections as edits. The next step is `spec-unit`
for unit 01; do not start it in the same turn.
