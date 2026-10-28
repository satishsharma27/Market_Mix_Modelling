# Marketing Mix Modelling (MMM) — Project Guide

Industry-level MMM project for the digital marketing domain. The goal is a full pipeline that ends in a **budget recommendation**, not just a fitted model.

## Business questions
- How many sales/conversions does each channel cause (contribution, incremental ROI)?
- Where does each channel saturate (diminishing returns)?
- How long does an ad keep working (carryover / adstock)?
- How should next quarter's budget be split to maximise the KPI?

## Data design
- Granularity: **weekly**, 2–3 years (104–156 rows).
- Target (KPI): revenue / conversions / new customers.
- Paid media (spend + impressions/clicks): Google Search, Google Shopping/PMax, Meta (FB/IG), YouTube, Display/Programmatic, LinkedIn, TikTok, Affiliate.
- Owned/organic: email sends, SEO sessions, social posts.
- Controls: seasonality, holidays (Diwali, Black Friday), promotions/discounts, price, competitor activity, macro factors.
- Baseline: trend, brand search volume (Google Trends).

Data sources:
1. **Synthetic data with known true effects** (primary) — generator sets true ROI, adstock and saturation per channel so we can check the model recovers them.
2. Public sample datasets (Google Meridian, Meta Robyn, PyMC-Marketing) — secondary demo.

## Modelling approach
- Bayesian MMM: adstock (geometric/Weibull) + saturation (Hill/logistic) + trend + Fourier seasonality + controls.
- Priors from business knowledge / lift tests (calibration).
- Route: hand-coded OLS/Ridge baseline → Bayesian model in **PyMC-Marketing** → optional comparison with **Google Meridian**.

## Roadmap
| Phase | Deliverable | Est. time | Status |
|---|---|---|---|
| 0. Setup | Repo structure, env, config, Git | 1 day | todo |
| 1. Data | Synthetic generator + data validation checks | 2–3 days | todo |
| 2. EDA | Spend trends, correlations, VIF, seasonality | 2 days | todo |
| 3. Baseline model | Hand-coded adstock + saturation + OLS/Ridge | 2–3 days | todo |
| 4. Bayesian MMM | PyMC-Marketing model, priors, convergence (R-hat, ESS) | 4–5 days | todo |
| 5. Validation | Holdout (MAPE, R²), synthetic parameter recovery, posterior predictive checks | 2–3 days | todo |
| 6. Insights | Channel contribution, ROI/ROAS with uncertainty, response curves | 2 days | todo |
| 7. Budget optimisation | Optimal allocation under fixed budget, scenario planning | 2–3 days | todo |
| 8. Product layer | Streamlit dashboard + FastAPI optimiser endpoint | 3–4 days | todo |
| 9. MLOps polish | pytest, MLflow, Docker, GitHub Actions CI, business README | 2–3 days | todo |

## Repo structure (target)
```
Market_Mix_Modelling/
├── configs/            # channels, priors, date ranges (YAML)
├── data/{raw,processed,synthetic}/
├── notebooks/          # EDA and experiments only
├── src/mmm/
│   ├── data/           # generator, loaders, validation
│   ├── features/       # adstock, saturation, seasonality
│   ├── models/         # baseline.py, bayesian.py
│   ├── evaluation/     # metrics, diagnostics
│   ├── optimization/   # budget allocator
│   └── reporting/      # plots, ROI tables
├── app/                # Streamlit dashboard
├── api/                # FastAPI
├── tests/
├── Dockerfile
├── pyproject.toml
└── README.md
```

## Conventions
- Python **3.11 or 3.12** in a project virtual env (system Python is 3.14, which PyMC/Meridian may not support yet).
- Reusable logic lives in `src/mmm/`; notebooks only call into it.
- All runs are config-driven (YAML in `configs/`) with fixed random seeds.
- Report ROI as a range (credible interval), never a single number.
- Final outputs should be business-readable (e.g. "move ₹X from Display to Search for +Y% revenue").

## Industry-level checklist
- [ ] Calibration with lift-test / geo-experiment priors (simulated if needed)
- [ ] Uncertainty shown in every ROI/contribution output
- [ ] Budget recommendation as the final deliverable
- [ ] Reproducible, tracked experiments
- [ ] Limitations documented (multicollinearity, short history, how lift tests help)

## Open decisions
- Business type to simulate: e-commerce (revenue) / lead-gen B2B (leads) / mobile app (installs)
- Tool: PyMC-Marketing (recommended), Meridian, or both
