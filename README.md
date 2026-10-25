# Sentinel

**An early-warning system for regional industrial crises, built by a reporter.**

Sentinel reads a region's public economic data every month and estimates the probability of a serious manufacturing downturn in the next six months. When the risk rises, AI agents investigate why, verify every claim against its source, and write a short brief for local decision-makers.

The first target is manufacturing in Gyeongsangnam-do, South Korea, home to the shipbuilding cluster that collapsed in 2016.

> Status: hackathon project (Meta Global AI Developer Hackathon, Oct 26 – Nov 4, 2026). Work in progress.

---

## Why

When shipbuilding orders collapsed in 2016, subcontractors in Geoje and Tongyeong closed one after another. By 2018, Geoje, Tongyeong-Goseong, and Changwon Jinhae had been officially designated employment and industrial crisis areas.

The warning signs were in public statistics long before that:

- shipments falling faster than production
- inventories piling up
- employment-insurance enrollment slowing, then shrinking

All of that data was public. Nobody put it together in time. Sentinel is built to do exactly that.

---

## How it works

```
Public data (with publication dates)
        │
        ▼
Statistical risk model ──► risk % + feature contributions
        │
        ▼
Investigator agent ──► indicators, filings, reports, historical analogs
        │
        ▼
Verifier agent ──► every sentence tagged OBSERVED / FORECAST / INTERPRETATION,
        │            and any claim without a source is sent back
        ▼
Planner agent ──► brief: what is happening, how sure we are,
                  what to watch, possible responses (JSON-schema validated)
```

**Separation of jobs.** A backtested statistical model produces the risk number. The language model never produces it. Its job is to read, cross-check, explain, and plan.

**Time-travel replay.** Pick a past date, for example March 2016. Sentinel runs the full pipeline using only the data and documents that had been published by that day, then shows what actually happened next. The warnings, and the failures, can be checked by anyone.

---

## Data

| Source | What |
|---|---|
| Statistics Korea (KOSIS) | Manufacturing production, shipment, and inventory indices by province |
| Ministry of Employment and Labor | Employment-insurance enrollment and unemployment-benefit claims |
| Korea Customs Service / KITA | Exports by province |
| Bank of Korea (ECOS) | Regional business sentiment |
| OpenDART | Corporate filings from major regional employers |
| BIG KINDS | Date-stamped news archive for replay |

Every value is stored with its **publication date**, not only the month it describes.

**Known limitation:** some sources provide only revised figures. Where first-release vintages are unavailable, official release lags are applied to the revised data, and this is flagged in the output.

---

## Model

- **Target:** the province's manufacturing production index (3-month average) falls more than 5% year-over-year at any point in the next 6 months. An employment-insurance-based target is tested as a second definition.
- **Panel:** 17 provinces, 2010–2025, about 3,000 province-months.
- **Models:** threshold rule (the baseline to beat) → regularized logistic regression → gradient-boosted trees, with calibrated probabilities.
- **Validation:** expanding-window backtests plus leave-one-crisis-out tests. Reported metrics: PR-AUC, Brier score, recall at a fixed false-alarm rate, and average months of warning.

---

## Stack

- **Frontend:** Next.js, TypeScript, Vercel
- **Data and modeling:** Python, FastAPI, pandas, scikit-learn, DuckDB
- **AI:** Meta Model API (Muse Spark: tool calling, search grounding, document understanding), Muse Code

---

## 10-day plan

| Days | Work |
|---|---|
| 1–2 | Data connectors with publication dates; clean province-month panel |
| 3–4 | Target, three models, calibration, backtest and leave-one-crisis-out |
| 5–7 | Investigator, Verifier, and Planner agents; claim tagging and citations; dated document set |
| 8–9 | Interface (risk timeline, evidence panel, action brief); full replay of the 2016 downturn |
| 10 | Evaluation report, demo video, README |

---

## Beyond manufacturing

Nothing in the core loop is specific to shipyards: dated public data, a backtested risk model, an evidence-gathering agent with a verifier, and a brief a person signs off on. Pointing Sentinel at a new problem means writing new connectors and defining the event. Next candidates:

- other industrial cities hit by similar shocks (Ulsan, Gunsan, Yeosu)
- population decline and youth out-migration
- small-business closures in old town centers

Sentinel is the first reasoning layer for **[GN Index Lab](https://gnindexlab.com)**, a live regional data platform for Gyeongsangnam-do.

---

## Author

Youngsu Park, reporter for 26 years and builder of GN Index Lab.
Contact: contact@gnindexlab.com
