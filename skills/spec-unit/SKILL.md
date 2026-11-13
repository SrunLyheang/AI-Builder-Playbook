---
name: spec-unit
description: Write the spec file for one unit of a planned build, so it can be implemented without guessing. Use when the user says "spec the next unit", "write the spec for unit 3", "what's next to build", or wants to add a feature to a project that has a context/ folder. Run before build-unit; writes no code.
argument-hint: "[unit number or feature]"
model: opus
---

# Spec Unit

Write the complete instruction set for one unit, so the implementing
agent has nothing to guess and no other file to read. Specs are written
just before a unit is built, never all upfront, because each unit
changes what the next one needs.

The thinking happens here, on the strongest model available;
`build-unit` then carries the spec out on a cheaper one. That only works
if the spec is self-contained.

## Rules

- Write no application code and install nothing. Output is one file in
  `context/specs/` and a small tracker update.
- One unit per run.
- A product or design decision the context files do not settle is a
  question for the user. Never spec from a vague request: when two
  developers could build different things from it ("add a dashboard",
  "let users share stuff"), say what is unclear, ask a sharper question,
  and give your recommended answer with the reason.

## Step 1: Load only what this unit needs

The entry point is already in context; do not read it again.

- Always: `context/progress-tracker.md`,
  `context/specs/00-build-plan.md`, `context/architecture.md`,
  `context/code-standards.md`.
- Only if the unit has an interface: `context/ui-context.md`.
- Only to judge a request that is not in the plan:
  `context/project-overview.md`.

If there is no `context/` folder or no build plan, stop and point the
user to `plan-app`.

## Step 2: Pick the unit

Use the unit the user named; otherwise the first one in the build plan
that the tracker does not show as complete, stated in one line without
waiting for confirmation. If a unit it depends on is not complete, say
so and offer to spec that one instead. If its spec file already exists,
ask whether to revise it.

## Step 3: Look at what exists

Find the code this unit touches or builds on, by search first, reading
only the files it will change or call. The spec must name real paths and
real function or component names and reuse what is there. Open an
earlier unit's spec only when the code does not show what it delivered.

## Step 4: Close the gaps in one message

List everything this unit needs that the files do not answer: form
fields, validation rules, empty, loading and error states, behavior on
failure, response shapes, mobile behavior, wording. Ask them together,
numbered, each with your recommended answer so the user can reply "ok"
to all or change a few. Give options with trade-offs only where there is
a real choice. Do not ask what a context file already settles.

If the unit crosses a system boundary or is too big for one session,
propose a split and update the build plan once the user agrees.

## Step 5: Write the spec

Save to `context/specs/NN-kebab-case-name.md` using
`assets/spec-template.md`, where NN is the unit's number in the build
plan.

- **Goal:** the concrete output. Weak: "Create the auth pages." Strong:
  "Sign-in and sign-up pages using the auth provider's components,
  two-panel on desktop, form only on mobile, unauthenticated users
  redirected to /sign-in."
- **Rules for this unit:** copy, word for word, the invariants,
  standards, boundaries, and protected paths that apply to this unit,
  and for a UI unit the tokens it uses, with their values. This section
  is what lets `build-unit` skip the context files, so leave out what
  does not apply and include everything that does.
- **Design:** every visual decision, by token name: layout, components,
  states, responsive behavior.
- **Implementation:** one sub-section per component, route, or module:
  file path, what it receives, what it returns or renders, how it
  handles errors. Describe behavior; the code is `build-unit`'s job.
- **Out of Scope:** what belongs to later units, especially what an
  agent would be tempted to add now.
- **Dependencies:** each new package with its reason, or "None".
- **Verify when done:** one checkable condition per behavior, then the
  project's exact verify commands.

Write it so that it already holds: every file sits in the folder that
owns the responsibility, nothing breaks an invariant, and a fresh agent
could build it without asking a question. If the unit cannot be built
without changing a context file, tell the user and update that file once
they agree.

## Step 6: Hand off

In `context/progress-tracker.md`, set Next Up to this unit with "spec
written" and record any decision from Step 4 that affects later units.
Give the user the file path and a summary of at most five lines, then
stop. `build-unit` is next, after the user has read the spec.

## A request that is not in the plan

This covers a new feature and extra behavior for the current unit.
Neither goes in quietly; unplanned features are how a build ends up
unlike its plan.

1. Ask, in one message, what need it serves and for whom, where it sits
   in the core flow, and what happens if the app ships without it.
2. Check it against `project-overview.md` and `architecture.md`. Say so
   plainly when it is listed as out of scope (quote the line), serves no
   goal, duplicates something built or planned, is bigger than it sounds
   (name the units it would take), or needs a stack or invariant change.
3. Give a verdict with the reason (now, later, or drop) and a smaller
   alternative when one covers the same need. The user decides.
4. Record it. Now: update the scope in `project-overview.md` if it
   changed, add the unit or units to the build plan in the right
   position, then spec the right one. Later: add it under Ideas in the
   tracker with the reason. Dropped: add it to Out of Scope.
