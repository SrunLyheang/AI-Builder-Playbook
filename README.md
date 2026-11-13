# AI Builder's Playbook

Three agent skills that make you plan an app before any code is written,
then build it one specified unit at a time.

Most AI-built projects fail the same way: the idea was vague, the agent
filled the gaps with guesses, and the result drifted away from what you
had in mind. These skills close the gaps first.

## The workflow

| Step | Skill | What it does | Output |
| ---- | ----- | ------------ | ------ |
| 1 | `plan-app` | Interviews you in four short rounds (product, system, design, rules), pushes back on vague answers, recommends an answer when you are unsure | `context/` files, `CLAUDE.md` or `AGENTS.md`, `context/specs/00-build-plan.md` |
| 2 | `spec-unit` | Writes a self-contained spec for the next unit in the build plan | `context/specs/NN-unit-name.md` |
| 3 | `build-unit` | Implements that unit exactly as specified, verifies it, updates the tracker | Code, updated `context/progress-tracker.md` |

Run step 1 once per project. Repeat steps 2 and 3 for every unit.

```
your-project/
├── CLAUDE.md or AGENTS.md
└── context/
    ├── project-overview.md
    ├── architecture.md
    ├── ui-context.md
    ├── code-standards.md
    ├── progress-tracker.md
    └── specs/
        ├── 00-build-plan.md
        ├── 01-first-unit.md
        └── ...
```

## Install

One command, works with Claude Code, Cursor, Codex, Gemini CLI and
other agents that support `SKILL.md`:

```bash
npx skills add SrunLyheang/AI-Builder-Playbook
```

Install a single skill, or target specific agents:

```bash
npx skills add SrunLyheang/AI-Builder-Playbook --skill plan-app
npx skills add SrunLyheang/AI-Builder-Playbook -a claude-code -a cursor
```

### Manual install

Copy the three folders inside `skills/` into your agent's skills
directory.

- **Claude Code:** `~/.claude/skills/` for all projects, or
  `.claude/skills/` inside one project.
- **Other agents:** these follow the open `SKILL.md` format. Check your
  agent's documentation for where it loads skills from.

## Use

Start in an empty project folder (or an existing one) and say what you
want to build:

```
I have an idea for an app. Help me plan it.
```

Then, for each unit:

```
Write the spec for the next unit.
Build the next unit.
Unit 01 works. Close it out.
```

In Claude Code you can also call them directly: `/plan-app`,
`/spec-unit`, `/build-unit`, `/build-unit close`.

## Models and token use

Planning is where the thinking happens, so it gets the strong model;
building follows a finished spec, so it gets a cheaper one.

| Skill | Model | Why |
| ----- | ----- | --- |
| `plan-app` | strongest available (`opus`) | Every later step builds on these decisions |
| `spec-unit` | strongest available (`opus`) | Turns the plan into exact instructions |
| `build-unit` | mid-tier (`sonnet`, medium effort) | Carries out a spec that leaves nothing to decide |

Claude Code applies this from each skill's `model` field, for the turn
that invokes the skill. `plan-app` runs over several turns, so set the
session model yourself for the interview. Other agents ignore the
field: switch models by hand between steps. To use different models,
edit the `model` line at the top of each `SKILL.md`.

The skills are also built to read little:

- The entry-point file tells the agent to read only the progress
  tracker each session and to open other context files when a task
  needs them.
- Each spec copies the few rules, tokens, and commands its unit needs,
  so `build-unit` reads two files: the spec and the tracker.
- Questions come in batches with a recommended answer, so you can reply
  "ok" to most of them.

## Credit

The context-file system and spec-driven workflow are adapted from JavaScript Mastery.
