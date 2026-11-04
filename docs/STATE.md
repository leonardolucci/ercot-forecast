# PROJECT STATE

> This file is the memory between chats. Chats inside the same Project do not
> share history: the only things that persist are the instructions and this
> knowledge. It is updated at the end of EVERY session.

**Last updated:** 2026-10-27
**Current phase:** 1 — Foundation
**Week:** 10 (calendar). Phase 1 was planned as weeks 1-8; that window closed
on 2026-10-13. Effective work: 3 sessions.
**Cumulative hours:** ~9 h

---

## 1. Where we are today

Session 3 (opened 2026-09-26, closed 2026-10-27) did the carried-over axis B
re-test (outcome in section 7). It did not reach items 5 and 6, and no command
was run. Setup agenda: **4 of 6**. No project chat took place between
2026-09-26 and 2026-10-27.

**The development machine changed.** On 2026-10-21 the notebook was wiped and
now runs **Xubuntu 26.04.1 LTS** (bash). Windows 10 was erased, and with it,
unless backed up: the local clone at `C:\Users\<user>\dev\ercot-forecast`, its
`.venv`, the global Git config and the GitHub credentials stored in Windows
Credential Manager. Nothing committed was lost: GitHub holds all of it.

Not yet verified on Xubuntu: git, uv, VS Code, the clone itself. Session 4 is
the first project session on Linux.

**Repository:** `github.com/leonardolucci/ercot-forecast`, public. `main` at
`54a19fa` (`docs: record instructions recalibration in STATE.md`), the last
recorded push. **This version of STATE.md is not in the repo yet:** it gets
committed in session 4, right after the clone.

Tracked tree, as last recorded:

```
ercot-forecast/
├── .gitattributes        line-ending policy
├── .gitignore
├── .python-version       "3.13"
├── pyproject.toml
├── uv.lock
├── README.md             empty — item 5
├── config/               .gitkeep
├── docs/                 ROADMAP.md, STATE.md, STRUCTURE.md
├── notebooks/            .gitkeep
├── scripts/              .gitkeep
├── tests/                .gitkeep
└── src/
    └── ercot_forecast/   __init__.py, py.typed
        ├── ingest/       __init__.py
        ├── features/     __init__.py
        ├── models/       __init__.py
        ├── evaluation/   __init__.py
        └── decision/     __init__.py
```

**Environment, as declared in the repo:** `requires-python = "==3.13.*"` and
`.python-version` = `3.13`. uv downloads and manages the interpreter, so on a
fresh Xubuntu clone `uv sync` must fetch CPython 3.13 by itself and install
the package editable. Verified on Windows in session 2 (uv 0.12.9, CPython
3.13.15); not yet on Linux. That re-verification is also the first real test
of the README's reproduce instructions.

`data/` and `artifacts/` still do not exist, on purpose.

---

## 2. Decisions made (with date and reason)

| Date | Decision | Reason |
|---|---|---|
| 2026-08-19 | Market: ERCOT (Texas) | Open data with no API paperwork, massive wind penetration, negative prices and extreme spikes |
| 2026-08-19 | Portfolio project, not a startup | The goal is remote employment; the product branch is assessed in Phase 3 |
| 2026-08-26 | `src/` layout instead of a package at the root | With the package at the root, `import ercot_forecast` works by accident of location rather than by real installation. With `src/`, what gets tested is what gets installed |
| 2026-08-26 | Subpackages = pipeline stages | Writes hard rule 2 into the filesystem. The invariant "an import only points leftward" becomes verifiable by eye and automatable later |
| 2026-08-26 | `data/` and `artifacts/` are NOT created by hand | If they exist because of a manual act, the code will assume they are there and will fail on a clean clone. This is a reproducibility test, not an inconvenience |
| 2026-08-26 | No `utils/` and no speculative folders | `utils/` means "I did not decide where this goes" and ends up untested. Folders with no defined role lie to whoever reads the repo |
| 2026-08-26 | ROADMAP, STATE, STRUCTURE live in `docs/` | The root holds only what a tool reads by convention or what a stranger needs in the first 30 seconds |
| 2026-08-26 | **Calibrated depth rule** | Maximum depth where a bad decision corrupts the methodology. Minimum depth where it is reversible in 5 minutes. See section 6 |
| 2026-08-27 | Working language switched to English | Deliberate English practice on top of the technical work |
| 2026-09-10 | **Dependency manager: `uv`** | Owns all four jobs — obtain interpreter, isolate, resolve, record — in one tool instead of four. Decisive factor: it manages the **interpreter** itself, so the Python version is a declared repository fact rather than a machine property. Not locked in: `pyproject.toml` is the standard format |
| 2026-09-10 | **Repository location: short path, no spaces, outside any synced folder** | A sync engine underneath `.git/` causes lock contention, conflict copies inside `objects/`, and dehydrated placeholders that look like present files (on Windows, OneDrive "Manage backup" was one checkbox away). Applied on Windows as `C:\Users\<user>\dev\`, 37 characters instead of 81. The Linux path is set in session 4 |
| 2026-09-10 | `uv init --lib --vcs none --python 3.13` | `--lib` writes the `[build-system]` table; without it uv treats the project as *virtual* and never installs the package, so `src/` would silently break every import. `--vcs none` keeps `git init` and `.gitignore` as deliberate agenda items rather than a side effect. `--python 3.13` states the interpreter instead of letting uv pick |
| 2026-09-10 | `requires-python = "==3.13.*"` (not `>=3.13`) | The unbounded form propagates into `uv.lock` and forces every dependency to be valid on 3.14, 3.15 and beyond, causing silent fallback to older releases. This is an application with exactly one interpreter, not a library. 3.13 rather than 3.14 because wheel availability for the scientific stack lags the interpreter release |
| 2026-09-10 | **Commit email: GitHub noreply** (`326698764+leonardolucci@users.noreply.github.com`), set `--global` on every machine | Commit metadata is immutable and public; the address would be scraped from every commit forever. Attribution to the account is unaffected. Both GitHub email-privacy checkboxes enabled, including *Block command line pushes that expose my email* — turns a silent permanent leak into a loud rejected push |
| 2026-09-10 | `authors = [{ name = "Leonardo Lucci" }]`, no email in `pyproject.toml` | Packaging metadata is machine-readable and harvested; it is not where a human looks. The contact point goes in the README (item 5), where it stays editable forever |
| 2026-09-10 | `init.defaultBranch main` | GitHub's default. Set before `git init` to avoid renaming a branch after a remote exists |
| 2026-09-10 | **`.gitattributes` with `* text=auto eol=lf`** — not on the original agenda | `core.autocrlf` is a machine property and does not travel with the repo. Without this, a collaborator or the Linux CI runner commits CRLF blobs and every diff shows every line changed. `.gitattributes` is *tracked*, so the policy belongs to the repository. Exceptions for `*.bat` / `*.cmd`, which `cmd.exe` genuinely mis-parses with LF |
| 2026-09-10 | **Authentication: browser OAuth, never a pasted token** (on Windows: HTTPS + Git Credential Manager) | No raw secret is ever handled, pasted or rotated; the token is stored by the OS. GCM shipped with Git for Windows and worked first try. It does not ship with Git on Linux, so the Linux equivalent is chosen in session 4 under the same constraint |
| 2026-09-10 | GitHub repo created **empty** (no README, no .gitignore, no licence) | An initialised remote has a commit with no common ancestor with the local root commit; the first push is rejected and the usual escapes (`--allow-unrelated-histories`, force-push) teach the workflow wrong |
| 2026-09-10 | Conventional Commits (`chore:`, `feat:`, `fix:`…) + imperative mood | Free to adopt, reads as professional, and lets CI generate a changelog in Phase 2 |
| 2026-09-24 | **Project instructions rewritten: short by default, depth on demand** | Reading long responses was eating most of the session budget, leaving little for execution. The calibrated depth rule already existed but was one exception inside a section written for depth, so it lost. Now brevity is the default, "deeper" expands one point, and depth has three tiers (reversible / hard to reverse / methodology-critical), with check questions only in the critical tier, delivered in short rounds |
| 2026-09-26 | **Folders under `data/` and `artifacts/` are created inside the function that writes, when it writes — never at module level** | A module-level `mkdir` makes `import` change the disk in every state where the folder is missing: a fresh clone, CI, or any process whose current directory is not the repo root (the path is relative). Axis B requires an import to change nothing in every state; `exist_ok=True` makes the change idempotent, not absent |
| 2026-10-21 | **Development machine: the same notebook, now Xubuntu 26.04.1 LTS (bash); Windows 10 erased** | Decided outside the project chats; the notebook does not meet Windows 11 requirements. Consequences: Windows-only traps removed from section 6; clone, `.venv`, global Git config and credentials are rebuilt in session 4 |

---

## 3. Done

Setup agenda (6 items):

- [x] 1. Directory structure — explained, materialised on disk, verified
- [x] 2. Reproducible environment — uv, CPython 3.13, `pyproject.toml` + `uv.lock`, editable install verified on Windows (to re-verify on Xubuntu in session 4)
- [x] 3. `.gitignore` for a data project — written, encoding verified, behaviour verified against `git status`
- [x] 4. `git init`, first commit, GitHub repo, remote connected, **pushed**
- [ ] 5. Initial `README.md`
- [ ] 6. `CLAUDE.md` at the root with the hard rules

Phase 1 (beyond setup):

- [ ] Day-ahead price ingestion script
- [ ] Raw data on disk

---

## 4. Next concrete step

**Session 4: rebuild on Xubuntu, then items 5 and 6, in that order.**
Suggested caps: rebuild 45 min, items 5 and 6 together 1 h. If the rebuild
overruns, items 5 and 6 move to session 5 instead of being squeezed.

**Step 0 — rebuild, nothing more.** Each item is the minimum needed to commit
again:

- **Git identity before any commit:** `user.name` = `Leonardo Lucci`,
  `user.email` = the noreply address in section 2. Hard to reverse: commit
  metadata is public and permanent.
- **GitHub authentication from Linux.** Same constraint as on Windows:
  browser OAuth, no raw token pasted.
- **uv, then `git clone`** into a short path with no spaces (proposed:
  `~/dev/ercot-forecast`), **`uv sync`, and verify** that
  `import ercot_forecast` resolves to `src/ercot_forecast/__init__.py`.
  Confirm `main` is at `54a19fa` with `git log --oneline -1`.
- **Copy this STATE.md into `docs/`.** It becomes the first commit of the
  feature branch.

**Item 5 — README.** What a stranger needs in the first 30 seconds: what this
is, what problem it solves, how to reproduce it (`git clone` + `uv sync`, as
verified in step 0), current status stated truthfully, one contact line. Then
the GitHub *About* field and topics, both empty at the last check.

**Item 6 — `CLAUDE.md`.** Transcription, ASCII only: pipeline order,
point-in-time discipline and the `available_at` rule, import direction,
derived vs. source, what is never committed. Two precision traps to settle in
the text:

- STRUCTURE.md's flow diagram says "imports point rightward only"; the
  2026-08-26 decision says "an import only points leftward". Same rule,
  opposite meanings of *points*. State it operationally: a module may import
  from its own stage or any stage to its left, never to its right (`features`
  may import `ingest`; `ingest` may never import `features`). Fix
  STRUCTURE.md's wording in the same pull request.
- The pipeline order (data → features → baseline → model → evaluation →
  decision) and the subpackage import order (ingest → features → models →
  evaluation → decision) are different lists: baselines and models both live
  in `models/`. Do not conflate them.

**Git workflow, taught on this work:** feature branch, one atomic commit per
file (STATE.md, README.md, CLAUDE.md, STRUCTURE.md), push, pull request,
merge, delete branch, sync local `main`.

Then: the ingestion chat, cheatsheet prompt 02.

---

## 5. Open problems

1. **`data/raw/` is irreproducible but not version-controllable.** Stays out of
   Git because of its size, so it will live on a single disk with no backup.
   Likely direction: an off-machine copy plus a version-controlled manifest
   (small, text) recording what was downloaded, when, and with what hash.
   **Known trap when implementing it:** `!data/manifest.json` will not work.
   When a *directory* is excluded, Git does not descend into it, so nothing
   inside can be re-included. Needs `data/*` plus `!data/manifest.json`, or the
   manifest lives outside `data/`.
   **To be solved in week 2 of real work, not today.**

2. **Time budget.** Phase 1 was planned as 8 calendar weeks at ~10 h/week
   (~80 h). At calendar week 10 the count is ~9 h across 3 sessions, setup at
   4 of 6 and no ingestion code. There was no project chat between 2026-09-26
   and 2026-10-27; the machine migration happened in that gap. Session 4 adds
   a rebuild before items 5 and 6, so ingestion starts in session 5 at the
   earliest. The 8-week window is gone. **Decision owed:** a new Phase 1 end
   date, set from the hours actually available rather than from the
   assumption. Make it in five minutes at the start of the ingestion block,
   not before.

3. **`README.md` is empty and public.** Anyone reaching the repo today sees a
   directory listing and nothing else. Closed by item 5.

---

## 6. Things tried that did NOT work

| What | Why it failed |
|---|---|
| **Uniform depth across all explanations** (Claude) | Recorded in session 1. **It recurred in session 2:** full depth was spent on Windows PATH scopes and file encodings — both reversible in minutes. Leonardo flagged the session was dragging. **Root cause (2026-09-24):** the instructions made depth the default and the depth rule a single exception, so the rule lost. Fixed by rewriting them: short by default, depth pulled on demand |
| **A `.gitignore` decorated with box-drawing characters** (Claude) | The `# ── …` header made the file non-ASCII, so the byte-check prediction given alongside it (`35 32 32 32`) was wrong. Decoration in a file whose first bytes get verified is a defect, not a style |
| **Assuming the project path from STATE.md** (Claude) | A `cd` was issued to the path recorded in the file. The project had been moved and the file was stale. STATE.md is only as good as its last update |
| **Predicting the GitHub account already existed** (Claude) | Item 4 was planned in detail before checking that its precondition held. Cheap to verify, expensive to assume |
| **Re-test design, session 3** (Claude) | The first axis B case depended on code that does not exist yet (a feature module, a backtest, the `available_at` filter), so it tested the future, not the axis. A formal notation (import as a function on a world state) then made a concrete disk question abstract, and a "verdict" was requested without stating its form. What worked: a case built only from files on disk, two named disk states, explicit pass/fail options, and a contrast case that changes one variable (the same `mkdir` line at top level vs. inside a `def`) |

**Traps recorded for the future:**

- **Git identity is per machine.** `git config --global` writes to the user's
  home directory, not to the repository, so it does not travel with a clone.
  Set `user.name` and `user.email` on every new machine before its first
  commit; otherwise Git may build an address from `user@hostname` and push it
  into public, permanent history.
- **Python `import` executes the module, once per process.** Every top-level
  statement runs at the first import; a `def` only creates the function, and
  its body runs when called. Importing a subpackage runs the parent package's
  `__init__.py` first. That is what makes axis B mechanical: read only the
  top-level lines. *Idempotent* is not *no effect*: `mkdir(exist_ok=True)`
  still creates the folder the first time.
- **VS Code's "New File" creates the file relative to the current explorer
  selection**, not the workspace root. This put a `.gitignore` inside `.venv/`
  once. Check the path before saving.
- **A new terminal starts in the home directory and discards any earlier
  `cd`.** Both `uv` and `git` find their project by walking *upward* from the
  current directory, never downward — hence `No pyproject.toml found in
  current directory or any parent directory`. Opening the folder in VS Code
  makes every integrated terminal start at the workspace root.
- **`git add .` stages relative to the current directory**, not the repository
  root. From `src/` it silently stages only `src/`. `git add -A` is
  repository-wide regardless of position.
- **Staging is a snapshot of content, not a pointer to a file.** Editing a file
  after `git add` leaves the staged version untouched; `git status` will list it
  as both staged and modified. Inspect with `git diff --cached <file>`.
- **`.gitignore` patterns are relative to the directory containing the
  `.gitignore`**, and it only applies to *untracked* files — it has no effect on
  anything already in the index, and is not retroactive. This is why it had to
  exist before the first commit.
- **`uv` does not overwrite existing source files.** `uv init` left the existing
  empty `__init__.py` alone instead of writing its template.

---

## 7. Questions for the next session

- **Axis B, session 3 outcome.** Produced by Leonardo: the mechanism. `import`
  runs every top-level line once per process; a `def` only creates the
  function, and its body runs when called, so a module whose `mkdir` sits
  inside a `def` passes. Not produced: the verdict on the failing case. A
  module-level `mkdir` in `__init__.py` fails, because the disk today (or any
  fresh clone) is a state where the import creates a folder, and one such state
  is enough. That verdict came from Claude after a wrong answer.
  **Real re-test, at no extra cost:** in the ingestion block, Leonardo decides
  which lines of the first ingestion code go to `scripts/` and which to
  `src/ercot_forecast/ingest/`, and why.
- Deferred until after setup, not before:
  - Schema of the raw price table
  - Storage format (partitioned Parquet + DuckDB)

---

## How to update this file

Last action of every session: ask Claude for the updated version, save it in
`docs/`, commit it, and replace the old one in the Project knowledge. Always
written in English.
