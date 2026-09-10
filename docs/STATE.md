# PROJECT STATE

> This file is the memory between chats. Chats inside the same Project do not
> share history: the only things that persist are the instructions and this
> knowledge. It is updated at the end of EVERY session.

**Last updated:** 2026-08-26
**Current phase:** 1 — Foundation
**Week:** 2 of 8 (calendar). Effective work: session 1 of the phase.
**Cumulative hours:** ~4 h

---

## 1. Where we are today

Session 1 of the **repository setup** block. A 6-item agenda; item 1 (directory
structure) is closed, items 2 through 6 remain.

The directory tree exists on disk, at `C:\Users\<user>\ercot-forecast`, verified
with `tree /F`. There is still **no Git initialised, no Python installed and no
GitHub repository**. None of this is under version control yet: if the folder is
deleted today, it is gone.

State of the tree:

```
ercot-forecast/
├── config/          .gitkeep
├── docs/            (empty — filled in item 5)
├── notebooks/       .gitkeep
├── scripts/         .gitkeep
├── tests/           .gitkeep
└── src/
    └── ercot_forecast/          __init__.py
        ├── ingest/              __init__.py
        ├── features/            __init__.py
        ├── models/              __init__.py
        ├── evaluation/          __init__.py
        └── decision/            __init__.py
```

`data/` and `artifacts/` **do not exist on purpose**. See the decision dated
2026-08-26.

---

## 2. Decisions made (with date and reason)

| Date | Decision | Reason |
|---|---|---|
| 2026-08-19 | Market: ERCOT (Texas) | Open data with no API paperwork, massive wind penetration, negative prices and extreme spikes |
| 2026-08-19 | Portfolio project, not a startup | The goal is remote employment; the product branch is assessed in Phase 3 |
| 2026-08-26 | `src/` layout instead of a package at the root | With the package at the root, `import ercot_forecast` works by accident of location rather than by real installation. With `src/`, what gets tested is what gets installed; a broken install fails here and not in the Phase 2 CI |
| 2026-08-26 | Subpackages = pipeline stages (`ingest → features → models → evaluation → decision`) | Writes hard rule 2 into the filesystem. The invariant "an import only points leftward" becomes verifiable by eye and automatable later |
| 2026-08-26 | `data/` and `artifacts/` are NOT created by hand | If they exist because of a manual act, the code will assume they are there and will fail on a clean clone. The ingestion code has to create them. This is a reproducibility test, not an inconvenience |
| 2026-08-26 | No `utils/` and no speculative folders (`api/`, `dashboard/`, `deploy/`) | `utils/` means "I did not decide where this goes" and ends up untested. Folders with no defined role lie to whoever reads the repo. `decision/` is there because its role is already in the ROADMAP |
| 2026-08-26 | ROADMAP, STATE and IDEAS live in `docs/`, not at the root | The root holds only what a tool reads by convention or what a stranger needs in the first 30 seconds |
| 2026-08-26 | Shell: PowerShell (not Git Bash) | Native Windows paths with no MSYS translation layer; the venv's `Activate.ps1` is a PowerShell script; it matches the VS Code integrated terminal. Git for Windows puts `git.exe` on the PATH, so Git Bash was never a requirement. **Reversible at no cost** |
| 2026-08-26 | **Calibrated depth rule** | Maximum depth where a badly made decision corrupts the methodology (temporal leakage, backtest protocol, probabilistic metrics). Minimum depth where the decision is reversible in 5 minutes (folder names, config format). See section 6 |
| 2026-08-27 | Working language switched to English (instructions, docs, sessions) | Deliberate English practice on top of the technical work. The project files were renamed accordingly: `ESTADO.md` → `STATE.md`, `ESTRUCTURA.md` → `STRUCTURE.md`. Content unchanged |

---

## 3. Done

Setup agenda (6 items):

- [x] 1. Directory structure — explained, materialised on disk, verified
- [ ] 2. Reproducible environment: dependency manager, isolation, dependency file
- [ ] 3. `.gitignore` for a data project
- [ ] 4. `git init`, first commit, GitHub repo, remote connected
- [ ] 5. Initial `README.md`
- [ ] 6. `CLAUDE.md` at the root with the hard rules

Phase 1 (beyond setup):

- [ ] Day-ahead price ingestion script
- [ ] Raw data on disk

---

## 4. Next concrete step

**Item 2: reproducible environment.** Pick a dependency manager (a single
recommendation with a reason, not a menu), install Python, create the isolated
environment and leave a version-controllable dependency file behind.

Expected snag: when activating the virtual environment, PowerShell will probably
raise an *execution policy* error — it blocks `.ps1` scripts by default. It is
fixed with one line, and what that line changes has to be explained before it is
run.

---

## 5. Open problems

1. **`data/raw/` is irreproducible but not version-controllable.** It stays out
   of Git because of its size, so it will live on a single disk, on a single
   machine, with no backup. If that disk dies in month 4, historical data is
   lost that ERCOT may no longer serve in the same shape, and the track record
   goes with it.
   Likely direction: an off-machine copy plus a version-controlled manifest file
   (small, text) recording what was downloaded, when, and with what hash — so the
   payload is not in Git but the *fact* of the download is.
   **To be solved in week 2, not today.**

2. **Nothing is under version control yet.** The tree exists only on the local
   disk. Closed by item 4 of the agenda.

3. **`docs/` has no `.gitkeep`.** As of today it would not travel to Git. It
   resolves itself in item 5, when ROADMAP and STATE move inside and the folder
   gains real content. Noted here so it is not forgotten if item 5 gets
   reordered.

4. **Time budget.** One calendar week has passed since the ROADMAP started and
   setup stands at 1 of 6. Setup is a one-off and does not repeat, but it is
   worth looking at the remaining 8 weeks of Phase 1 with that figure in view.

---

## 6. Things tried that did NOT work

| What | Why it failed |
|---|---|
| **Uniform depth across all explanations** (Claude) | "Where does each folder go" was given the same depth that point-in-time discipline deserves. Result: the session felt disproportionate for a decision that takes 20 minutes and is reversible. Leonardo flagged it explicitly. **Correction adopted: calibrated depth rule** (see section 2) |
| **Badly formulated comprehension question** (Claude) | The question asked where "the list of expected column names" goes, expecting `config/`. Applying the test itself — is there more than one legitimate value I might want to run? — the answer is no: there is exactly one valid schema at a time. It is not a parameter, it is a contract, and it belongs in `src/`. Leonardo answered correctly and the question was wrong |
| **Question about an unstated criterion** (Claude) | The logic/parameter distinction (axis D) was asked about before it had been formalised; at point 1 it was only hinted at as "run parameters" and nothing more. You cannot ask someone to apply a criterion that was never given |
| **`foreach` loops in PowerShell to create the tree** | Unnecessary. `New-Item` accepts several comma-separated paths in a single `-Path`. The looped version works but is harder to read and to reproduce alone, which was precisely the goal |

**PowerShell trap recorded for the future:**
`New-Item -ItemType File -Force` on a file that **already has content wipes it**,
without asking and without a recycle bin. Today it was harmless because the
`__init__.py` files were empty. Once they hold code, re-running that command
destroys work. Safe version: `New-Item -ItemType File -Path path
-ErrorAction SilentlyContinue`. (On `Directory`, `-Force` is harmless.)

---

## 7. Questions for the next session

- **Item 2 (immediate):** which dependency manager, and why that one and not
  another.
- Conceptual errors still to consolidate, to be re-checked later with fresh
  cases:
  - Axis **library vs. entry point** (`src/` vs. `scripts/`). It was inverted on
    the first round and correct on the second; one more case is worth doing to
    confirm it stuck.
  - Axis **logic vs. parameter** (`src/` vs. `config/`). Explained but not
    verified with an answer of his own.
  - Why a `FileNotFoundError` on a clean CI clone is **good news** and not a
    problem with the CI.
- Deferred until after setup, not before:
  - Schema of the raw price table
  - Storage format (partitioned Parquet + DuckDB)

---

## How to update this file

Last action of every session: ask Claude for the updated version, save it in the
repo, and replace the old one in the Project knowledge. Always written in
English.
