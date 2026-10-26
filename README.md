# vanskills

Personal, agent-agnostic skills for coding agents (Claude Code, Codex, OpenCode, and others), inspired by Matt Pocock's skills and pstack.

Each skill lives in `skills/<name>/SKILL.md` and follows the [Agent Skills](https://agentskills.io) format: YAML frontmatter with `name` and `description`, plus optional `references/`.

## Skills

| Skill | Use it for |
|-------|-----------|
| [`codebase-design`](skills/codebase-design/SKILL.md) | Shared vocabulary for designing deep modules: module, interface, depth, seam, adapter, leverage, locality, plus the deletion test and design-it-twice. |
| [`improve-codebase-architecture`](skills/improve-codebase-architecture/SKILL.md) | Scan for shallow modules, present deepening opportunities as a visual HTML report, then grill through the one you pick. Manual only. |
| [`domain-modeling`](skills/domain-modeling/SKILL.md) | Build and sharpen the project's domain model while designing: challenge terms, invent edge cases, record glossary entries and ADRs. |
| [`grill-me`](skills/grill-me/SKILL.md) | Manual entry point for `grilling`. |
| [`grill-with-docs`](skills/grill-with-docs/SKILL.md) | `grilling` plus `domain-modeling`: a relentless interview that writes ADRs and the glossary as decisions settle. Manual only. |
| [`grilling`](skills/grilling/SKILL.md) | Stress-test a plan, decision, or idea through rounds of numbered questions, each with a recommended answer, until the design tree is fully settled. |
| [`create-verification-skill`](skills/create-verification-skill/SKILL.md) | Generate a project-local `verify-<app>` skill that launches the real app, drives it like a user, and captures evidence. |
| [`maintain-verification-skill`](skills/maintain-verification-skill/SKILL.md) | Keep a `verify-<app>` skill and its feature map honest: read every feature from source, drive every feature live, ship at most one PR of proven fixes. |
| [`how`](skills/how/SKILL.md) | How code works: subsystem architecture, runtime flow, and where things should live. Use `why` for motivation. |
| [`teach`](skills/teach/SKILL.md) | Explain a body of work so a person actually understands it, combining `how` and `why` into one plain, paced explanation. |
| [`how-to-test`](skills/how-to-test/SKILL.md) | Open the running app on the current change and hand the human a step-by-step guide to test it: where to go, what to do, what they should see. |
| [`pr`](skills/pr/SKILL.md) | Write a PR body: a visual summary, before/after evidence, and merge danger (one-way or two-way door, blast radius). |
| [`skill-creator`](skills/skill-creator/SKILL.md) | Create or revise an agent skill: earn it, pick invocation, write the description as a context pointer, write and prune the body, validate. Carries the `writing-for-agents` reference. |
| [`why`](skills/why/SKILL.md) | Why code is shaped the way it is: design rationale, tradeoffs, regressions, data-backed thresholds. Cited and confidence-weighted. |

## Install

### Claude Code (plugin)

The repo is a Claude Code plugin and marketplace. Skills load namespaced as `vanskills:<skill>`, for example `/vanskills:why`.

```sh
claude plugin marketplace add /path/to/vanskills
claude plugin install vanskills@vanskills
```

After changing a skill, bump `version` in `.claude-plugin/plugin.json`, commit, run `claude plugin marketplace update vanskills && claude plugin update vanskills@vanskills`, and restart Claude Code.

### Other agents

Symlink or copy a skill folder into your agent's skills directory, for example:

```sh
ln -s "$PWD/skills/why" ~/.claude/skills/why
```

See [NOTICE.md](NOTICE.md) for attribution of adapted skills.
