# File Guides

What each context file is for, what it must contain, and how it usually
goes wrong. Write every file from `context/planning-notes.md` and the
conversation. Do not add decisions the user never made.

General rules for all files:

- Be specific and concrete. "Fast" and "clean" tell an agent nothing.
- Write for a reader with no memory of the conversation.
- Keep each file short enough to be read at the start of every session.
  A file that runs past two screens usually contains explanation that
  belongs in the planning notes.
- Use the same names everywhere. If the overview says "workspace", the
  architecture file does not say "project".

---

## project-overview.md

**Job:** defines what is being built and why. The agent uses it to
resolve ambiguity in a spec.

**Contains:** one-paragraph overview; numbered goals; the core user flow
step by step; features by category; in scope; out of scope; success
criteria.

**Good looks like**

- Goals are measurable: "A user can import a 1,000-row file in under ten
  seconds", not "Make importing easy".
- The user flow has no gaps; every step names what the user does and
  what they see.
- Out of scope is long and explicit. Include the things a reasonable
  agent would add unprompted: dark mode toggle, admin panel, email
  notifications, tests for everything, analytics, internationalization.
- Success criteria can be checked: "A signed-in user can create and open
  a project."

**Common failure:** an out-of-scope section with two vague lines. This
section is what prevents scope creep, so treat it as the most important
part of the file.

## architecture.md

**Job:** defines the stack, the system boundaries, the storage model,
the auth model, and the invariants. This is the most important file;
without it the agent makes reasonable-looking decisions that slowly
corrupt the codebase.

**Contains:** stack table (layer, technology, role); system boundaries
with exact folder names; storage model; auth and access model; AI or
background task model if relevant; invariants.

**Good looks like**

- Every technology has a stated role, and pinned major versions where
  the version changes how code is written.
- Boundaries say what a folder owns and what it must not do:
  "`lib/db/` owns all database queries. No other folder imports the
  database client."
- The storage model leaves no doubt about what lives where.
- At least four invariants, written as rules: "Every mutation verifies
  the caller owns the resource before changing it."

**Common failure:** invariants written as preferences ("try to keep
handlers small"). If a violation could not be pointed at in a code
review, rewrite it.

## ui-context.md

**Job:** defines the visual language so the agent never makes a visual
decision by itself.

**Contains:** theme description; color tokens with variable names and
hex values; typography; border radius scale; component library and how
components are added; layout patterns; icons.

**Good looks like**

- Every color the app needs is a named token: backgrounds, surfaces,
  text levels, accent and its hover state, border, error, success,
  warning.
- Layout patterns describe this app's real structure: how the sidebar
  behaves, how modals open, what the navbar holds, what changes on
  mobile.
- States are covered: empty, loading, error.

**Common failure:** a palette with five colors, leaving the agent to
invent a hover color or a disabled state.

Omit this file for projects without an interface, and remove it from the
entry point.

## code-standards.md

**Job:** keeps the code consistent from the first unit to the last, so
unit twenty looks like unit five.

**Contains:** general principles; language rules (typing strictness,
error handling, naming); framework conventions; styling rules; API or
route structure (validation, auth check, response shape); data and
storage rules; file organization with what belongs in each folder.

**Good looks like**

- Rules are checkable: "No `any`; use explicit types", "Every route
  validates input with a schema before any logic runs".
- One standard response shape and one error-handling pattern, shown in a
  few lines.
- Naming conventions for files, components, functions, and database
  fields.
- File organization matches the boundaries in `architecture.md` exactly.

**Common failure:** generic advice ("write clean code"). Each rule
should be one the agent could otherwise get wrong in this stack. Rename
the template's sections to fit the stack; the template's TypeScript and
Next.js headings are examples.

## ai-workflow-rules.md

**Job:** makes the agent disciplined: how to scope work, when to split
it, what to do when a requirement is missing, what to verify.

**Contains:** approach; scoping rules; when to split work; handling
missing requirements; protected files; keeping docs in sync; checklist
before moving to the next unit.

**Good looks like**

- Imperative commands: "Work on one unit at a time."
- The missing-requirements section says exactly what to do: stop, add
  the question to the progress tracker, ask the user.
- Protected files are listed by path.
- The final checklist names this project's real commands (the actual
  build, lint, and test commands).

**Common failure:** leaving the template's generic text. The "when to
split" list and the protected files must come from this project.

## progress-tracker.md

**Job:** restores full context at the start of every session. It is the
one file that changes constantly.

**Contains:** current phase; current goal; completed; in progress; next
up; open questions; architecture decisions; session notes.

**At the end of planning it should hold**

- Current phase: Planning complete, build not started
- Next up: unit 01 from the build plan
- Open questions: everything left undecided, each with its working
  assumption
- Architecture decisions: each significant choice with the reason, and
  the options that were rejected and why
- Session notes: anything a fresh session needs to know

**Common failure:** dropping the reasons. "Chose Postgres" is far less
useful to a later session than "Chose Postgres over MongoDB because the
data is relational and the user already knows SQL".

## Entry point (CLAUDE.md / AGENTS.md)

**Job:** the file the agent reads first, telling it to read the context
files in order and keep them updated.

Keep it short. List only files that exist. Add a line pointing to
`context/specs/00-build-plan.md` and noting that each unit is specified
in `context/specs/` before it is built. If the file already exists,
append this section and leave the rest untouched.
