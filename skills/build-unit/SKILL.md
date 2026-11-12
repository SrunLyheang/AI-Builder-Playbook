---
name: build-unit
description: Implement one unit of a planned build exactly as its spec file says, verify it against the spec's checklist, and update the progress tracker. Use this whenever the user says "build unit 3", "implement the next unit", "implement context/specs/04-...", "build the spec", "fix unit 3, it doesn't match the spec", "unit 3 is done, close it out", or asks for changes or extra features while a unit is being built, in a project that has a context/ folder with spec files. Requires a spec written by spec-unit; if none exists, run that first.
---

# Build Unit

Implement one unit from its spec: no more and no less.

The spec and the context files already contain the decisions. This
skill's job is to carry them out faithfully. Extra features, tidy-ups,
and "while I'm here" changes are how a build drifts away from its plan,
so they are out even when they look helpful.

This is the third of three skills: `plan-app` plans, `spec-unit`
specifies one unit, and `build-unit` (this one) implements it.

It runs in one of three modes depending on what the user asks for:
implement, correct, or close.

## Before anything: load context

Read the entry-point file (`CLAUDE.md` or `AGENTS.md`), every context
file it lists, and the spec for the unit in `context/specs/`.

Stop and tell the user if:

- there is no spec file for the unit (run `spec-unit` first)
- a unit this one depends on is not marked complete in
  `context/progress-tracker.md`
- the spec conflicts with an invariant in `architecture.md`

If the user did not name a unit, use the one listed under Next Up in the
progress tracker and confirm it.

## Vague requests

A request is vague when it could be carried out in more than one way:
"make it look nicer", "it feels off", "fix the form", "add the usual
validation", "clean this up". Acting on one means guessing, and a wrong
guess costs a rebuild.

Every time the user's request is vague, in any mode, do all three
before changing code:

1. **Say what is unclear**, in one line.
2. **Ask one sharper question** at a time (which element, what it does
   now, what it should do).
3. **Give your recommended answer with the reason**, based on the spec
   and context files, so the user can accept, adjust, or reject it.

Keep asking until the request is concrete. When there is a real choice,
show two or three options with trade-offs, recommend one, and say why
the others lose for this project.

## Requests outside the unit

While a unit is being built, the user will often ask for something the
spec does not contain: a new feature, extra behavior, or a change to an
earlier unit. Do not build it, even if it is small. Building it would
make the spec wrong and move the build away from the plan.

Instead:

1. **Say that it is outside this unit**, and name the spec section or
   Out of Scope line that shows it.
2. **Ask detailed questions, one at a time:** what problem it solves
   and for whom, where it sits in the core flow in
   `project-overview.md`, what exactly the user does and sees, and what
   happens if the app ships without it.
3. **Push back when warranted, with the reason:** it is out of scope in
   `project-overview.md`, it serves no goal, it duplicates something
   built or planned, it is larger than it sounds, it delays a more
   important unit, or it breaks an invariant.
4. **Recommend the better path:** build now, later, or drop, plus an
   alternative if one is better (a smaller version, a different feature
   that meets the same need for less, or the same feature after the
   core flow works). Say why it beats the original for this project.
5. **Route the decision. The user decides.**
   - It truly belongs to this unit's visible result and boundary:
     update the spec file first (implementation, out of scope,
     checklist), then build it.
   - It is a separate piece of work: finish the current unit as
     specified, then run `spec-unit` to add it to the build plan. Log
     it in the progress tracker now so it is not lost.
   - Later or dropped: record it in the progress tracker, or in Out of
     Scope in `project-overview.md`, with the reason.

## Mode 1: Implement

1. **Mark it in progress.** Move the unit to In Progress in
   `context/progress-tracker.md`.
2. **Branch, if the project uses git.** Create
   `feat/NN-unit-name` unless the context files define a different
   convention. If the working tree has uncommitted changes that are not
   part of this unit, tell the user before starting.
3. **Install only what the spec lists** under Dependencies.
4. **Implement the spec section by section**, following
   `code-standards.md` and `ui-context.md`. Put each file in the folder
   that owns that responsibility according to `architecture.md`.
5. **Stay inside the unit.** Do not touch the protected files named in
   `ai-workflow-rules.md`. Do not refactor code from earlier units,
   add features from later units, or add anything listed under the
   spec's Out of Scope. If something outside the unit looks wrong, note
   it for the user; leave it alone.
6. **Verify** (see below).
7. **Report** (see below).

### When the spec has a gap

If the spec does not say what to do, or cannot be implemented as
written, stop. Do not invent behavior. Tell the user what is missing,
offer two or three options with a recommendation and the reasons, and
once they decide, write the answer into the spec file before continuing.
The spec has to stay an accurate record of what was built.

A choice with no visible effect on behavior or structure (a local
variable name, the order of two helper functions) is not a gap; follow
`code-standards.md` and carry on.

### Verify

Go through the spec's "Verify when done" list one item at a time.

- Run every command the list names (type check, lint, build, tests) and
  read the output. A check counts as passed only after it was run and
  passed.
- For behavior you can exercise, exercise it.
- For anything you cannot check yourself (how a page looks, a flow that
  needs a real account or device), say so plainly and list it for the
  user to check by hand.

Also confirm that no invariant in `architecture.md` was violated.

If a check fails, fix the cause and run the checks again. If it cannot
be fixed within the unit's scope, stop and explain.

### Report

Give the user:

- what was built, as a short list of files created or changed
- the checklist, each item marked passed, failed, or needs your check
- how to try it (the command to run and where to look)
- anything noticed outside the unit's scope
- any spec gap that was resolved and how

Leave the unit In Progress. It is complete when the user says so.

## Mode 2: Correct

Used when the user reports that something does not match the spec.

1. Restate the mismatch: what the spec says, what was built. If the
   report is vague ("it looks wrong"), apply the vague-request rule
   first. If what the user wants was never in the spec, treat it as a
   request outside the unit.
2. If the spec is right, fix only that. Change nothing else.
3. If the user now wants something different from the spec, update the
   spec first, then change the code to match.
4. Re-run the checks that the fix could affect and report.

## Mode 3: Close

Used when the user confirms the unit works.

1. Confirm every checklist item is passed, including the ones the user
   checked by hand. If any is still open, ask about it.
2. Update `context/progress-tracker.md`: move the unit to Completed, set
   Next Up to the next unit in the build plan, record any architecture
   decisions made during the unit with their reasons, and write session
   notes a fresh session would need.
3. If the implementation changed anything documented in a context file
   (a boundary, a storage decision, a convention, the scope), update
   that file now.
4. Commit the work with a message naming the unit. Push the branch or
   open a pull request only when the user asks for it.
5. Tell the user the next step: `spec-unit` for the next unit.

Do not start the next unit in the same turn.
