---
name: build-unit
description: Implement one unit of a planned build exactly as its spec file says, verify it, and update the progress tracker. Use when the user says "build unit 3", "build the next unit", "fix unit 3, it doesn't match the spec", or "unit 3 works, close it out", in a project with spec files in context/specs/. Needs a spec from spec-unit.
argument-hint: "[unit number] [fix | close]"
---

# Build Unit

Implement one unit from its spec: no more and no less.

The decisions were made when the spec was written. This skill carries
them out, so a cheaper, faster model is enough for it. It makes no
product or design decisions; when one is needed, it stops and asks.
Only the user changes the model: never switch it yourself.

## Load

Read the unit's spec in `context/specs/` and
`context/progress-tracker.md`. That is all. The spec's "Rules for this
unit" section holds what applies from the context files; open a context
file only when the spec points to something it does not contain.

With no unit named, use Next Up from the tracker and say which one in
one line. If there is no spec for it, stop and point the user to
`spec-unit`.

Pick the mode from the request: implement, fix, or close.

## Implement

1. Create the branch `feat/NN-unit-name` if the project uses git and
   the entry point names no other convention. If the working tree holds
   unrelated uncommitted changes, tell the user first.
2. Install only the packages listed under Dependencies.
3. Build the spec section by section, following its Rules.
4. Stay inside the unit: no refactoring of earlier units, nothing from
   later units, nothing listed under Out of Scope, no edits to protected
   paths. If something outside the unit looks wrong, mention it in the
   report and leave it alone.
5. Verify. Run each command in "Verify when done" once and read the
   output; exercise the behavior you can exercise. If a check fails, fix
   the cause and re-run only that check. What you cannot check yourself
   (how a page looks, a flow that needs a real account) goes to the user
   as a list.
6. Set the tracker's Status to this unit, "built, awaiting your check",
   then report in this shape and stop:
   - files created or changed
   - each checklist item: passed, failed, or needs your check
   - how to try it
   - anything noticed outside the unit, and any gap resolved

The unit is complete only when the user says it works.

## When the spec is not enough

- **Gap:** the spec does not say what to do, or cannot be built as
  written. Stop, say what is missing, offer two or three options with a
  recommendation, and write the user's answer into the spec before
  continuing. A choice with no visible effect (a local variable name) is
  not a gap; decide and move on.
- **Vague request** ("make it nicer", "fix the form"): say what is
  unclear and ask one sharper question with your recommended answer.
  Change no code on a guess.
- **Something the spec does not contain** (a new feature, extra
  behavior, a change to an earlier unit): do not build it, however
  small. Add it under Ideas in the tracker and tell the user to run
  `spec-unit` for it after this unit. The one exception is a detail that
  plainly belongs to this unit's own result: with the user's yes, add it
  to the spec first, then build it.

## Fix

For a report that something does not match the spec.

1. State the mismatch in one line: what the spec says, what was built.
2. If the spec is right, fix that and nothing else. If the user now
   wants something different, update the spec first, then the code.
3. Re-run only the checks the fix could affect, and report.

## Close

For when the user confirms the unit works.

1. If a "needs your check" item was never confirmed, ask about it.
2. Update the tracker in one edit: add the unit to Completed as one
   line, set Next Up to the following unit in the build plan, add any
   decision made during the unit with its reason, and replace Notes with
   what a fresh session must know.
3. If the build changed something a context file documents (a boundary,
   a storage decision, a convention, the scope), update that file.
4. Commit with a message naming the unit. Push or open a pull request
   only when asked.
5. Name the next step in one line and stop: `spec-unit` for the next
   unit, after switching back to their strongest model.
