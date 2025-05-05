# Notion → AI → Google Sheets Automation Pipeline

> End-to-end Python workflow that turns a **Completed** Due-Diligence Questionnaire (DDQ) in Notion into a fully-scored entry in our research Google Sheet – no servers, no databases, just APIs and scheduled jobs.

---

## ✨ What you get

| Step | Output |
|------|--------|
| 1. Watcher | Detects cards in a Notion CRM whose **Completed** checkbox flipped to ☑️ in DDQs |
| 2. Deep Research | Institutional-grade Markdown "Deep" Research Report written by an LLM (define the depth of the web research in `src/research.py`)|
| 3. Report Writer | Child page **"AI Deep Research Report"** created under the relative Notion card |
| 4. Scoring Agent *(WIP)* | Strict JSON with 20+ scoring columns |
| 5. Sheet Pusher *(WIP)* | Values & rationales filled into our Google Sheet |

All activity is logged to `logs/` so the whole flow is fully auditable.

---

## 🗺️ Repository Layout

```text
.
├── src/                 # All runtime code
│   ├── watcher.py       # 1️⃣ Poll Notion for completed DDQs
│   ├── research.py      # 2️⃣ Deep-research wrapper around `deep-research-py`
│   ├── writer.py        # 3️⃣ Convert Markdown → Notion blocks & publish
│   ├── scorer.py        # 4️⃣ Score DDQ + report (TBD)
│   └── pusher.py        # 5️⃣ Write scores to Google Sheets (TBD)
├── main.py              # Orchestrator (weekly cron)
├── requirements.txt     # Python deps
└── tests/               # Pytest suite
```

---

## 🚀 Quick Start

1. **Clone & enter** the repo
   ```bash
   git clone https://github.com/Liscivia/AI_intern.git
   cd AI_intern
   ```
2. **Create a virtual-env** (Python ≥3.11)
   ```bash
   python -m venv .venv
   source .venv/bin/activate   # Windows: .venv\Scripts\activate
   ```
3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   playwright install  # only once, downloads browser binaries
   ```
4. **Configure secrets** – copy the template below into `.env` and fill the values:
   ```bash
   cp .env.example .env  # if the file exists; otherwise create it manually
   ```
   ```dotenv
   # .env
   NOTION_TOKEN=secret_xxxx
   NOTION_DB_ID=xxx
   OPENAI_API_KEY=sk-...
   OPENAI_MODEL=o3
   FIRECRAWL_KEY=fc_... # advised to utilize Firecrawl to prod
   GOOGLE_SERVICE_JSON=/absolute/path/to/google-service.json
   ```
5. **Run the pipeline once** (with tests, until `main.py` is implemented):

   `pytest tests/test_multiple.py -rs -s` to produce all Deep Research Reports for all projects with completed DDQs within the previous week in the Notion db, and publish them in their cards.
   `pytest tests/test_writer.py -rs -s` to produce a single Deep Research Report for the last completed DDQ in the Notion db and publish it on the card.
   `tests/test_research.py -s` to produce a single Deep Research Report for the last completed DDQ in the Notion db and be able to read it in `reports/`
   ...

---

## 🗓️ Scheduled Execution

Add a weekly cron (or GitHub Actions, Cloud Run, etc.) that simply calls:

---

## 📜 Logging

| File | Purpose |
|------|---------|
| `logs/watcher.log` | Notion polling (step 1) |
| `logs/research.log` | Deep-research orchestration |
| `logs/writer.log` | Markdown → Notion publishing |

Each entry is a single `key=value` line so you can `grep` it easily.

---

## 🪄 Common Issues

| Symptom | Fix |
|---------|-----|
| `RuntimeError: Environment variable NOTION_TOKEN is required.` | Ensure `.env` is loaded (`source .venv/bin/activate` then `pip install python-dotenv` or export vars in your shell). |
| Browser launch fails in CI | Use Playwright's `--browser chromium --with-deps` docker image or run `playwright install chromium` during build. |
| `firecrawl` 429 errors | The library is set as default scraper; tune the rate-limit envs or switch to Playwright by un-commenting the fallback in `src/research.py`. |

---


