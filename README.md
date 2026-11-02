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
## Verify

```powershell
python -m pytest -q
python scripts/evaluate.py
```

The offline evaluator uses scripted model responses and makes no external API calls. Live evaluation is optional and requires a configured Groq key:

```powershell
python scripts/evaluate.py --live
```

## Evaluation

Latest recorded offline run: **10/10 scenarios passed** for the Assignment 1 profile. The evaluator
uses deterministic scripted responses and a temporary SQLite database for each scenario. It checks
the selected tools and observable application/database effects; it does not call Groq or evaluate
live language-model answer quality.

| Metric | Assignment 1 |
|---|---:|
| Common scenarios passed | 10/10 |
| Mean offline orchestration latency in the recorded run | 6.60 ms |

The scripted provider makes no model calls, so model-token usage is not collected for this run.
The latency figure measures local orchestration and tool/database work only; it is not Groq response
latency and may vary by machine. Run `python scripts/evaluate.py` to reproduce the profile comparison.

### Assignment 1 scenario coverage

| Scenario | Automated behavior checked |
|---|---|
| Greeting | Replies without calling a private-record tool. |
| Own fee status | Uses the fee tool and reports the expected outstanding balance. |
| Own scholarship status | Uses the scholarship tool and reports the expected award/status. |
| Payment portal policy | Searches the handbook and retrieves the matching `PAYMENT-02` evidence. |
| Save a preference | Saves the explicitly requested allowed programme preference. |
| Recall a preference | Selects the saved preference in a later-session evaluation case. |
| Temporary information | Does not save a fact explicitly marked temporary. |
| Policy not in handbook | Does not return unsupported handbook evidence. |
| Create a support request | Creates one request owned by the signed-in student. |
| Missing request details | Asks for missing information without creating an invented request. |

These are deterministic orchestration checks, not a substitute for reviewing answers from a live
provider against the assignment rubric.

### Previously recorded live Groq run

The project also contains measurements from a **23 October 2026** live run of the combined source
project using Groq-hosted `openai/gpt-oss-20b`. These are historical project measurements, not a
new live run of this split repository.

| Profile and scenario group | Scenarios | Automated checks | Mean agent latency | API input tokens | API output tokens |
|---|---:|---:|---:|---:|---:|
| Assignment 1 common cases | 10 | 9/10 | 1,537.19 ms | 14,709 | 1,529 |

The live run's one automated failure was the preference-recall case: the model returned the
remembered programme but also made an unnecessary student-record lookup. The answer passed the
project's assistant rubric review, but the stricter tool-selection check correctly failed. The
answer review was performed by a coding assistant, not an independent human reviewer; official
human accuracy review remains pending. The table reports provider-recorded token usage for this
live run only.

## Project layout

```text
app.py
student_support/   Flask app, agent, tools, database, UI
data/              Synthetic student/staff records and handbook
scripts/           Offline/live evaluation runner
tests/             Automated tests
```

Local configuration, generated databases, session keys, caches, and evaluation output are excluded by `.gitignore`.
