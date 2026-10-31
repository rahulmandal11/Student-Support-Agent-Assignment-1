# Student Support Agent — Assignment 1

A Flask-based student-support assistant with tool calling, handbook retrieval, student-specific demo records, support requests, and saved preferences. Bundled records are synthetic; use this project only with fictional data.

## Assignment 1 scope

This repository runs the combined project's **Assignment 1 capability profile**. The profile keeps the Assignment 1 student-support tools and excludes the Assignment 2 programme-change tool. The source project did not contain a separate historical pre-Assignment 2 tree, so this is a clean, independently runnable extraction of the Assignment 1 profile—not a claim to be a byte-for-byte copy of an earlier snapshot.

## Requirements

- Python 3.11 or newer
- A Groq API key for live AI responses (optional for the UI and offline tests)

## Setup

Create and activate a virtual environment, then install the project dependencies:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements-dev.txt
Copy-Item .env.example .env
```

Edit `.env` locally:

- Set `DEMO_ACCOUNT_PASSWORD` to a unique password of at least 12 characters. It is shared only by the synthetic local demo accounts and is never included in this repository.
- Set `GROQ_API_KEY` to enable live model responses. Without it, the application and offline tests still run, but chat reports that the provider is not configured.
- `SECRET_KEY` can remain empty; the app generates an ignored local key on first run.
- Leave `ASSIGNMENT_PROFILE=a1`.

The demo account IDs are `STU001`, `STU002`, `STF001`, and `ADM001`. Use the password you set in your local `.env`. Never use real student records or credentials.

## Run

```powershell
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000). The development server binds to loopback only.

## Verify

```powershell
python -m pytest -q
python scripts/evaluate.py
```

The offline evaluator uses scripted model responses and makes no external API calls. Live evaluation is optional and requires a configured Groq key:

```powershell
python scripts/evaluate.py --live
```

## Project layout

```text
app.py
student_support/   Flask app, agent, tools, database, UI
data/              Synthetic student/staff records and handbook
scripts/           Offline/live evaluation runner
tests/             Automated tests
```

Local configuration, generated databases, session keys, caches, and evaluation output are excluded by `.gitignore`.
