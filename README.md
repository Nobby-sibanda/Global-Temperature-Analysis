# 🌍 Global Temperature Analysis

**Author:** Nobukhosi Sibanda  
**Degree:** MSc Data Science (May 2026) | BSc Finance | Quantitative Analyst Student  

A comprehensive data science analysis of Earth's surface temperature record spanning **144 years (1880–2024)**, combining four independent datasets, full exploratory data analysis, and seven machine learning / statistical algorithms.

The notebook has been fully executed end-to-end (all 36 code cells produce real output — charts, tables, and printed statistics — viewable directly on GitHub without running anything yourself) and reviewed for correctness: see [Methodology notes & fixes](#methodology-notes--fixes) below for what changed and why.

---

## Data Sources

| Source | What it provides | Coverage |
|--------|-----------------|----------|
| **NASA GISTEMP v4** | Global & zonal land-ocean temperature anomalies | 1880–2024 |
| **Berkeley Earth** | Independent monthly global land+ocean reconstruction | 1850–2024 |
| **NOAA ESRL (Mauna Loa)** | Atmospheric CO₂ annual means | 1959–2024 |
| **Our World in Data / OWID** | Country-level GHG temperature forcing | 1990–2022 |

All datasets are downloaded directly at notebook run-time — no manual downloads required. NASA's GISTEMP endpoint is occasionally unreachable from some networks (sandboxed CI runners, restrictive firewalls, or NASA-side downtime); if so, the notebook automatically falls back to a fixed, dated third-party snapshot and prints a warning rather than crashing. When the fallback is used, GISTEMP-derived figures reflect that snapshot's date, not the live present day — see the fetch cell's output for whether it was used on any given run.

---

## Notebook Sections

| # | Section | Techniques |
|---|---------|-----------|
| 1 | **Setup & Data Loading** | Multi-source ingestion, cleaning, merging |
| 2 | **Global Trend EDA** | Anomaly bar charts, rolling means, era-by-era warming rates |
| 3 | **Monthly Heatmap & Seasonality** | Year×month heatmaps, seasonal decomposition (additive) |
| 4 | **Decadal & Distribution Analysis** | Decadal averages, KDE distribution shift across eras |
| 5 | **Hemispheric & Zonal Analysis** | NH vs SH, latitude-band warming heatmap |
| 6 | **CO₂–Temperature Correlation** | Dual-axis trend, scatter regression, year-on-year changes |
| 7 | **World Temperature Map** | Choropleth (static Matplotlib + interactive Plotly), Arctic amplification |
| 8 | **Multi-Source Comparison** | GISTEMP vs Berkeley Earth validation |
| 9 | **ML Regression** | Linear, Polynomial (deg 2 & 3), Random Forest with time-series CV |
| 10 | **Forecasting — ARIMA & Prophet** | ADF stationarity test, AIC-selected ARIMA order, Facebook Prophet |
| 11 | **Clustering & PCA** | K-Means (k=4) climate regimes, PCA on zonal profiles |
| 12 | **Anomaly Detection** | Changepoint detection (PELT), Isolation Forest |
| 13 | **Key Findings** | Summary table of all major results |
| 14 | **Interactive Dashboard** | Standalone HTML export: KPI cards + 6 interactive Plotly panels |

---

## Key Findings

These are the **actual verified numbers from a real, fully-executed run** of the notebook (previous versions of this table were written before the notebook had ever been run end-to-end — see [Methodology notes & fixes](#methodology-notes--fixes)). Exact figures will shift slightly run-to-run as NASA/NOAA update their series, and further if the GISTEMP fallback mirror is used instead of the live source (see [Data Sources](#data-sources)):

| Metric | Value |
|--------|-------|
| Total warming (1880 → last complete year) | **+1.11 °C** |
| Linear warming rate, full record | **+0.078 °C/decade** |
| 21st-century warming rate (2000 → present) | **+0.218 °C/decade** — roughly 3× the full-record rate |
| CO₂ rise (1959 → 2025) | **316.0 → 427.4 ppm (+111.4 ppm, +35%)** |
| CO₂–Temperature correlation | **r > 0.95** (p < 0.001) |
| NH vs. SH warming (recent decade) | **NH warms ~1.8×** as fast as SH |
| Arctic amplification (general climate-science finding, not this notebook's own metric) | Polar regions warm **2–4× the global average** |
| Record warmest year | **2016 (+1.02 °C)** in this run |
| Anomalous years (Isolation Forest) | **1904, 1909, 1917** (early-record outliers) **+ 2015–2022** (recent cluster) — not a single set of El Niño peaks |
| Structural breaks (PELT changepoint) | **1935, 1980, 2000, 2015** — multiple regime shifts, not one single break |
| ARIMA forecast 2035 (AIC-selected order, not a fixed (2,1,2)) | **+1.05 °C** above 1951–1980 baseline |

*(This particular run used the GISTEMP fallback mirror — see [Data Sources](#data-sources) — whose snapshot's last complete year is 2022; a run that reaches the live NASA source directly will show later years and correspondingly different totals/record year. The pipeline and methodology are what's being verified here, not one fixed vintage of the data.)*

---

## Methodology notes & fixes

This notebook was reviewed and corrected in six areas (in priority order of what was wrong):

1. **The notebook had never been executed.** Every code cell showed only source, no output — the Key Findings table above was apparently written without ever running the analysis (several of its numbers didn't match what the code actually produces, e.g. the Isolation Forest years and the ARIMA forecast). It's now fully executed end-to-end with zero errors, and this README's table reflects the real output.
2. **SSL certificate verification was disabled** (`ssl.CERT_NONE`, `check_hostname=False`) for every data fetch — a real man-in-the-middle exposure. Fixed to use a properly verified default context via `certifi`.
3. **The Random Forest (Section 9B) had target leakage** — it predicted `Annual` from the Jan–Dec monthly columns that `Annual` is literally defined as the mean of. Replaced with a genuinely independent feature set (Year, Decade, CO₂ level, lagged prior-year anomalies). The corrected, honest result: in-sample R² still looks strong (~0.99) but 5-fold time-series CV R² is actually **negative** (≈ −2.4) — the model doesn't generalize to unseen future periods. Less flattering than the original leaky number, but real.
4. **ARIMA's (2,1,2) order was hardcoded** with no visible connection to the ACF/PACF diagnostics computed right above it. Replaced with a small AIC grid search over the training period, which selects **(1,1,1)** — a materially different, data-driven choice that also changes the 2035 forecast substantially (see table above).
5. **The world map assigns one anomaly value per country from centroid latitude alone** — a coarse proxy that visually flattens large, multi-zone countries (Russia, Canada, the US, Brazil, Australia). Now explicitly caveated in the notebook rather than described as "physically grounded."
6. **The polynomial regression (Section 9A) had no held-out test**, unlike the ARIMA/RF sections. Added the same train/test split (≤2014 / >2014). Result: **all three curves (linear, poly-2, poly-3) post negative test R²** — none extrapolate the post-2014 acceleration well, which is itself an important, honest finding about the limits of simple trend extrapolation on this series.

Three additional bugs surfaced only once the notebook was actually run (not part of the original 6-item review, found by execution):
- `geopandas.datasets.get_path('naturalearth_lowres')` was removed in geopandas 1.0+ — the world map now fetches the same Natural Earth country layer directly from its canonical source.
- Two chart-label strings contained a literal embedded newline inside a single-quoted string (invalid Python), and one OWID data-cleaning line raised `TypeError: bad operand type for unary ~: 'float'` on newer pandas (inverting a NaN-containing mask) — both fixed.
- **A partial in-progress year silently counted as a complete annual mean.** `df_global[month_cols].mean(axis=1)` uses pandas' default `skipna=True`, so a year with only Jan–Jun reported (the current year, mid-stream) still produced an "Annual" value — averaged over 6 months, not 12 — and that partial value was then treated as the latest complete year everywhere downstream (total warming, record-year check, the dashboard KPIs). Fixed by requiring all 12 months present before a year counts.

`requirements.txt` was also pinned to exact tested versions (previously open `>=` ranges) and dropped an unused `xarray` dependency.

---

## Project Structure

```
├── notebooks/
│   └── Global_Temperature_Analysis.ipynb   # Main analysis notebook
├── assets/
│   └── global_temperature_dashboard.html   # Interactive Plotly dashboard (open in browser)
├── data/                                    # Auto-populated at runtime
├── requirements.txt
└── README.md
```

## Quick Start

```bash
git clone https://github.com/Nobby-sibanda/Global-Temperature-Analysis.git
cd Global-Temperature-Analysis
pip install -r requirements.txt

# Full notebook
jupyter notebook notebooks/Global_Temperature_Analysis.ipynb

# Interactive dashboard — open directly in a browser, no Jupyter needed
open assets/global_temperature_dashboard.html      # macOS
xdg-open assets/global_temperature_dashboard.html  # Linux
```

> **Note:** The notebook fetches all data automatically via URL at runtime.  
> An internet connection is required for the first run.

---

## Interactive Dashboard Preview

`assets/global_temperature_dashboard.html` is a self-contained page — KPI cards plus six
interactive Plotly panels, built from the same verified numbers as the notebook:

1. **Global Annual Trend** — annual anomaly bars + 10-year rolling mean
2. **World Map** — warming anomaly by country (same choropleth as Section 7)
3. **CO₂ vs. Temperature** — dual-axis trend
4. **Hemispheric Comparison** — NH vs. SH vs. global mean
5. **Decadal Mean Anomaly** — bar chart by decade
6. **ARIMA Forecast to 2039** — AIC-selected order, with 90% confidence band

All charts support hover tooltips, zoom/pan, and clickable legends. Plotly is loaded from a
CDN, so an internet connection is needed the first time it's opened; the chart data itself is
fully embedded and works offline after that.
