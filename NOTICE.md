# Notice

Some skills in this repository are adapted from MIT-licensed work:

| Skill | Source | Copyright |
|-------|--------|-----------|
| `why` | [t3-pstack](https://github.com/chhoumann/t3-pstack) `plugins/pstack/skills/why` | Copyright (c) 2026 Lauren Tan |

Changes to `why`: removed the dependency on T3 Code orchestration (`delegate_task`, the per-machine model sheet, and the `poteto-mode` skill) so the skill runs in any coding agent, with a sequential fallback when subagents are unavailable. Reference files are unchanged.

The original MIT license text applies to the adapted portions:

> Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
