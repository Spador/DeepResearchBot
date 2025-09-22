Deep Research Bot

Overview

- Small, single‑purpose app that does “deep research” on a topic and emails the result.
- Orchestrates a few focused agents: plan → search → write → email.
- Simple Gradio UI; runs locally with your API keys in a .env file.

How it works

- UI: `Application/deep_research.py` starts a Gradio app. You enter a query and click Run.
- Orchestration: `Application/research_manager.py` coordinates the flow and streams status:
  - Plan searches with `PlannerAgent` (`Application/planner_agent.py`).
  - Run multiple web searches in parallel with `Search agent` (`Application/search_agent.py`).
  - Synthesize a long markdown report with `WriterAgent` (`Application/writer_agent.py`).
  - Convert the report to HTML and send one email with `Email agent` (`Application/email_agent.py`).
- Tracing: prints a link to an OpenAI trace so you can inspect the run end‑to‑end.

What you get

- A detailed, multi‑page markdown report (plus a short summary and follow‑ups).
- Minimal, readable status updates as the pipeline executes.
- Optional email delivery through SendGrid.

Requirements

- Python 3.12+
- API keys in environment or `.env` (loaded via `python-dotenv`):
  - `OPENAI_API_KEY` (required for the models in use)
  - `SENDGRID_API_KEY` (required if you want email delivery)

Install

- Using uv (preferred):
  - `uv sync`
- Using pip:
  - `python -m venv .venv && source .venv/bin/activate`
  - `pip install -r requirements.txt`

Run

- Create a `.env` in the repo root with your keys, for example:
  - `OPENAI_API_KEY=sk-...`
  - `SENDGRID_API_KEY=...`
- Start the app:
  - `python Application/deep_research.py`
- A browser window opens. Enter a topic and Run. The console will print a trace URL you can open to inspect the run.

Configuration

- Number of searches: `HOW_MANY_SEARCHES` in `Application/planner_agent.py` (default 3).
- Models: set per agent in each file (currently `gpt-4o-mini`). Change as needed.
- Email from/to: in `Application/email_agent.py`. Update the addresses to your own.
- Web search: handled by `WebSearchTool` in `Application/search_agent.py` (kept concise; summary only).

Architecture (at a glance)

- `ResearchManager.run(query)` orchestrates:
  - plan_searches → perform_searches (async, parallel) → write_report → send_email
- Agents return structured outputs using Pydantic models where appropriate (e.g., `WebSearchPlan`, `ReportData`).
- Each step is small, testable, and replaceable.

Notes

- Email is optional. If you don’t provide `SENDGRID_API_KEY`, comment out the email step or adjust the agent.
- The writer aims for a long, detailed markdown report (5–10 pages target). Expect model costs accordingly.
- Traces: the app prints an OpenAI trace link for debugging/observability.

Development

- Everything lives under `Application/` plus a quick `ResearchBotTesting.ipynb` for ad‑hoc runs.
- Dependencies are declared in `pyproject.toml`; a pinned `requirements.txt` is generated.
- If the `agents` package isn’t found at runtime, make sure you’ve installed the project (use `uv sync` or `pip install -r requirements.txt`).

Quick tasks

- Change number of searches: edit `Application/planner_agent.py`.
- Tighten/loosen search summaries: edit `Application/search_agent.py` instructions.
- Tweak report length/tone: edit `Application/writer_agent.py` instructions.
- Turn off email: comment out `send_email` call in `Application/research_manager.py`.

License

- Not specified. Use at your own discretion.
