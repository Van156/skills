---
name: how-to-test
description: "Open the running app on the current change and hand the human a step-by-step guide to test it themselves: what changed, where to find it, what to do, and what they should see. Use for /how-to-test, \"open the app and tell me how to test this\", \"how do I check these changes\", or before a human reviews an uncommitted or branch change."
license: MIT
---

# How to test this change

The agent built or changed something, and now a human wants to see it with their own hands before they trust it. This skill does two things: it puts the real app in front of them, already running the change and already past the boring setup, and it gives them a short guide that turns the diff into things to press and results to expect. The guide is for a person sitting at the app, not for an agent: every step names a place they can reach and a result they can see.

Do not commit, push, or clean up as part of this skill. The human is reviewing. Leave the instance running and tell them how to stop it.

## 1. Pin down the change

Work from the actual diff, not from memory of the session:

- `git status` and `git diff` for uncommitted work, plus `git log <base>..HEAD` and `git diff <base>...HEAD` for the branch. If the change spans both, say which revision the app will run.
- Read any feature doc, task file, or PR description that came with the change. They often name the intent and the measurements.

Sort what you find into three lists:

- **Changed behavior:** anything a user can see or feel that is now different (a new screen, different feedback, a new option, a fixed bug).
- **Preserved behavior:** what the change touched but must not alter (ranking order, filters, permissions, saved data). These become regression checks.
- **Invisible changes:** performance, refactors, caching. These need a way to observe them, or an honest note that a human can't see them directly.

## 2. Open the app for them

Get the app to the first screen the guide needs, on the change's code:

- **Prefer the project's verification skill** (a `verify-<app>` skill in the project skills directory). Use its launch and doctor steps, then its own flows to get past sign-in or onboarding. Reuse an existing build when only interpreted code changed (JS served by a bundler, a dev server with hot reload). Rebuild when native code, config, or dependencies changed.
- **Otherwise use the repo's documented dev command.** Never stop or reuse an instance the human started; run on separate ports or a separate device. If the project has no scripted way to launch and drive the app, finish this guide by hand and suggest `create-verification-skill`.
- **Bring it to the front** (open the simulator, browser tab, or terminal window) and confirm with the doctor or a screenshot that it rendered.

Tell them how to reach the instance: its name, the URL or device, how to open a route or deep link directly, and the credentials of the seeded test account if they get signed out. Only ever print credentials the run created for verification, never real ones.

## 3. Write the guide

Group the steps by change, in the order a person would walk through the app. Each step has three parts:

- **Where:** a screen, route, deep link, or command they can open directly.
- **Do:** concrete actions with concrete input (type `bench`, press and hold a row, drag a little before releasing), not "try the search".
- **Expect:** the observable result, with exact values where they exist (`113 results`, `Bench Press first`, `no results`).

Cover, in this order:

1. **Changed behavior.** One step per user-visible change. Include the edge cases the code handles (drag during a press, disabled state, empty list, offline).
2. **Preserved behavior.** Take the inputs and expected outputs from the tests or the baseline evidence, never invented ones. If a value was observed before and after the change, quote it.
3. **Invisible changes.** Say how a person can observe them (a perf overlay, a network panel, a log line) and what caveats apply (debug builds are slower, simulators run on the host CPU, compare only against the same setup). Add the scripted measurement commands and where the baseline lives, marked optional.

Every expected result must come from code, a test, or evidence you saw. If you can't back one up, mark the step `unverified` rather than guess. Drive at least the first step of each group yourself, or check that its route resolves, so the guide doesn't send them somewhere broken.

## 4. Hand over

End with:

- the instance they are looking at and how to reach it;
- the guide, grouped as above;
- the cleanup command, and the note that it stops only what this run started;
- anything you could not set up for them (a device you don't have, a permission prompt only they can accept).

Keep it short enough to follow with one hand on the mouse. A guide they have to scroll back through while testing is too long; cut background and keep the steps.

See [`references/guide-example.md`](references/guide-example.md) for the shape of a finished hand-off.
