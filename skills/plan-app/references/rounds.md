# Rounds

For each round: what must be settled, when to push back, and the
skeleton of the file to write. The "settle" lists are what the file
needs, not a script; ask only what is still open.

Rules for every file: concrete over adjectives ("imports 1,000 rows in
under ten seconds", not "fast"); the same name for the same thing in
every file; no placeholder left behind (an undecided section says
"Undecided, see progress tracker"); rename or drop sections that do not
fit the project.

## Round 1: Product

**Settle**

- What the app does, in one sentence, as an action ("turns a bank export
  into a monthly spending dashboard"), not a category.
- The one primary user and what they are trying to get done. Whether a
  second user type has to exist in version one.
- The core flow from first open to the moment of value, screen by
  screen, including the empty state, a return visit, and what the user
  sees when a step fails.
- Every feature the user imagines, then the three that make version one.
  For the rest: later or never.

**Push back when** the user is "everyone"; a step hides work behind a
verb such as process, manage, sync, or analyze; only the happy path is
described; a "small" feature is large (notifications, payments,
real-time, offline, multi-language, admin panel, mobile app); or nothing
is out of scope. Useful prompts: "better at which one thing, for which
user?", "does it break if this updates after a refresh?", "how many
users in the first three months?", "with a two-week deadline, what
would you cut?"

**Write `context/project-overview.md`**

```
# [Project name]
## Overview            one paragraph: what, for whom, which problem
## Goals               numbered, measurable
## Core User Flow      numbered; each step names what the user does and sees
## Features            grouped by category
## In Scope
## Out of Scope
## Success Criteria    numbered, checkable by a person
```

Out of Scope is the section that stops scope creep. Make it long and
specific, and include what an agent would add unprompted: dark mode
toggle, admin panel, email notifications, analytics, i18n.

## Round 2: System

**Settle**

- The things the app stores (the nouns of the core flow), their
  important fields, who owns each, and how they relate. Files: what
  kind, how large, where they live. What happens to a user's data when
  the account is deleted.
- Whether version one needs accounts. How users sign in. Who may read,
  change, and delete each thing. What is public.
- The part the user is least sure how to build. Anything slow, and what
  the user sees while waiting. Outside services: cost, limits, and
  behavior when they are down. Anything touching money, private data,
  or messages to other people.
- The stack, layer by layer, only for layers a version-one feature
  uses: language and framework, UI, database and access layer, auth,
  file storage, background jobs, external APIs, hosting, testing. Ask
  first what the user already knows and what is fixed.
- Which folder owns which responsibility, with exact names, and what
  each must never do.
- At least four invariants. Propose candidates from the answers so far
  (every mutation checks ownership; secrets are read only on the
  server; slow work never runs in a request handler).

**Push back when** a relationship is ambiguous (one owner or many?);
large files are headed for the database; roles appear with no feature
that needs them; auth is to be built by hand without a reason; a tool
is chosen for hype or for scale the app will not see; two tools
overlap; or the user says nothing is hard (name the part you think is
hardest and ask how it works). Weigh "the user already knows it"
heavily: an unfamiliar stack on an unfamiliar problem is two risks.

**Write `context/architecture.md`**

```
# Architecture
## Stack                  table: Layer | Technology | Role (pin a major version where it changes how code is written)
## System Boundaries      `folder/`: what it owns, what it must not do
## Storage Model          what lives in which store
## Auth and Access Model  sign-in, ownership, who can read and change what
## Invariants             numbered rules
```

An invariant is a rule whose violation can be pointed at in a code
review. "Try to keep handlers small" is a preference; rewrite it or
drop it.

## Round 3: Design

Skip for projects with no interface, and leave the file out of the
entry point.

**Settle**

- Dark, light, or both. The feel, with a reference app.
- Brand colors if any; otherwise propose a full palette, or two
  contrasting directions to pick from.
- Component library, icon set, fonts.
- Main layout (sidebar, top navigation, single column, bottom tabs),
  and phone or desktop first.

**Push back when** the answer is only "clean and modern": ask for a
reference app or offer the two directions.

**Write `context/ui-context.md`**

```
# UI Context
## Theme              two or three sentences
## Colors             table: Role | Variable | Hex
## Typography         table: Role | Font | Variable
## Border Radius      table: Context | Value
## Component Library  which one, where components live, how they are added
## Layout Patterns    this app's real structure, plus empty, loading and error states
## Icons              set and sizes
```

Every color the app will need is a named token: backgrounds, surfaces,
text levels, accent and its hover, border, disabled, error, success,
warning. A missing token is a color the agent will invent.

## Round 4: Rules

**Settle**

- Conventions the user cares about: naming, typing strictness, error
  handling, testing expectations. Propose the ones this stack needs.
- The commands that verify a change: type check, lint, build, test.
- Files and folders the agent must not modify (generated components,
  migrations, vendored code).
- Git: branch per unit or not, commit style, who pushes.
- Which coding agent the user builds with (decides the entry-point
  file).

**Write `context/code-standards.md`**

```
# Code Standards
## General
## [Language]            typing, error handling, naming
## [Framework]           conventions
## Styling               tokens only, radius scale
## API                   validation, auth check, one response shape
## Data and Storage
## File Organization     matches System Boundaries in architecture.md exactly
```

Keep only rules the agent could get wrong in this stack, each one
checkable ("no `any`", "every route validates input with a schema
before any logic runs"). Show the response shape and the error pattern
in a few lines. Generic advice ("write clean code") is noise.

**Write the entry point** at the project root:

| Agent | File |
| ----- | ---- |
| Claude Code | `CLAUDE.md` |
| Codex, Copilot, most others | `AGENTS.md` |
| Cursor, Windsurf, others with their own convention | the file that agent reads |
| Several, or unsure | `AGENTS.md`, plus a `CLAUDE.md` holding the single line `@AGENTS.md` |

If the file exists, append the section and leave the rest untouched.
List only context files that exist.

```
## Project context

Read `context/progress-tracker.md` first, every session. Open the others
only when the task touches what they cover:

- `context/project-overview.md`: what is being built, the core flow, in and out of scope
- `context/architecture.md`: stack, folder boundaries, storage, auth, invariants
- `context/ui-context.md`: color tokens, typography, layout patterns
- `context/code-standards.md`: conventions and naming
- `context/specs/00-build-plan.md`: the ordered units; each has a spec in `context/specs/`, written before it is built

## Rules

- Build one unit at a time, from its spec. Whatever the spec and the context files do not settle is a question for the user, not a guess.
- When work changes something a context file documents, update that file in the same change.
- Verify with: `[commands]`
- Do not modify: [protected paths]
- Git: [convention]
```

## Build plan (`context/specs/00-build-plan.md`)

```
# Build Plan

Units are built in this order. Status lives in the progress tracker.

| #  | Unit | Boundary | Depends on | Builds | Not in this unit | Done when |
| -- | ---- | -------- | ---------- | ------ | ---------------- | --------- |
| 01 |      |          | none       |        |                  |           |
```

"Builds" is the one checkable result. "Not in this unit" names what is
deliberately left for later.

## Progress tracker (`context/progress-tracker.md`)

```
# Progress Tracker
## Status          phase, and the unit in flight with its state
## Next Up
## Completed       one line per unit
## Open Questions  each with its working assumption
## Decisions       choice, reason, what it beat
## Notes           what a fresh session must know; replaced, not appended
## Ideas           deferred features, each with the reason
```

Every session reads this file, so it stays under about sixty lines.
Keep the reason with each decision: "Postgres over MongoDB because the
data is relational and the user knows SQL" is useful later; "chose
Postgres" is not.
