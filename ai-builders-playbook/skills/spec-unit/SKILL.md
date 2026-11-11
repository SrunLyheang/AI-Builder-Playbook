---
name: spec-unit
description: Write the spec file for one unit of a planned build. Reads the project's context files and build plan, asks the user about anything unit-specific that is undecided, and writes context/specs/NN-unit-name.md with goal, design, implementation, dependencies, and a verification checklist. Use this whenever the user says "write the spec for unit 3", "spec the next unit", "what's next to build", "plan the next feature", or wants to add a new feature to a project that already has a context/ folder. Run this before build-unit; it writes no code.
---

# Spec Unit

Write the complete instruction set for one unit of the build, so the
implementing agent has nothing to guess.

A spec replaces the vague prompt. A vague spec gives vague code; a
detailed one gives predictable code. Specs are written just before a
unit is built, never all upfront, because each unit teaches something
that changes the next.

This is the second of three skills: `plan-app` produces the context
files and build plan, `spec-unit` (this one) specifies one unit, and
`build-unit` implements it.

## Ground rules

- **Write no application code and install nothing.** The only output is
  one Markdown file in `context/specs/` and small updates to the
  progress tracker.
- **One unit per run.**
- **Decide nothing on the user's behalf.** A product or design decision
  that the context files do not settle is a question for the user.
- **Never spec from a vague request.** See "Vague requests" below.

## Vague requests

A request is vague when two developers could build different things
from it: "add a dashboard", "make onboarding better", "add some AI",
"let users share stuff". A spec written from that is a guess with
headings.

Every time the user's request or answer is vague, at any step, do all
three before continuing:

1. **Say what is unclear**, in one line.
2. **Ask one sharper question** at a time (what the user clicks, what
   appears, what is stored, what happens on failure).
3. **Give your recommended answer with the reason**, tied to this
   project's context files, so the user can accept, adjust, or reject
   it.

Keep asking until the answer is concrete. When there is a real choice
to make, show two or three options with trade-offs, recommend one, and
say why the others lose for this project.

## Step 1: Load context

Read the entry-point file (`CLAUDE.md` or `AGENTS.md`) and every context
file it lists, then `context/specs/00-build-plan.md` and
`context/progress-tracker.md`.

If there is no `context/` folder or no build plan, stop and tell the
user to run `plan-app` first. A spec written without the context files
is just a guess with headings.

## Step 2: Pick the unit

- If the user named a unit, use it.
- Otherwise take the first unit in the build plan that the progress
  tracker does not show as complete, and confirm it with the user.
- Check that every unit it depends on is marked complete. If one is not,
  say so and ask whether to spec that one instead.
- If a spec file for this unit already exists, ask whether to revise it
  or leave it.

If what the user asks for is not in the build plan, or goes beyond the
unit being specified, follow "Features outside the plan" below before
doing anything else.

## Features outside the plan

This covers a new feature that is not in the build plan, and extra
behavior the user wants to add to the current unit that the build plan
did not give it. Do not quietly add either. Unplanned features are how
a build ends up unlike the plan, so each one has to earn its place.

**1. Ask detailed questions, one at a time.**

- What problem does this solve, and for which user?
- Where does it sit in the core user flow in `project-overview.md`?
- What exactly does the user do and see? What data does it need?
- What happens if the app ships without it?
- Why now instead of after the remaining planned units?

Apply the vague-request rule to every answer.

**2. Check it against the context files and push back.**

Push back, with the reason, when the feature:

- is listed as out of scope in `project-overview.md` (quote the line)
- does not serve any goal or step of the core flow
- duplicates something already built or planned
- is much larger than the user thinks (name the units it would take)
- delays a planned unit that matters more
- would break an invariant or need a stack change

Pushing back means saying so clearly and asking the user to respond. It
does not mean refusing; the user decides.

**3. Recommend something better when there is something better.**

Give an honest verdict: build now, build later, or drop. Where it fits,
recommend an alternative with reasons: a smaller version that delivers
most of the value, a different feature that serves the same goal at
lower cost, or the same feature after the core flow works. Say why the
alternative beats the original for this project.

```
You want comments on each project. Two concerns: comments are listed
as out of scope in project-overview.md, and they need notifications
and moderation to be useful, which is about three more units.

What is the need behind it? If it is "leave a note for a teammate",
I recommend a single notes field on the project instead: one small
unit, no new tables beyond one column, and it covers that need.
Full comments could follow once sharing exists.

Notes field now, full comments later, or full comments now?
```

**4. Record the decision.**

- **Build now:** update the scope in `project-overview.md` if it
  changed, add the feature to the build plan as one or more new units
  in the right position with dependencies, then spec the right unit.
  Extra behavior stays out of the current unit unless it truly belongs
  to the same visible result and the same boundary.
- **Later:** add it to the progress tracker as a future idea, with the
  reason it was deferred.
- **Dropped:** add it to Out of Scope in `project-overview.md` so it is
  not proposed again.

## Step 3: Look at what exists

Read the code this unit will touch or build on: the files from earlier
units, existing components, routes, schema. The spec must name real
paths and real function or component names, and reuse what is already
there. Read the spec files of the units it depends on to see what they
actually delivered.

## Step 4: Close the gaps

List what this unit needs that the context files and build plan do not
answer. Typical gaps: exact fields on a form, validation rules, empty,
loading, and error states, what happens on failure, the precise shape of
an API response, mobile behavior, wording.

Ask the user about these one at a time. When they are unsure, give two
or three options with trade-offs, recommend one with reasons tied to
this project, and say why the others lose. Do not ask about things the
context files already settle; quote the file instead.

If the unit turns out to cross a system boundary or to be too big for
one session, propose splitting it and update the build plan once the
user agrees. One feature may need one spec or three.

## Step 5: Write the spec

Save to `context/specs/NN-kebab-case-name.md`, where NN is the unit's
two-digit number in the build plan. Use `assets/spec-template.md`.

- **Goal:** one or two sentences naming the concrete output. Weak:
  "Create the auth pages." Strong: "Create sign-in and sign-up pages
  using the auth provider's components, two-panel on desktop and
  form-only on mobile, with unauthenticated users redirected to
  /sign-in."
- **Design:** every visual decision for this unit, using token names
  from `ui-context.md`. Layout, components, states, responsive behavior.
  The agent should have no visual choice left to make.
- **Implementation:** one sub-section per component, route, or module.
  Give the file path, what it receives, what it returns or renders, and
  how it handles errors. Describe behavior; leave the code to
  `build-unit`.
- **Out of Scope:** what belongs to later units, especially the things
  an agent would be tempted to add now.
- **Dependencies:** each new package with its reason, or "None". Only
  packages first needed in this unit.
- **Verify when done:** conditions specific enough to check, covering
  each behavior in the spec, plus the project's own type check, lint,
  and build commands from `ai-workflow-rules.md`.

## Step 6: Check it

Before showing the user, check the spec against the context files:

- Does anything break an invariant in `architecture.md`?
- Does every file sit in the folder that owns that responsibility?
- Does it follow `code-standards.md`?
- Does it use only tokens and components defined in `ui-context.md`?
- Does it stay inside the scope in `project-overview.md`?
- Could a fresh agent implement it without asking a question?

Fix what fails. If the unit cannot be built without changing a context
file, tell the user, and update that file once they agree, before
finishing the spec.

## Step 7: Hand off

Show the user a short summary of the spec and the path to the file. In
`context/progress-tracker.md`, set Next Up to this unit with the note
"spec written", and record any decisions made during Step 4 that affect
later units.

Then stop. The next step is `build-unit`, which the user starts after
reading the spec.
