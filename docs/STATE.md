# PROJECT STATE

> This file is the memory between chats. Chats inside the same Project do not
> share history: the only things that persist are the instructions and this
> knowledge. It is updated at the end of EVERY session.

**Last updated:** 2026-09-12
**Current phase:** 1 — Foundation
**Week:** 4 of 8 (calendar). Effective work: session 2 of the phase.
**Cumulative hours:** ~8 h

---

## 1. Where we are today

Session 2 of the **repository setup** block. Items 2, 3 and 4 are closed. Items 5
(README) and 6 (CLAUDE.md) remain.

**The project is now on GitHub.** `github.com/leonardolucci/ercot-forecast`,
public, one commit (`87ee3d8`, root commit, 20 files). Open problem 2 from the
previous session is closed: the code no longer exists on a single disk.

**The project moved.** New location:

```
C:\Users\<user>\dev\ercot-forecast
```

It was previously at `Desktop\Projects\ERCOT - Forecasting y decision\`. Moved
before `git init`, for reasons recorded in the decisions table.

State of the tree (only tracked files; `.venv/` exists on disk and is ignored):

```
ercot-forecast/
├── .gitattributes        NEW — line-ending policy
├── .gitignore            NEW
├── .python-version       NEW — "3.13"
├── pyproject.toml        NEW
├── uv.lock               NEW
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

**Environment:** uv 0.12.9, CPython 3.13.15 (downloaded and managed by uv, not
installed system-wide — there is still no system Python on the machine). The
package is installed editable; `import ercot_forecast` resolves to
`src\ercot_forecast\__init__.py`, verified.

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
| 2026-08-26 | Shell: PowerShell (not Git Bash) | Native Windows paths, VS Code integrated terminal. **Reversible at no cost** |
| 2026-08-26 | **Calibrated depth rule** | Maximum depth where a bad decision corrupts the methodology. Minimum depth where it is reversible in 5 minutes. See section 6 |
| 2026-08-27 | Working language switched to English | Deliberate English practice on top of the technical work |
| 2026-09-10 | **Dependency manager: `uv`** | Owns all four jobs — obtain interpreter, isolate, resolve, record — in one tool instead of four. Decisive factor: it manages the **interpreter** itself, so the Python version is a declared repository fact rather than a machine property. Not locked in: `pyproject.toml` is the standard format |
| 2026-09-10 | **Project moved to `C:\Users\<user>\dev\`** | `Desktop\` is one OneDrive "Manage backup" checkbox away from being synced. A sync engine underneath `.git\` causes lock contention, conflict copies inside `objects\`, and dehydrated placeholders that look like present files. Also cut the path from 81 to 37 characters and removed a space from a folder I control. Verified at the time: KFM was NOT active, `$env:OneDrive` set but syncing only `OneDrive\` |
| 2026-09-10 | `uv init --lib --vcs none --python 3.13` | `--lib` writes the `[build-system]` table; without it uv treats the project as *virtual* and never installs the package, so `src/` would silently break every import. `--vcs none` keeps `git init` and `.gitignore` as deliberate agenda items rather than a side effect. `--python 3.13` states the interpreter instead of letting uv pick |
| 2026-09-10 | `requires-python = "==3.13.*"` (not `>=3.13`) | The unbounded form propagates into `uv.lock` and forces every dependency to be valid on 3.14, 3.15 and beyond, causing silent fallback to older releases. This is an application with exactly one interpreter, not a library. 3.13 rather than 3.14 because wheel availability for the scientific stack lags the interpreter release |
| 2026-09-10 | **Commit email: GitHub noreply** (`326698764+leonardolucci@users.noreply.github.com`), set `--global` | Commit metadata is immutable and public; the address would be scraped from every commit forever. Attribution to the account is unaffected. Both GitHub email-privacy checkboxes enabled, including *Block command line pushes that expose my email* — turns a silent permanent leak into a loud rejected push |
| 2026-09-10 | `authors = [{ name = "Leonardo Lucci" }]`, no email in `pyproject.toml` | Packaging metadata is machine-readable and harvested; it is not where a human looks. The contact point goes in the README (item 5), where it stays editable forever |
| 2026-09-10 | `init.defaultBranch main` | GitHub's default. Set before `git init` to avoid renaming a branch after a remote exists |
| 2026-09-10 | **`.gitattributes` with `* text=auto eol=lf`** — not on the original agenda | `core.autocrlf` is a machine property and does not travel with the repo. Without this, a collaborator or the Linux CI runner commits CRLF blobs and every diff shows every line changed. `.gitattributes` is *tracked*, so the policy belongs to the repository. Exceptions for `*.bat` / `*.cmd`, which `cmd.exe` genuinely mis-parses with LF |
| 2026-09-10 | **Authentication: HTTPS + Git Credential Manager**, not PAT or SSH | Ships with Git for Windows, needs no setup, uses GitHub's OAuth flow in a browser and stores the token encrypted in Windows Credential Manager. No raw secret is ever handled, pasted or rotated. Worked first try |
| 2026-09-10 | GitHub repo created **empty** (no README, no .gitignore, no licence) | An initialised remote has a commit with no common ancestor with the local root commit; the first push is rejected and the usual escapes (`--allow-unrelated-histories`, force-push) teach the workflow wrong |
| 2026-09-10 | Conventional Commits (`chore:`, `feat:`, `fix:`…) + imperative mood | Free to adopt, reads as professional, and lets CI generate a changelog in Phase 2 |

---

## 3. Done

Setup agenda (6 items):

- [x] 1. Directory structure — explained, materialised on disk, verified
- [x] 2. Reproducible environment — uv, CPython 3.13.15, `.venv`, `pyproject.toml` + `uv.lock`, editable install verified
- [x] 3. `.gitignore` for a data project — written, encoding verified, behaviour verified against `git status`
- [x] 4. `git init`, first commit, GitHub repo, remote connected, **pushed**
- [ ] 5. Initial `README.md`
- [ ] 6. `CLAUDE.md` at the root with the hard rules

Phase 1 (beyond setup):

- [ ] Day-ahead price ingestion script
- [ ] Raw data on disk

---

## 4. Next concrete step

**Items 5 and 6, kept lean, then open the ingestion block.**

Item 5 — README. What a stranger needs in the first 30 seconds: what this is,
what problem it solves, how to reproduce it (`git clone` + `uv sync`, nothing
else), and one contact line. The GitHub *About* field and topics also need
filling; right now the repo page says "No description, website, or topics
provided".

Item 6 — `CLAUDE.md` at the root: the hard rules, the pipeline order, the
point-in-time discipline, the depth rule.

**Budget warning, raised under hard rule 8:** Phase 1 is 8 calendar weeks and
week 4 is starting with zero ingestion code written. Setup does not repeat, but
items 5 and 6 are documentation. If session 3 spends its full budget on prose,
that is three sessions of meta-work before a single byte of ERCOT data exists on
disk. Cap them and move on.

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

2. ~~Nothing is under version control yet.~~ **CLOSED 2026-09-10.** Commit
   `87ee3d8` pushed to `github.com/leonardolucci/ercot-forecast`.

3. ~~`docs/` has no `.gitkeep`.~~ **CLOSED.** It holds ROADMAP.md, STATE.md and
   STRUCTURE.md, so it travels to Git on its own content.

4. **Time budget.** Four calendar weeks gone, setup at 4 of 6, no pipeline code.
   See the warning in section 4.

5. **`README.md` is empty and public.** Anyone reaching the repo today sees a
   directory listing and nothing else. Closed by item 5.

---

## 6. Things tried that did NOT work

| What | Why it failed |
|---|---|
| **Uniform depth across all explanations** (Claude) | Recorded in session 1. **It recurred in session 2:** full depth was spent on Windows PATH scopes and file encodings — both reversible in minutes. Leonardo flagged the session was dragging. The rule is in section 2; applying it is the open problem |
| **A `.gitignore` decorated with box-drawing characters** (Claude) | The `# ── …` header made the file non-ASCII, so the byte-check prediction given alongside it (`35 32 32 32`) was wrong. Decoration in a file whose first bytes get verified is a defect, not a style |
| **Assuming the project path from STATE.md** (Claude) | A `cd` was issued to the path recorded in the file. The project had been moved and the file was stale. STATE.md is only as good as its last update |
| **Predicting the GitHub account already existed** (Claude) | Item 4 was planned in detail before checking that its precondition held. Cheap to verify, expensive to assume |
| **`foreach` loops in PowerShell to create the tree** | Unnecessary. `New-Item` accepts several comma-separated paths in one `-Path` |

**Traps recorded for the future:**

- **`New-Item -ItemType File -Force` on a file that already has content wipes
  it**, without asking and with no recycle bin. Safe version:
  `New-Item -ItemType File -Path path -ErrorAction SilentlyContinue`.
  On `Directory`, `-Force` is harmless (it only means "do not error if it
  exists"). Same flag, opposite consequence.
- **Never create text config files with `>` or `Out-File` on PowerShell 5.1.**
  They default to UTF-16LE with a BOM; Git reads the file as binary and not one
  pattern matches, silently. Use the editor. Verify with
  `Get-Content <file> -Encoding Byte -TotalCount 4` — `255 254` is a UTF-16 BOM.
- **VS Code's "New File" creates the file relative to the current explorer
  selection**, not the workspace root. This put a `.gitignore` inside `.venv\`
  once. Check the path before saving.
- **A new terminal starts at `$env:USERPROFILE` and discards any earlier `cd`.**
  Both `uv` and `git` find their project by walking *upward* from the current
  directory, never downward — hence `No pyproject.toml found in current
  directory or any parent directory`. Opening the folder in VS Code makes every
  integrated terminal start at the workspace root.
- **`git add .` stages relative to the current directory**, not the repository
  root. From `src\` it silently stages only `src\`. `git add -A` is
  repository-wide regardless of position.
- **Staging is a snapshot of content, not a pointer to a file.** Editing a file
  after `git add` leaves the staged version untouched; `git status` will list it
  as both staged and modified. Inspect with `git diff --cached <file>`.
- **`.gitignore` patterns are relative to the directory containing the
  `.gitignore`**, and it only applies to *untracked* files — it has no effect on
  anything already in the index, and is not retroactive. This is why it had to
  exist before the first commit.
- **`uv` does not overwrite existing source files.** `uv init` left the existing
  empty `__init__.py` alone instead of writing its template. Opposite behaviour
  to `New-Item -Force`.

---

## 7. Questions for the next session

- **Re-test with fresh cases** (answers were given, not produced — verification
  is still owed):
  - Axis **A**: `.venv/` is ignored and `uv.lock` is committed, both generated
    by a tool. The sharpened test: *is the regeneration a function of the
    repository alone, or does it depend on the outside world at the moment you
    run it?* The source/derived boundary is drawn at the last point where
    information entered from outside.
  - Axis **B**: a module that hits the network at import time is not merely
    misfiled — the design is wrong. Axis B is a design smell detector, and
    invariant 3 (the leakage test) depends on it.
  - Axis **D**: the discriminator is whether a second value is *legitimate*,
    not whether it is *possible*.
  - Why a `FileNotFoundError` on a clean CI clone is good news: a clean clone is
    the only environment with no accumulated local state, so it is the only
    honest test of reproducibility. The bad outcome is the test passing.
- Deferred until after setup, not before:
  - Schema of the raw price table
  - Storage format (partitioned Parquet + DuckDB)

---

## How to update this file

Last action of every session: ask Claude for the updated version, save it in
`docs/`, commit it, and replace the old one in the Project knowledge. Always
written in English.
