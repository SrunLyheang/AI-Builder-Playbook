# AI Builder's Playbook

Three agent skills that make you plan an app before any code is written,
then build it one specified unit at a time.

Most AI-built projects fail the same way: the idea was vague, the agent
filled the gaps with guesses, and the result drifted away from what you
had in mind. These skills close the gaps first.

## The workflow

| Step | Skill | What it does | Output |
| ---- | ----- | ------------ | ------ |
| 1 | `plan-app` | Interviews you one question at a time, pushes back on vague answers, offers options with a recommendation when you are unsure | `context/` files, `CLAUDE.md` or `AGENTS.md`, `context/specs/00-build-plan.md` |
| 2 | `spec-unit` | Writes the spec for the next unit in the build plan | `context/specs/NN-unit-name.md` |
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
    ├── ai-workflow-rules.md
    ├── progress-tracker.md
    ├── planning-notes.md
    └── specs/
        ├── 00-build-plan.md
        ├── 01-first-unit.md
        └── ...
```

## Install

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
`/spec-unit`, `/build-unit`.

## Credit

The context-file system and spec-driven workflow are adapted from JavaScript Mastery.
