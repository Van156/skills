---
name: create-verification-skill
description: "Generate a project-local verification skill that drives your app the way a user does — any language, framework, or platform. Use for /create-verification-skill, \"make a control skill for this repo\", or when a project has no scripted way to prove UI/CLI/service behavior."
license: MIT
---

# Create a verification skill

Every serious project needs a scripted way to drive the real app and prove behavior: launch it, exercise a feature the way a user would, and capture evidence. This skill generates that as a project-local skill (`<skills dir>/verify-<app>/`, where `<skills dir>` is the project skills directory your agent reads: `.claude/skills` on Claude Code, `.agents/skills` for most other agents; follow the repo's existing convention if it has one) tailored to the repo. You write the generator's output for the next agent, not for a human: it will be read cold, mid-task, by an agent that has never seen the app.

## 1. Interview the repo, not the user

Answer these from the codebase and only ask the user what you cannot observe:

- **Surface:** what does a user actually touch? A web UI, a CLI/TUI, a desktop app, an API, a mobile app, a library? A repo can have several; pick the primary one and note the rest.
- **Run:** how does the app start locally? Prefer the repo's own documented dev command (package scripts, Makefile, README quickstart). Note ports, env vars, seed data, auth.
- **Drive:** how can an agent interact with it programmatically? Existing harnesses first — Playwright/Cypress specs, expect scripts, PTY helpers, curl-able endpoints, a debug port. Only then pick a generic recipe: browser/CDP for web and Electron, a tmux/PTY harness for CLI/TUI, plain HTTP for services, a simulator/emulator plus a UI flow runner for mobile (see [Mobile surfaces](#mobile-surfaces-expo--react-native)).
- **Observe:** what evidence can be captured? Screenshots, terminal transcripts, response bodies, logs, exit codes, DB state, device logs.
- **Isolate:** can two instances run side by side (ports, data dirs, profiles)? If not, say so in the generated skill: refusing to double-drive a shared instance beats corrupting the user's session.

If the checkout doesn't build or start as-is, fix that first (or report it precisely) before generating; a skill written against a broken base teaches wrong steps. When an irrelevant missing asset blocks startup (a static dir the API never serves, a sample config), the generated skill may create it, clearly marked as verification scaffolding, and remove it in cleanup.

## 2. Generate the skill

Write `<skills dir>/verify-<app>/SKILL.md` with YAML frontmatter (`name: verify-<app>` and a `description` that names the app, the surface, and when to reach for it — without frontmatter the skill never registers) and these sections, each grounded in what the interview actually found (no placeholders left):

- **Launch:** the exact command that starts the app for verification, and how to tell it's ready (a log line, a prompt). Launch must reach a usable state from an empty data dir, including any migrations and seed data. Ready means usable, not just that a process answers. A database health check can pass while its init scripts are still running. Include teardown. For a short-lived CLI or TUI there is no server to keep alive: launch means build the binary (or install deps) once, then start each drive in its own isolated PTY or tmux session.
- **Doctor:** one read-only check that answers "is this instance worth driving?" — process up, right version/build, port owned by us, auth valid. An agent runs this first whenever anything looks off.
- **Drive:** the harness recipe with real selectors/commands from this repo, not examples. Prefer stable handles (ARIA labels, data attributes, prompt strings, route paths) over coordinates and tab order.
- **Evidence:** what to capture for a proof and where it goes. State the proof standards: exercise the real user path, not internal setters or test-only endpoints; capture the action and the resulting state, not just the final screen; verify side effects (files written, rows inserted, messages sent) alongside what's visible; mocks only where a production boundary already isolates the external system. When the safe path is a dry-run or test mode, verify what it actually skips by observing (files, network, git refs) rather than trusting its name: some dry-runs still touch the network or open a browser.
- **Cleanup:** how to tear down instances the run created. Never kill by process name; kill what you started. Cleanup removes instances and scratch state, never the evidence: proof artifacts survive the teardown, in a location the skill names.
- **Helpers:** any script the skill ships is executable and its invocation is shown in the skill body. A helper the reader has to reverse-engineer is not a helper.

### Mobile surfaces (Expo / React Native)

A mobile app has three moving parts that each need their own ready check: the JS bundler, the device, and the installed app. Ground every choice below in the repo (`app.json`/`app.config.*`, `eas.json`, `package.json` scripts, an existing `ios/` or `android/` dir, existing `.maestro/` or `e2e/` flows).

- **Runtime:** Expo Go (`npx expo start`) only works when every native module the app uses ships in Expo Go. If the repo has a dev client (`expo-dev-client` dependency, `ios/`/`android/` dirs, or a `development` profile in `eas.json`), build it with `npx expo run:ios` / `npx expo run:android` (or the repo's EAS command) and drive that build instead. Say which one the generated skill uses and why.
- **Platform:** iOS Simulator needs macOS with Xcode; the Android emulator needs the Android SDK and an AVD. Pick the platform the host can actually run. If the repo targets both, prove one and record the other as unverified.
- **Launch:** start the bundler on an explicit port (`npx expo start --port <port>`). It is ready when `curl -s http://localhost:<port>/status` returns `packager-status:running`. Boot one specific device and wait until it is usable, not just listed: iOS with `xcrun simctl boot <udid>` then `xcrun simctl bootstatus <udid> -b`; Android with `emulator -avd <name> -port <port>` then poll `adb -s emulator-<port> shell getprop sys.boot_completed` until it prints `1`. Open the app through the deep link from the config's `scheme` (or `exp://127.0.0.1:<port>` for Expo Go) using `xcrun simctl openurl <udid> <url>` or `adb -s <serial> shell am start -W -a android.intent.action.VIEW -d <url>`. Ready means the first screen has rendered, which a flow runner can assert.
- **Doctor:** bundler status endpoint answers, the device is booted, the app is installed (`xcrun simctl get_app_container <udid> <bundle-id>` or `adb -s <serial> shell pm list packages <package>`), and the bundle the app loaded came from our bundler port, not a stale one.
- **Drive:** reuse existing Maestro or Detox flows first. Otherwise prefer Maestro (`maestro --device <id> test <flow>.yaml`), since it needs no app code changes. Select by `testID` and `accessibilityLabel`, not by coordinates or visible copy that changes with locale. If a journey's elements have no stable handle, report that in the feature's `Gotchas` instead of tapping by position.
- **Evidence:** screenshots via `xcrun simctl io <udid> screenshot <file>.png` or `adb -s <serial> exec-out screencap -p > <file>.png`, recordings via `xcrun simctl io <udid> recordVideo` or `adb shell screenrecord`, plus device logs (`xcrun simctl spawn <udid> log stream`, `adb logcat`) and the bundler output for JS errors. A red box or LogBox overlay in a screenshot is a failure, even when the flow reaches its last step.
- **Isolate:** always address a device by UDID or serial, never `booted` or the default `adb` target, because the user may have their own simulator open. Reset app state only on a device the run created (`xcrun simctl erase`, `adb shell pm clear <package>`, or Maestro `clearState`); never erase a device the user owns.
- **Cleanup:** stop the bundler process you started, then shut down only the devices the run booted (`xcrun simctl shutdown <udid>`, `adb -s <serial> emu kill`). Copy screenshots, recordings, and logs off the device before shutting it down.

## 3. Seed the feature map

Create `<skills dir>/verify-<app>/features/README.md` plus one file per user-facing feature you can identify (aim for the top 3-5 to start, from routes, commands, menus, or docs). Follow the shape in [`references/feature-map-example/`](references/feature-map-example/), with a README index and one file per feature. Each file answers, from the user's point of view: what the feature is, how to reach it, how to drive it with the harness, and what observable end state proves it works. The four H2s are `Sub-features`, `How to get to it (user POV)`, `Driving it with <harness>`, and `Gotchas`. If the seed data lacks a precondition that a journey needs, the journey reports a failure and the reason goes in that feature's `Gotchas`. Never weaken the journey to make it pass. The map is the repo's maintained verification source; a proof that drives one convenient entry point is incomplete when the map lists others.

## 4. Prove the generated skill before handing it over

Run its own instructions end to end once: launch, doctor, drive ONE mapped feature (one is enough; the map exists so later runs can cover the rest), capture evidence, clean up. After cleanup, confirm the evidence still exists at the named location — a cleanup that eats the proof fails this step. Fix what fails, and run the generated cleanup after every failed iteration too, so broken attempts don't strand processes and ports. A generated skill that was never executed is a draft, not a deliverable. If you proved it in a stand-in for the real target (a container in place of a fresh VM, a local stack in place of a remote one), list every setup step the stand-in did that the target will not. Those steps stay unverified until a run on the real target covers them.

## 5. Offer the maintenance loop

Tell the user the feature map must be kept honest as the app changes: update a feature file whenever its routes, commands, selectors, or end states change, and add files for new user-facing features. If a `maintain-verification-skill` skill is installed, point them at it. Suggest a cadence only if they ask.
