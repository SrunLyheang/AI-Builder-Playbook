# Build Plan

Units are built in this order. Each unit gets its own spec file in
`context/specs/` (written just before it is built) and is tracked in
`context/progress-tracker.md`. Status lives in the progress tracker,
not here.

## Units

| #  | Unit name | Boundary | Depends on |
| -- | --------- | -------- | ---------- |
| 01 | [name]    | [e.g. UI / API / database / auth] | — |
| 02 | [name]    | [boundary] | 01 |

## Unit Details

### Unit 01: [Unit name]

- **Builds:** [The one visible, verifiable result this unit produces]
- **Not in this unit:** [What is deliberately left for a later unit]
- **New dependencies:** [Packages first needed here, or "none"]
- **Done when:** [One-line end state a person can check]

### Unit 02: [Unit name]

- **Builds:**
- **Not in this unit:**
- **New dependencies:**
- **Done when:**
