# AgentEval: Build Plan & Checklist

Repo: https://github.com/shrutisharma22066/AgentEval (pushed after every finished chunk)

A step-by-step map of everything we're building. Finished steps end with ✅.
When you come back: scroll to the first step **without** a ✅. That's where we are.

> **Current step:** M0 → 2.1 (create `pyproject.toml`)

---

## M0. Setup (make an empty, installable Python project)
Goal: `python -c "import agenteval"` works from anywhere.

**Chunk 1: folders + virtual environment**
- 1.1 Create project folder `Desktop\My projects\AgentEval` ✅
- 1.2 Create package folders `agenteval\` and `agenteval\env\` ✅
- 1.3 Add empty `__init__.py` to both package folders ✅
- 1.4 Create data/test folders `tasks\` and `tests\` (no `__init__.py`) ✅
- 1.5 Create virtual environment `.venv\` with `python -m venv .venv` ✅
- 1.6 Verify the venv: `.venv\Scripts\python.exe --version` prints Python 3.14

**Chunk 2: `pyproject.toml`**
- 2.1 Create `pyproject.toml` in the project root
- 2.2 Understand each section: `[build-system]`, `[project]`, `[tool.setuptools...]`

**Chunk 3: `.gitignore` + git + editable install**
- 3.1 Create `.gitignore` (ignore `.venv/`, `__pycache__/`, `.env`, `runs/`, etc.) ✅
- 3.2 `git init` + first commit ✅
- 3.2a Install `gh`, log in, create the GitHub repo `shrutisharma22066/AgentEval` ✅
- 3.2b Connect the remote and push (`git remote add origin ...`, `git push -u origin main`) ✅
- 3.3 Activate the venv (`.\.venv\Scripts\Activate.ps1`)
- 3.4 `pip install -e .` (editable install)
- 3.5 Done check: `python -c "import agenteval"` runs with no error
- 3.6 2 interview questions on M0
- 3.7 Create `PROGRESS.md` (what we learned, weak spots, next step)
- 3.8 Commit + push to GitHub

---

## M1. Environment database (`agenteval/env/db.py`)
Goal: a fake store "ShopDesk" in SQLite, built identically every time.
- Write the SQL schema: customers, orders, order_items, refunds, tickets
- Add CHECK constraints (e.g. status must be a known value, amounts ≥ 0)
- Seed data: 5 customers
- Seed data: ~9 orders with different statuses (pending, shipped, delivered, cancelled)
- Seed data: one prior partial refund
- Seed data: one delivered order older than 30 days (store "today" = 2026-10-10)
- Write `build_database(path)`
- Write `snapshot(path)` (every table's rows, sorted)
- Done check: build the DB, query it with a sqlite3 one-liner
- Interview questions + update PROGRESS.md + commit

## M2. MCP server (`agenteval/env/mcp_server.py`)
Goal: the agent's tools, served over MCP.
- Install `mcp`; check version with `pip show mcp` (1.x vs 2.x imports differ)
- Server reads DB path from an environment variable
- Read tool: `find_customer`
- Read tool: `get_order`
- Test the 2 read tools
- Read tools: `list_orders`, `get_refund_policy`
- Action tool: `cancel_order` (refuses shipped orders: an invariant)
- Action tool: `update_shipping_address`
- Action tool: `issue_refund` (does NOT enforce the 30-day policy: that's the agent's job)
- Action tool: `create_ticket`
- Use `ToolError` for tool failures
- Done check: tiny client script lists tools and calls `get_order`
- Interview questions + update PROGRESS.md + commit

## M3. Task format (`agenteval/tasks.py` + `tasks/*.yaml`)
Goal: tasks written in YAML, validated by Pydantic.
- Install `pydantic`, `pyyaml`
- Models: StateCheck, AnswerCheck, Checks, ToolCall, ReferenceSolution, Task
- Validator: reject a task with no checks
- `load_tasks()`
- **You write** task 1 (a lookup task)
- **You write** task 2 (an action task)
- Claude drafts tasks 3–10, you review
- Done check: all tasks load; a no-checks task raises a validation error
- Interview questions + update PROGRESS.md + commit

## M4. Verifiers (`agenteval/verifiers.py`)
Goal: decide pass/fail by inspecting the database.
- `check_state` (SQL → expected value, float tolerance)
- `check_unchanged` (snapshot diff: nothing else changed)
- `check_tools` (required / forbidden tools)
- `check_answer` (contains_all / contains_any / not_contains)
- Optional LLM judge (reported separately)
- Done check: pytest with a hand-modified DB shows one pass and one fail
- Interview questions + update PROGRESS.md + commit

## M5. Runner + oracle (`agenteval/runner.py`, `agenteval/agents.py`)
Goal: run a task end to end; the oracle passes everything.
- Temp dir → fresh DB → before-snapshot
- Start MCP server as a subprocess over stdio; list tools
- Wrapped `call_tool` that records the trajectory (handles unknown tool names)
- After-snapshot → verify
- `OracleAgent` replays the reference solution
- `run_suite` with an asyncio semaphore (limited concurrency)
- Done check: oracle passes 100% of tasks
- Interview questions + update PROGRESS.md + commit

## M6. Real LLM agent (`OpenAICompatAgent` in `agents.py`)
Goal: a real model solves tasks through tool calls.
- Install `openai`; `.env` + `.env.example` for the Groq key
- Tool-calling loop: messages + tools → tool calls → results → repeat
- `max_steps`, temperature 0, token tracking, API error handling
- Done check: one real task runs on Groq and you can read the trajectory
- Interview questions + update PROGRESS.md + commit

## M7. Metrics + reports (`agenteval/report.py`)
- `summarise()`: pass rate, pass^k, pass@k, unsafe-action rate, tool error rate, averages
- Breakdowns by category / difficulty
- Save `results.json` + simple `report.html`
- `compare()` two runs
- Interview questions + update PROGRESS.md + commit

## M8. CLI + tests (`agenteval/cli.py`, `tests/`)
- Commands: `agenteval validate`, `agenteval run ...`, `agenteval compare A B`
- Minimal `.env` loader
- Test: oracle passes everything
- Mistake tests: refund whole order, cancel another customer's order, call a non-existent tool → verifiers must catch them
- Interview questions + update PROGRESS.md + commit

## M8.5. Demo dashboard (`app/streamlit_app.py`)
- Runs list, run detail, trajectory viewer, compare page
- Reads only `demo_runs/*/results.json` (no API keys)
- Check current Hugging Face Spaces docs, then deploy
- Done check: public URL works on your phone

## M9. Experiments + write-up
- 2 models × 3 trials
- Prompt A/B (v1 vs v2 that checks ownership + reads policy first)
- Copy results to `demo_runs/`, redeploy
- README: what, design decisions, results table, demo link, how to run
- One resume bullet using real numbers

## M10. Harbor export (`agenteval/harbor_export.py`)
- Read current Harbor docs + an example task first
- Decide with you: MCP inside the container vs a small `shopdesk` CLI
- `agenteval export-harbor --out DIR`
- Done check: Harbor's oracle agent passes at least 3 exported tasks
