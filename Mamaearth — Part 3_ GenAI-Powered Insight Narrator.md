# Mamaearth — Part 3: GenAI-Powered Insight Narrator

This folder extends the verified Mamaearth Part 1 SQL and Part 2 Python/Pandas work. It generates a structured `narrator/findings.json` from the Part 2 dataframe, then turns those verified findings into a three-part Situation–Complication–Resolution (SCR) business narrative.

The online path uses the `google-genai` client and a free Google AI Studio Gemini API key. The offline path requires no API key, no network access, and no paid quota; it is the default when no key is configured or when the API call fails.

## Repository layout

```text
sql/                         # existing Part 1 files in the combined repo
  schema.sql
  seed_data.sql
  reports.sql
analysis/
  clean_and_eda.py           # existing Part 2 pipeline; now exports findings.json
  visualize.py
narrator/
  export_findings.py         # derives findings from the cleaned dataframe
  findings.json              # generated; never hand-edit
  generate_narrative.py      # Gemini + deterministic offline SCR paths
  sample_output.txt          # saved offline sample and checker results
requirements-part3.txt
README.md
```

## Reproduce the project from the top

Run these commands from the root of the combined GitHub repository. Keep the existing SQL files from Part 1 and the CSV files from Part 2; this Part 3 folder does not replace them.

### 1. Load and run the SQL work

Use SQLite (or the SQL engine specified by the course) to execute the Part 1 files in this order:

```bash
sqlite3 mamaearth.db < sql/schema.sql
sqlite3 mamaearth.db < sql/seed_data.sql
sqlite3 mamaearth.db < sql/reports.sql
```

If the SQL files already create/use a different database filename, follow that filename while preserving the order: schema, seed data, then reports. The SQL reports establish the raw revenue reference of INR 99,860.20 used by Part 2.

### 2. Run Part 2 and export verified findings

Install dependencies and run the cleaning/EDA pipeline first:

```bash
python -m pip install -r requirements.txt
python -m pip install -r requirements-part3.txt
python analysis/clean_and_eda.py
python analysis/visualize.py
```

At the end of `analysis/clean_and_eda.py`, the pipeline calls `narrator/export_findings.py` and writes `narrator/findings.json`. The JSON is calculated from the cleaned dataframe, not hand-typed, so it stays aligned with the Part 2 checks. The resulting values are the verified figures required by the Part 3 brief.

To regenerate only the JSON after the Part 2 code has already run:

```bash
python narrator/export_findings.py
```

### 3. Run the Part 3 narrator with a Gemini key

Google AI Studio provides a free Gemini API key and free usage tier. Set the key as an environment variable for the current terminal session; do not commit the key to GitHub.

Linux/macOS:

```bash
export GEMINI_API_KEY="your_google_ai_studio_key"
python narrator/generate_narrative.py
```

Windows PowerShell:

```powershell
$env:GEMINI_API_KEY = "your_google_ai_studio_key"
python narrator/generate_narrative.py
```

The code also accepts `GOOGLE_API_KEY`. The request uses temperature `0.0`, an explicit maximum of 500 output tokens, and a 30-second HTTP timeout. The system instruction fixes the analyst role, SCR structure, and no-invented-statistics rule. The user prompt is built from the loaded findings dictionary.

### 4. Run fully offline with no key

Unset both key variables, or simply run in a fresh terminal with no key configured:

```bash
unset GEMINI_API_KEY GOOGLE_API_KEY
python narrator/generate_narrative.py
```

This invokes `generate_scr_narrative_offline(findings)`, a deterministic template that uses the loaded `findings.json`. If a configured Gemini request returns an error, the public function automatically uses the same offline fallback and returns a structured success result rather than exposing a raw exception.

## Numeric accuracy check

`generate_narrative.py` checks the narrative after generation. It prints a pass/fail line for all required figures: cleaned revenue `97,358.30`, COD return rate `44.4`, COD + Tier-2 risk rate `54.5`, duplicate reconciliation delta `2,501.90`, and March with revenue `20,318.90`.

`narrator/sample_output.txt` is a saved keyless sample and checker result. It is included so grading does not depend on a live API request or a network connection.

## Important GitHub safety notes

Do not commit a real Gemini API key. Use an environment variable locally and add any local `.env` file to `.gitignore`. Commit the Python files, generated `findings.json`, sample output, requirements, and README. Keep the SQL files and Part 2 CSV data in their existing locations when merging this folder into the main capstone repository.
