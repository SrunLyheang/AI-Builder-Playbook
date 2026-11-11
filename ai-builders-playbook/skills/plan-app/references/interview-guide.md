# Interview Guide

Topics in order. For each: what must be answerable by the end, starter
questions, and what to push back on. The starter questions are openers;
follow the user's answers rather than reading the list aloud. Skip a
question when an earlier answer or the existing code already covers it.

## Contents

1. Product
2. Users
3. Core flow
4. Features and scope
5. Data and storage
6. Auth and access
7. Hard parts and risks
8. Stack
9. Design
10. Process and rules
11. Pushback patterns
12. Readiness checklist

---

## 1. Product

**Must be answerable:** What does the app do, in one sentence?

- What problem does this solve, and how is that problem handled today?
- If it worked perfectly, what would be different for the person using it?
- Can you say what it does in one sentence, without the word "and"?

**Push back when** the sentence describes a category ("a productivity
app") instead of an action ("turns a bank export into a monthly spending
dashboard"), or when it needs three clauses to cover everything.

## 2. Users

**Must be answerable:** Who is the primary user and what is their core need?

- Who is the one person this is for first? Describe a real one if you can.
- What are they trying to get done when they open the app?
- Is there a second kind of user (admin, viewer, customer)? Do they need
  to exist in the first version?
- Is this for you, for people you know, or for strangers?

**Push back when** the user is "everyone" or "businesses". Each extra
user type multiplies screens, permissions, and tests.

## 3. Core flow

**Must be answerable:** The step-by-step path from first open to the
moment the app delivers its value.

- Walk me through the first visit, click by click. What is on the first
  screen?
- At which step does the user get the thing they came for?
- What does the user see when there is no data yet?
- What happens when a step fails (bad input, upload fails, AI returns
  nonsense, payment declined)?
- What does a return visit look like?

**Push back when** a step hides work ("then the app analyzes it"), when
the flow skips from sign-up to the finished result, or when only the
happy path is described. Every vague verb (process, manage, handle,
sync, analyze) hides a screen or a system; ask what happens on screen
and behind it.

## 4. Features and scope

**Must be answerable:** The three most important features of version
one, and an explicit list of what is not being built.

- List everything you imagine this app doing. (Let them get it all out.)
- If you could ship only three of these, which three?
- For each of the rest: later, or never?
- What would a user reasonably expect that you are choosing to leave out?

**Push back when** version one has more than a handful of features, when
a "small" feature is large (notifications, payments, real-time
collaboration, offline mode, multi-language, admin panels, mobile apps),
or when nothing is out of scope. The out-of-scope list is what stops an
agent from adding things, so make it long and specific.

## 5. Data and storage

**Must be answerable:** What the app stores, where each kind of data
lives, and how the pieces relate.

- What are the main things the app keeps track of? (Nouns from the core
  flow: users, projects, orders.)
- For each: what fields matter, who owns it, and what does it belong to?
- Are there files (images, video, PDFs, exports)? How large, how many?
- Does anything need to be fast enough to cache, or is that premature?
- What must never be lost? What can be regenerated?
- Does any data come from or go to an outside service?

**Push back when** relationships are unclear ("a project has members" -
one owner or many? can a member be in several projects?), when large
files are headed for the database, or when deletion has not been thought
about (what happens to a user's data when the account is removed?).

## 6. Auth and access

**Must be answerable:** How users sign in, who owns what, and who may
read or change each thing.

- Does the app need accounts at all in version one?
- How do people sign in (email and password, magic link, Google, phone)?
- Who can see a given record? Who can change it? Who can delete it?
- Are there roles? Is sharing or inviting part of version one?
- What is public without signing in?

**Push back when** ownership is assumed but unstated, when roles appear
without a feature that needs them, or when the user plans to build auth
by hand without a reason.

## 7. Hard parts and risks

**Must be answerable:** The most technically complex parts, and what
could go wrong.

- Which part of this are you least sure how to build?
- Is there anything slow (AI calls, video processing, large imports)?
  What does the user see while waiting?
- Which outside services does it depend on? What do they cost, what are
  their limits, and what happens when they are down?
- Is there anything with real-world consequences (money, private data,
  messages sent to other people)?
- What is the one thing that, if it does not work, makes the app
  pointless?

**Push back when** the user says nothing is hard. Name the part you
think is hardest and ask how it will work. For a risky core piece,
suggest making it an early unit so it is proven before the rest is built
around it.

## 8. Stack

**Must be answerable:** The full technology stack and the reason for
each choice.

Ask first what the user already knows and whether anything is fixed
(school requirement, existing code, team). Then cover each layer the app
needs: language and framework, UI and styling, database and how it is
accessed, auth, file storage, background jobs, AI or other external
APIs, hosting, testing.

For each layer the user has not decided, use the options format from
SKILL.md: two or three options with trade-offs, one recommendation with
reasons, and why each of the others loses for this project.

**Push back when** a choice comes from hype rather than need, when a
tool is chosen for scale the app will not see, when two tools overlap,
or when a layer is included that no version-one feature uses. Weigh "the
user already knows it" heavily; an unfamiliar stack plus an unfamiliar
problem is two risks at once.

Then settle structure:

- Which folder owns which responsibility? (Agree on exact folder names.)
- What must each folder never do?

## 9. Design

**Must be answerable:** The visual language, the component library, and
the high-level layout.

Skip this topic for projects with no interface.

- Dark, light, or both?
- What should it feel like? (Technical or friendly, dense or airy,
  playful or serious.) Name an app whose look you would like to be near.
- Any brand colors already decided?
- Which component library, if any?
- What is the main layout: sidebar, top navigation, single column,
  bottom tabs?
- Phone, desktop, or both, and which one first?
- Which icon set?

When the user has no palette, propose one: a full set of named tokens
with hex values (backgrounds, surfaces, text, accent, border, error,
success), fonts, and a border radius scale. Offer two directions if they
are unsure and let them pick. Every color the app will need must have a
token name, so the agent never invents one.

**Push back when** the answer is only "clean and modern". Ask for a
reference app or offer two contrasting directions.

## 10. Process and rules

**Must be answerable:** The invariants, the conventions, and the files
an agent must not touch.

- What rules should the codebase never break? Propose candidates drawn
  from the earlier answers and let the user confirm, for example: every
  mutation checks ownership; secrets are read only on the server; slow
  work never runs inside a request handler; components use color tokens
  only.
- Any conventions you care about (naming, file layout, typing strictness,
  testing expectations)?
- Which files or folders are off limits to the agent (generated
  components, migrations, vendored code)?
- Which commands verify a change (build, lint, type check, test)?
- How do you use git: a branch per unit, commit messages, who pushes?

Aim for at least four invariants. A good invariant is a rule whose
violation is visible in a code review, written as a statement, not a
preference.

The unit breakdown itself is done in Step 3 of SKILL.md, after the
context files are written.

---

## 11. Pushback patterns

| Signal | Response |
| ------ | -------- |
| Vague verb: manage, handle, process, sync | "What does the user click, and what changes on screen?" |
| "Like X but better" | "Better at which one thing, for which user?" |
| "AI-powered" | "What goes in, what comes out, and what happens when the output is wrong?" |
| "Real-time" | "Does it break the app if this updates after a refresh instead?" |
| "Scalable" | "How many users in the first three months?" |
| "Simple" or "just" | Spell out the steps it involves and ask if it is still version one. |
| Feature list without a flow | Go back to the core flow and place each feature in it. |
| Contradiction with an earlier answer | Quote both and ask which one stands. |
| "I don't know" | Options format: two or three options, recommendation, why the others lose. |
| Everything is in scope | "If the deadline were two weeks, what would you cut?" |

Every response in this table is followed by your recommended answer and
the reason for it, as described under "Vague answers are never accepted"
in SKILL.md.

Stop pushing on a point once the user has given a concrete answer, or
has heard the concern and made an informed choice. Record the choice and the reason, then move on.

## 12. Readiness checklist

The interview is done when the user can answer all of these without
hesitating. Check every line before writing files.

**Product**

- [ ] What the application does, in one sentence
- [ ] The primary user and their core need
- [ ] The step-by-step flow from sign-up (or first open) to core value
- [ ] The three most important features for the first version
- [ ] What is explicitly out of scope

**Technical**

- [ ] The full stack, with a reason for each choice
- [ ] Where data lives: database, file storage, cache
- [ ] How authentication and ownership work
- [ ] System boundaries: which folder owns which responsibility
- [ ] The rules the codebase must never violate (four or more)

**Design** (skip if there is no interface)

- [ ] Colors as named tokens, typography, border radius scale
- [ ] The component library
- [ ] The high-level layout

**Process**

- [ ] The commands that verify a change
- [ ] Protected files
- [ ] The git approach

Anything still unchecked is either asked now or written down as an open
question with a working assumption.
