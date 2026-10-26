# Example hand-off

The change: an exercise library list on a mobile app. Rows got plain pressed feedback instead of a scale animation, the search index is now built once, and the typo check stops early. Uncommitted, on a feature branch.

---

The app is running on simulator **`verify-20261025-153201`** (iPhone 17 Pro, iOS 26.2, Debug). It's signed in and on Home. The bundler on port 8091 serves your uncommitted changes, and saving a file reloads the app.

- Signed out? `verify-20261025-153201@example.test` / `<generated password>`
- Open a deep link: `xcrun simctl openurl <udid> "<link>"`

**1. Row press feedback**
- `app://exercises`: press and hold a row. **Expect:** it dims; it no longer shrinks.
- Press, drag a little, release. **Expect:** the row still opens.
- Tap ☆ on a row. **Expect:** it toggles without opening the row.

**2. Search (should be unchanged)**
- Type `bench`. **Expect:** `113 results`, same top rows as on main.
- `bnech` → Bench Press first. `rwo` → no results.

**3. Favourites and recently used**
- Star an exercise, then turn on the Favourites chip. **Expect:** only starred exercises.

**4. Scroll smoothness (invisible change)**
- `app://ui-audit?surface=library&dataset=many`: fling fast. Open the perf overlay with ⌃⌘Z → Perf Monitor.
- Debug numbers are inflated. Compare only against main on the same setup.
- Optional measurement: `scripts/perf.sh library-scroll.yaml lib --js`, baseline in `.verify/<baseline-run>/`.

**Done?** Run `scripts/cleanup.sh`. It stops only the bundler, API, database and simulator this run started.
