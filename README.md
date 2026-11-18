# GridMind FI — Intelligent Energy & Industrial Ops Copilot

Building AI course project

## Summary

GridMind FI predicts 24-hour Finnish electricity price spikes and grid load using Fingrid and FMI open data, pairing machine learning forecasts with a RAG copilot that recommends safe industrial load-shedding actions from equipment manuals.

---

## Background

Electricity spot prices and grid load in Finland fluctuate rapidly due to weather shifts, wind generation changes, and seasonal heating demand. For industrial facilities and automated plants, running heavy machinery during extreme price peaks increases operational costs and strains the power grid.

This project solves two connected problems:
- **Short-term forecasting:** Predicting next-24h grid load and electricity price spikes using historical consumption, generation, and weather data.
- **Actionable engineering guidance:** Translating raw forecasts into concrete, safe operational decisions by retrieving load-shedding procedures directly from technical equipment manuals.

---

## How Is It Used?

Plant operators, energy managers, and automation engineers interact with GridMind FI through a REST API or web dashboard:
1. **Forecast View:** Displays predicted 24-hour electricity demand and high-price windows.
2. **Operations Copilot:** An engineer asks a question in plain English—for example: *"Prices spike between 17:00 and 20:00 tomorrow. Which non-critical loads can we shed safely according to the VFD manual?"*
3. **Cited Recommendation:** The copilot combines the ML forecast with retrieved sections from technical manuals and outputs step-by-step actions with exact document citations.

---

## Data Sources and AI Methods

### Data Sources
- **Fingrid Open Data API (`CC BY 4.0`):** Historical and near real-time Finnish electricity consumption, wind/solar production, and grid system data via [data.fingrid.fi](https://data.fingrid.fi/en).
- **Finnish Meteorological Institute (FMI) Open Data (`CC BY 4.0`):** Temperature and wind speed observations/forecasts via [en.ilmatieteenlaitos.fi/open-data](https://en.ilmatieteenlaitos.fi/open-data).
- **Technical Equipment Manuals:** Openly available industrial VFD and automation manuals indexed for retrieval-augmented generation (RAG).

### System Architecture & AI Methods

| Module | AI / Engineering Technique | Purpose |
| :--- | :--- | :--- |
| **Data Pipeline** | Python, REST APIs, SQLite/PostgreSQL | Automated ingestion and cleaning of time-series grid and weather data |
| **Load & Price Forecaster** | Gradient Boosted Trees (`XGBoost` / `LightGBM`), Cross-Validation | Predicts 24-hour grid load and price spike probability |
| **Technical Manual RAG** | Dense Embeddings, `FAISS` Vector Search, LLM Prompting | Retrieves relevant safety and load-shedding rules with citations |
| **Deployment** | `FastAPI`, `Docker` | Serves predictions and copilot responses as a containerized service |

---

## Project Structure

```text
gridmind-fi/
├── data/               # Raw and processed time-series datasets (ignored in Git)
├── docs/manuals/       # Technical PDF manuals for the RAG copilot
├── src/
│   ├── ingestion/      # Fingrid & FMI API data fetchers
│   ├── models/         # ML feature engineering, training, and evaluation
│   ├── rag/            # Document chunking, vector store, and Q&A copilot
│   └── api/            # FastAPI endpoints
├── tests/              # Unit and pipeline validation tests
├── .env.example        # Environment variable template
├── requirements.txt    # Python dependencies
└── README.md

---

## Challenges

- **Sudden Grid Outages:** Unplanned power plant trips or cross-border transmission faults cannot be predicted purely from historical weather and load data.
- **Automation Safety Limits:** The AI copilot serves strictly as an advisory tool for engineers; physical PLC/SCADA load-shedding actions must always remain under human approval and hardware safety interlocks.
- **Hallucination Risk:** Mitigated by instructing the RAG copilot to answer only from indexed equipment manuals with source citations.

---

## What Next?

- **Phase 1 (Current):** Repository setup, Fingrid/FMI API ingestion pipeline, and baseline regression forecaster.
- **Phase 2:** Add `FAISS`-based RAG copilot over VFD/automation manuals with automated citation checks.
- **Phase 3:** Package the `FastAPI` backend and dashboard in `Docker` for one-command deployment.

---

## Acknowledgments

- **Electricity Grid Data:** Fingrid Oyj / [data.fingrid.fi](https://data.fingrid.fi/en), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- **Weather Data:** Finnish Meteorological Institute (FMI) Open Data / [en.ilmatieteenlaitos.fi/open-data](https://en.ilmatieteenlaitos.fi/open-data), licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- Developed as the final project for the **Building AI** course by the University of Helsinki and Reaktor.
