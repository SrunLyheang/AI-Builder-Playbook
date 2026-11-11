---
name: plan-app
description: Plan an application before any code is written. Interviews the user one question at a time about their idea, pushes back on vague answers, then writes the project's context files (project-overview, architecture, ui-context, code-standards, ai-workflow-rules, progress-tracker), an entry-point file (CLAUDE.md or AGENTS.md), and an ordered build plan. Use this whenever the user has an app idea, says "I want to build...", "help me plan my app", "start a new project", "set up my context files", "write my CLAUDE.md / AGENTS.md", or asks to begin coding a new app that has no context/ folder yet. Trigger even if the user asks to jump straight to code on a brand-new project; planning comes first.
---

# Plan App

Turn a rough app idea into a set of context files and a build plan that a
coding agent can execute without guessing.

The reason this skill exists: an agent fills every gap in a plan with an
assumption, and those assumptions compound until the result looks nothing
like what the user had in mind. The fix is to close the gaps before the
first line of code, through a conversation, and then write the answers
down where every future session will read them.

This skill is the first of three:

1. `plan-app` (this one): interview, context files, build plan
2. `spec-unit`: writes the spec file for one unit of the build plan
3. `build-unit`: implements one unit from its spec

## Ground rules

- **Write no application code.** Do not scaffold, install packages, or
  create source files. The only files this skill creates are Markdown
  files inside `context/` and the entry-point file at the project root.
  If the user asks to start coding mid-interview, tell them what is still
  undecided and what the agent would have to guess; let them choose.
- **One question at a time.** A wall of questions gets shallow answers.
  Ask one, listen, follow up, then move on.
- **The user decides, you challenge.** Never silently pick a stack, a
  feature, or a scope cut. Offer options, recommend, and let them choose.
- **Record only what was decided.** Anything the user did not decide goes
  into Open Questions, never into a context file as if it were settled.

## Step 0: Orient

Before asking anything, look at the working directory.

- **`context/` already exists:** read it. If `context/planning-notes.md`
  is there, an earlier session was interrupted; summarize what is already
  decided and resume from the first unsettled topic. If the context files
  are complete, ask whether the user wants to revise them or is looking
  for `spec-unit` instead.
- **Existing code but no `context/`:** read the manifest, folder layout,
  and README first. Do not ask about things the code already answers
  (stack, folder structure, auth library). State what you found and
  interview only on the gaps.
- **Empty project:** start fresh.

Then ask the user to describe the idea in two or three sentences, and ask
how much experience they have building apps. Use the second answer to
calibrate: explain a term in one line when the user is new to it, and
skip the explanation when they clearly know it.

Tell the user up front that this is a long conversation (often 30 to 60
questions), that nothing gets built during it, and that progress is saved
so they can stop and resume.

## Step 1: The interview

Read `references/interview-guide.md` now. It holds the topic order, the
questions each topic must answer, the pushback patterns, and the readiness
checklist.

Work through the topics in order: product, users, core flow, features and
scope, data, auth, hard parts and risks, stack, design, process. Later
topics depend on earlier ones, so do not ask about the database before
the core flow is clear.

### Vague answers are never accepted

A vague answer written into a context file becomes a guess the agent
makes later, so it has to be resolved here. An answer is vague when two
developers could build different things from it: "users can manage
their stuff", "a nice dashboard", "it should be fast", "some kind of
login", "the usual features".

Every time an answer is vague, do all three:

1. **Say what is unclear**, in one line.
2. **Ask one sharper question** that forces a concrete answer (what the
   user clicks, what appears on screen, what is stored).
3. **Give your recommended answer with the reason**, so the user can
   accept it, change it, or reject it instead of starting from nothing.

```
"Manage their projects" could mean a few things. When a user opens
the projects page, what can they do there?

My recommendation: create, rename, and delete only, with no folders
or archiving, because your core flow needs a project to exist and
nothing more. Folders and archive can come after v1.

Does that match, or is something missing?
```

Keep going on the same point until the answer is concrete. Never note a
vague answer down and move on. This applies to every topic, and equally
to a vague opening idea ("an app for students"): get it concrete before
going any further.

### Pushing back

Agreeing with everything is the failure mode. Beyond vagueness,
challenge an answer when it contradicts an earlier answer, when the
first version is too big, or when a choice has a consequence the user
has not mentioned. Say what the problem is, ask a sharper question, and
give your recommendation with the reason. Keep it friendly; the aim is a
clearer plan, and the user should come away feeling helped.

### Features added along the way

Users think of new features throughout the interview, and again while
reviewing the files and the build plan. Do not just add them. For each
one, ask what problem it solves, who needs it, and where it sits in the
core flow; then give a verdict with reasons: in v1, later, or never. If
a smaller or different feature would serve the same need better, say so
and recommend it. Once the files are written, any change to scope means
updating `project-overview.md` and the build plan together.

### When the user does not know

When the user cannot answer (common for stack, database, auth, hosting),
do not leave it open and do not just pick for them. Present it like this:

```
You need [the decision] because [why it matters for this app].

Option A: [name]
  Good: [...]   Cost: [...]
Option B: [name]
  Good: [...]   Cost: [...]
Option C: [name]
  Good: [...]   Cost: [...]

I recommend A, because [reasons tied to what they told you:
their experience, the app's needs, the scope].
B loses because [specific reason for this project].
C loses because [specific reason for this project].

Go with A, or do you prefer another?
```

Two or three options. The reasons must refer to this project, not to
general reputation. If the user still cannot decide, record the
recommendation as the working choice and list it under Open Questions.

If the agent has a structured question tool (for example
`AskUserQuestion` in Claude Code), use it for these multiple-choice
moments. Use plain text for open questions.

### Saving progress

After each topic is settled, append the decisions to
`context/planning-notes.md` (create `context/` if needed). Plain bullets
under a heading per topic, plus a running "Open questions" list. This
protects a long interview against a lost session or a compacted context,
and it is the source the context files are written from.

### The readiness gate

When every topic is covered, play back the whole plan in a compact
summary: one-sentence pitch, primary user, core flow, v1 features, out of
scope, stack, data model, auth model, invariants, visual direction. Ask
the user to correct anything that is wrong.

Then check the readiness checklist at the end of the interview guide. If
any item is unanswered, go back to it. Move on only when the user
confirms the summary.

## Step 2: Write the context files

Read `references/file-guides.md` now. It describes what each file must
contain and the mistakes to avoid.

Templates are in `assets/templates/`. They define the section structure.
The bracketed examples in them come from a Next.js and TypeScript
project; replace them with this project's reality, and rename or drop
sections that do not apply (a Python API has no "TypeScript" section, a
CLI tool has no `ui-context.md`).

Write the files in this order, into `context/`:

1. `project-overview.md`
2. `architecture.md`
3. `ui-context.md`
4. `code-standards.md`
5. `ai-workflow-rules.md`
6. `progress-tracker.md`

After writing each file, give the user a three to five line summary of
what it locks in and ask for corrections before writing the next one.
`project-overview.md` and `architecture.md` deserve the most careful
review, because every later file and every spec builds on them.

Adjust the set to the project. A weekend project may fold code standards
into the workflow rules; a large one may add files such as
`data-model.md` or `api-contracts.md`. Whatever the count, the agent must
end up knowing what is being built, how it fits together, what the rules
are, and where things stand.

No placeholder may survive. If a section cannot be filled because the
decision was never made, either ask now or write it into Open Questions
in the progress tracker and say "Undecided, see progress tracker" in the
section.

### The entry-point file

Ask which coding agent the user builds with, then write the entry point
at the project root from `assets/templates/entry-point.md`:

| Agent | File |
| ----- | ---- |
| Claude Code | `CLAUDE.md` |
| Codex, GitHub Copilot, and most others | `AGENTS.md` |
| Cursor, Windsurf, others with their own convention | The file that agent reads; check its docs |
| Several agents, or unsure | `AGENTS.md` with the content, plus a `CLAUDE.md` containing the single line `@AGENTS.md` |

If the file already exists, add the section to it; never overwrite what
is there. If files were dropped or added in the step above, make the list
in the entry point match the files that exist. Add one line pointing to
`context/specs/00-build-plan.md`.

## Step 3: The build plan

Break the whole build into units and save the plan as
`context/specs/00-build-plan.md` using
`assets/templates/build-plan.md`.

A unit is one scoped, verifiable piece of work that fits in a single
focused session. "Build the dashboard" is a phase. "Build the project
sidebar with My Projects and Shared tabs, empty states, and open and
close behavior, no API calls yet" is a unit.

Each unit must:

- produce one result a person can see or check
- stay inside one system boundary (do not mix UI, database, and
  background work)
- have a clear end state
- need no decision that belongs to another unit

Order the units by these rules:

- **Dependencies first.** Nothing is built on something that does not
  exist yet.
- **Security before functionality.** Auth and access control come before
  the features they protect.
- **Backend before frontend wiring.** Build the route, then connect the
  UI to it, in separate units.
- **UI shells before real data.** Build the screen with placeholder data,
  then connect it.
- **Dependencies just in time.** A package is installed in the unit that
  first needs it.

Then validate. For every unit, confirm everything it depends on exists in
an earlier unit; reorder if not. Merge adjacent units that have no
standalone result between them. Split any unit that crosses a boundary or
would not fit in one session.

Show the user the ordered list and adjust it with them. Then fill in the
progress tracker: phase "Planning complete", Next Up set to unit 01,
remaining open questions listed, and the key decisions with their reasons
under Architecture Decisions.

## Step 4: Hand off

Finish with a short summary: the files created, any open questions still
unresolved, and the next step, which is to run `spec-unit` for unit 01
and then `build-unit` to implement it. Keep
`context/planning-notes.md` as the raw decision log; it is not listed in
the entry point, so agents will not load it every session.

Do not start writing a spec or code in the same turn. Planning ends here
so the user can review the files with a clear head.
