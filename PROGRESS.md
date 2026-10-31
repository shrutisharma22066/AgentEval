# AgentEval: Progress Log

Updated at the end of every milestone. Start each new session by reading this,
then answer 2 quick recap questions on the last milestone.

---

## M0. Setup ✅ (finished 2026-10-31)

### What we built
- Project folder `Desktop\My projects\AgentEval`, pushed to
  https://github.com/shrutisharma22066/AgentEval
- Package layout: `agenteval/` and `agenteval/env/` (each with an empty `__init__.py`),
  plus `tasks/` and `tests/`
- `.venv/`, a virtual environment using Python 3.14
- `pyproject.toml`, which declares the `agenteval` package (setuptools, Python >= 3.10, no dependencies yet)
- `.gitignore`, which excludes `.venv/`, `__pycache__/`, `.env`, `runs/` and build files
- Editable install: `pip install -e .`, so `import agenteval` works from any folder

### Key concepts learned
- **Virtual environment:** a private Python + `site-packages` per project. It keeps
  library versions from clashing between projects (e.g. pydantic v1 vs v2) and makes
  the setup reproducible.
- **Why `.venv` isn't committed:** it's machine-specific (OS binaries, paths to this
  laptop), large, and rebuildable. We commit the *recipe* (`pyproject.toml`) instead.
- **`__init__.py`:** marks a folder as an importable package (one per package folder,
  so `agenteval` and `agenteval.env` each have one). It also *runs on import*,
  which is why we keep it empty.
- **`pyproject.toml` / TOML:** the project's config "ID card". TOML = `[sections]` +
  `key = value`. `requires-python` makes pip fail fast on an unsupported Python.
- **Editable install (`-e`):** installs a *pointer* to the project folder instead of a
  copy, so code edits are live without reinstalling. Proven by experiment: a print
  added to `__init__.py` showed up with no reinstall.
- **Git:** working folder → `git add` (staging) → `git commit` (saved locally) →
  `git push` (uploaded to GitHub). `main` is a branch that exists both locally and
  on GitHub (`origin/main`). `git restore <file>` discards uncommitted changes
  permanently. Git doesn't track empty folders.
- **`.env` / secrets:** API keys live in `.env`, which is git-ignored. A leaked key in a public
  repo gets abused within minutes and stays in git history, so the fix is to revoke the key.
- **Terminal:** `cd` = change directory, `..` = parent folder. The `PS C:\...>` part is
  the prompt; never paste it as a command. `&` runs a program by its path in PowerShell.

### Weak spots (revisit these)
- Mixed up **staging vs committing** (staging is `git add`; commit saves the snapshot).
- Thought `main` meant GitHub. It's a *branch*; GitHub's copy is `origin/main`.
- Thought a commit survives a dead laptop. It doesn't: only a **push** protects
  against losing the machine.
- Didn't know **`pip install .` vs `pip install -e .`** at first (copy vs pointer).
- First guess for the venv was "it holds personal details / API keys". It's actually about
  machine-specific builds; keys go in `.env`.

### Next step
**M1: the environment database** (`agenteval/env/db.py`). Start with the SQL schema
for the ShopDesk store (customers, orders, order_items, refunds, tickets) and its
CHECK constraints.
