# vanskills

Personal, agent-agnostic skills for coding agents (Claude Code, Codex, OpenCode, and others), inspired by Matt Pocock's skills and pstack.

Each skill lives in `skills/<name>/SKILL.md` and follows the [Agent Skills](https://agentskills.io) format: YAML frontmatter with `name` and `description`, plus optional `references/`.

## Skills

| Skill | Use it for |
|-------|-----------|
| [`create-verification-skill`](skills/create-verification-skill/SKILL.md) | Generate a project-local `verify-<app>` skill that launches the real app, drives it like a user, and captures evidence. |
| [`maintain-verification-skill`](skills/maintain-verification-skill/SKILL.md) | Keep a `verify-<app>` skill and its feature map honest: read every feature from source, drive every feature live, ship at most one PR of proven fixes. |
| [`why`](skills/why/SKILL.md) | Why code is shaped the way it is: design rationale, tradeoffs, regressions, data-backed thresholds. Cited and confidence-weighted. |

## Install

Symlink or copy a skill folder into your agent's skills directory, for example:

```sh
ln -s "$PWD/skills/why" ~/.claude/skills/why
```

See [NOTICE.md](NOTICE.md) for attribution of adapted skills.
