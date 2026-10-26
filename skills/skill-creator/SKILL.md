---
name: skill-creator
description: Create or revise an agent skill. Use when the user wants a new skill, a rewrite of an existing one, or to turn a repeated workflow or convention into reusable agent instructions.
license: MIT
---

Build the skill by running the steps below in order. Each step ends on a completion criterion; do not start the next step until it holds.

Read [`references/writing-for-agents.md`](references/writing-for-agents.md) before step 3. Read [`references/skill-mechanics.md`](references/skill-mechanics.md) before step 2.

## 1. Earn the skill

A skill earns its place when a workflow repeats or a convention is unwritten and non-obvious. A one-off task or a lookup the environment already answers (config, `--help`, directory layout) is not a skill.

Search the skills directories already installed and the repo you are in for an existing skill that covers the same branches. Extend it instead of adding a second.

**Done when** you can list the skill's **branches** (the distinct cases it handles) and have named any existing skill checked.

## 2. Choose invocation and home

Pick model-invoked or user-invoked per the mechanics reference: model-invoked only when the agent, or another skill, must reach it unprompted. Pick the folder: `<skills-dir>/<name>/SKILL.md`, name in lowercase kebab-case, matching the folder.

**Done when** invocation, name, and location are decided.

## 3. Write the description

The description is the skill's context pointer. Front-load the leading word, write one trigger per branch, and cut identity the body already carries. User-invoked: one human-facing summary line instead.

**Done when** every branch from step 1 has a trigger and no trigger is a synonym of another.

## 4. Write the body

Steps in order, each ending on a checkable, exhaustive completion criterion. Push reference only some branches reach into `references/<topic>.md` and point at it from the step that needs it. Prompt the positive behaviour; keep a prohibition only as a hard guardrail paired with its positive target.

**Done when** every step has a completion criterion and every file the body names exists.

## 5. Prune

Go sentence by sentence: delete no-ops (the model already does it), duplicates (one meaning, one place), and caches of what the environment answers. Replace spelled-out triads with a leading word.

**Done when** each remaining sentence changes behaviour versus the default.

## 6. Validate and register

- Frontmatter has `name` (equals the folder name) and `description`, on valid YAML.
- Every relative link and referenced file resolves.
- If the skill lives in a collection, add it where the collection lists skills (README table, plugin manifest, NOTICE for adapted work), following that collection's own rules.

**Done when** all three checks were run and observed.

## Reply

Files created or changed, invocation choice and why, branches covered, and anything validated or skipped.
