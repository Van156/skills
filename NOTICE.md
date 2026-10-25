# Notice

Some skills in this repository are adapted from MIT-licensed work:

| Skill | Source | Copyright |
|-------|--------|-----------|
| `create-verification-skill` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/create-verification-skill` | Copyright (c) 2026 Lauren Tan |
| `maintain-verification-skill` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/maintain-verification-skill` | Copyright (c) 2026 Lauren Tan |
| `why` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/why` | Copyright (c) 2026 Lauren Tan |

Changes to `why`: removed the dependency on T3 Code orchestration (`delegate_task`, the per-machine model sheet, and the `poteto-mode` skill) so the skill runs in any coding agent, with a sequential fallback when subagents are unavailable. Reference files are unchanged.

Changes to `create-verification-skill`: generalized the project skills directory, made the maintenance hand-off work without the `maintain-verification-skill` skill installed, and replaced `disable-model-invocation` with `license`. Reference files are unchanged.

Changes to `maintain-verification-skill`: generalized the project skills directory and slash-command references, added a sequential fallback for runtimes without subagents and a commit fallback for repos without a PR host, and replaced `disable-model-invocation` with `license`.

The original MIT license text applies to the adapted portions:

> Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
