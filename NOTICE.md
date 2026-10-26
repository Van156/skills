# Notice

Some skills in this repository are adapted from MIT-licensed work:

| Skill | Source | Copyright |
|-------|--------|-----------|
| `codebase-design` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/codebase-design` | Copyright (c) 2026 Matt Pocock |
| `create-verification-skill` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/create-verification-skill` | Copyright (c) 2026 Lauren Tan |
| `domain-modeling` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/domain-modeling` | Copyright (c) 2026 Matt Pocock |
| `grill-me` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/productivity/grill-me` | Copyright (c) 2026 Matt Pocock |
| `grill-with-docs` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/grill-with-docs` | Copyright (c) 2026 Matt Pocock |
| `grilling` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/productivity/grilling` | Copyright (c) 2026 Matt Pocock |
| `how` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/how` | Copyright (c) 2026 Lauren Tan |
| `improve-codebase-architecture` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/improve-codebase-architecture` | Copyright (c) 2026 Matt Pocock |
| `maintain-verification-skill` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/maintain-verification-skill` | Copyright (c) 2026 Lauren Tan |
| `pr` | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/pr` | Copyright (c) 2026 Matt Pocock |
| `teach` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/teach` | Copyright (c) 2026 Lauren Tan |
| `why` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/why` | Copyright (c) 2026 Lauren Tan |

Changes to `why`: removed the dependency on T3 Code orchestration (`delegate_task`, the per-machine model sheet, and the `poteto-mode` skill) so the skill runs in any coding agent, with a sequential fallback when subagents are unavailable. Reference files are unchanged.

Changes to `create-verification-skill`: generalized the project skills directory, made the maintenance hand-off work without the `maintain-verification-skill` skill installed, and replaced `disable-model-invocation` with `license`. Reference files are unchanged.

Changes to `maintain-verification-skill`: generalized the project skills directory and slash-command references, added a sequential fallback for runtimes without subagents and a commit fallback for repos without a PR host, and replaced `disable-model-invocation` with `license`.

Changes to `how`: removed the dependency on T3 Code orchestration (`delegate_task`, the per-machine model sheet, and the `poteto-mode` skill) so the skill runs in any coding agent, with a sequential fallback when subagents are unavailable, and replaced `disable-model-invocation` with `license`. The explainer reference names file tools generically instead of Claude Code's `Read`, `Grep`, and `Glob`.

Changes to `teach`: removed the dependency on the `unslop` skill (its plain-English rules are kept inline), added a fallback for runtimes that can't invoke `how` or `why` as skills, made the image-generation tool optional, and replaced `disable-model-invocation` with `license`.

Changes to `grill-me` and `grill-with-docs`: replaced the Claude Code-specific "Call the Skill tool" instruction with agent-agnostic wording. `grilling` and `domain-modeling` are unchanged.

Changes to `improve-codebase-architecture`: replaced the Claude Code-specific "Call the Skill tool" instructions with agent-agnostic wording. `codebase-design` is unchanged.

The original MIT license text applies to the adapted portions:

> Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
