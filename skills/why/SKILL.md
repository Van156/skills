---
name: why
description: "Use for 'why does X work this way', 'why we picked Y', design rationale, regressions, postmortems, or data-backed thresholds. Discovers the evidence sources available to the agent (source control, issue tracker, long-form docs, real-time chat, infrastructure observability, error tracking, product analytics warehouse), investigates each one in parallel, then returns a cited, confidence-weighted read on decisions and tradeoffs. Not for explaining what code does at runtime."
license: MIT
---

# Why

Investigate the motivation and intent behind code. Other questions cover what the code does and how it works. `why` answers what forces led to its shape.

This skill works in any coding agent. It needs shell access for `git` (and `gh` when the repo is on GitHub). Subagents and MCP servers help but aren't required.

## Running Subagents

Investigators and the synthesizer run as **subagents** when the runtime has them: Claude Code's Agent/Task tool, Codex subagents, OpenCode's task tool, an orchestrator's delegation tool, or similar. Give each one full read access, including MCP tools, since the MCP-backed investigators and the synthesizer's citation spot-checks need them. Investigators never write files.

Pick the strongest reasoning model and the highest reasoning effort available for both roles. This is evidence work where precision matters more than speed.

**No subagents?** Run each investigation yourself, one category at a time, following the same prompt and playbook. Write each category's findings as a separate block before starting the next, so one source's evidence doesn't bleed into another. Then do the synthesis step yourself under the same rules.

## Operating Posture

Operate as a **careful, cautious, and precise investigator**. Be honest about what you know vs what you're inferring. Read `references/epistemics.md` for the full confidence framework and phrasing guide. The synthesizer must follow it.

## Step 1. Understand the Target and the Question

The **target** is usually a chunk of code, a pattern, a feature, or a named design decision. The **question** is usually a design rationale, a tradeoff, a motivating edge case, an external constraint, dead code, or a broad history sweep.

If the target is vague ("why do we do it this way?" with no clear referent), make your best guess from conversation context (open files, recent edits, cursor location, what was just discussed). State your interpretation briefly so the user can redirect if you're off, then proceed.

## Step 2. Establish the Code Anchor

Before starting investigators, build the seed context yourself: file paths and line ranges, key symbols (functions, classes, constants), the last few commits touching the target, PR numbers from merge commits (pattern `(#1234)` in the subject line), and any linked ticket IDs.

- `git blame -L <start>,<end> <file>` for last-touch commits
- `git log --follow -p -- <file>` for full history with patches through renames
- `git log --oneline -20 -- <file>` for recent commits with PR numbers visible
- `git log -1 --format=%B <commit>` to extract PR numbers

If the repo is on GitHub and `gh` is available, pull the PR bodies and discussion for any substantive commit: `gh pr view <number> --json title,body,author,createdAt,mergedAt,labels,closingIssuesReferences,comments,reviews`. For other hosts, use their CLI or MCP if one is available.

This anchor goes to every investigator and to the synthesizer.

## Step 3. Run Parallel Investigators

**Default to the full parallel investigation.** The goal is a complete coverage map, not a minimal one. Document the null, don't skip the search.

### Discovery

Before starting investigators, list the MCP servers and other tools this session exposes.

Map each available source to one evidence category:

1. Source control history
2. Issue / ticket tracker
3. Long-form documents
4. Real-time team chat
5. Infrastructure observability
6. Error / exception tracking
7. Product analytics warehouse

Source control is always available through `git`. For the other six, classify using the MCP name, server instructions, tool names, and resource descriptors. If a source could fit more than one category, choose the one matching its primary evidence. Record ambiguous cases in the coverage map.

Start one investigator per category that has a matching source, all at once so they run concurrently, each owning exactly one tool or MCP. Don't ask one investigator to cover several MCPs.

Each investigator's prompt is `references/investigator-prompt.md` filled in, plus:

- the category playbook `references/sources/<source>.md` for the selected source, adapted from the examples in `references/source-playbook.md`
- `references/sources/incident-postmortem.md` **if the target code looks defensive** (null checks, retry logic, timeout handling, rate limiting, feature flags, egress guards, OOM handlers)
- the code anchor and the user's original question

### Investigator roster, one per available evidence category

Each category below is uniquely positioned to surface one shape of "why", shown after "Best at". Use it to know what to expect back, to name a gap when a category returns empty, and, only in the rare provably-irrelevant case, to justify a skip.

1. **Source control** (git history, PRs, code comments, tests). Always run, the only guaranteed source. Best at *implementation-time rationale captured during review*.
2. **Issue / ticket tracker** (e.g. Linear, Jira, GitHub Issues, Plane, Shortcut). Best at *the product or business forcing function*. Strongest when the why is external to engineering.
3. **Long-form documents** (e.g. Notion, Confluence, Google Docs, Coda). Best at *long-form design rationale*, written out before it becomes code.
4. **Real-time team chat** (e.g. Slack, Discord, Microsoft Teams, Mattermost). Best at *real-time deliberation that never reached a doc*. Especially important when the source control, ticket, and doc paper trail is thin.
5. **Infrastructure observability** (e.g. Datadog, New Relic, Honeycomb, Grafana, Splunk). Best at *infrastructure and runtime reality that motivated the code*. Strongest when the target reacts to an infra signal (timeouts, retries, rate limits, circuit breakers).
6. **Error / exception tracking** (e.g. Sentry, Rollbar, Bugsnag, Airbrake). Best at *the specific exceptions and error trajectories that motivated defensive or corrective code*. Strongest for catch blocks, null guards, type checks, retries, and other defenses.
7. **Product analytics warehouse** (e.g. Databricks, Snowflake, BigQuery, ClickHouse, dbt, Redshift). Best at *product and data reality that shaped the code*. Strongest for flag-gated code, experiment-driven ships, data migrations, and "where did this number come from" questions.

### When to skip an investigator

Only with an **explicit, written justification** that goes in the final Sources Consulted. Two valid reasons:

- **No source is available for that category** in this environment. Flag it as a gap, not a choice: "Real-time team chat skipped. No matching MCP available, so the conversational record was not searchable."
- **The source is provably irrelevant**, not just "probably irrelevant." A high bar: "Error / exception tracking skipped. Target is a build-time script with no runtime code path."

If your scope assessment suggests a single-commit trivial target where the PR description already contains the complete answer, you may answer inline **only after** confirming every available category search would be redundant. Say so explicitly. This should be rare.

## Step 4. Synthesize

Run one synthesizer (a subagent, or yourself if subagents aren't available). It gets:

1. The investigator findings, including any null results and any categories skipped with justification
2. The code anchor
3. The user's original question
4. The epistemics framework from `references/epistemics.md`
5. The synthesizer prompt template from `references/synthesizer-prompt.md`

## Step 5. Present

Present the synthesizer's output to the user. You may lightly edit for clarity or add context from the conversation, but **do not rewrite the confidence language**.

## Output Format

The structure is the one in `references/synthesizer-prompt.md`: The Question, The Code in Question, What We Found, What We Can Reasonably Infer, Competing Hypotheses, What We Don't Know, Sources Consulted, Confidence Summary. Adapt as needed, but keep the confidence separation intact, and keep Sources Consulted as one line per investigator, including the ones that returned nothing or were skipped, with the reason.

After Sources Consulted, if the user's `why` question is a precursor to changing this code, convert the lineage findings into a Preserve / Change / Avoid / Risk constraint set for planning the change.

## Common Failure Modes to Avoid

- **Recency bias**. Assuming the most recent commit is authoritative. The current shape is often the accretion of many earlier decisions. Trace back.
