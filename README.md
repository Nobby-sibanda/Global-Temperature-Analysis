# 🌍 Global Temperature Analysis

**Author:** Nobukhosi Sibanda  
**Degree:** MSc Data Science (May 2026) | BSc Finance | Quantitative Analyst Student  

A comprehensive data science analysis of Earth's surface temperature record spanning **144 years (1880–2024)**, combining four independent datasets, full exploratory data analysis, and seven machine learning / statistical algorithms.

---

## Data Sources

| Source | What it provides | Coverage |
|--------|-----------------|----------|
| **NASA GISTEMP v4** | Global & zonal land-ocean temperature anomalies | 1880–2024 |
| **Berkeley Earth** | Independent monthly global land+ocean reconstruction | 1850–2024 |
| **NOAA ESRL (Mauna Loa)** | Atmospheric CO₂ annual means | 1959–2024 |
| **Our World in Data / OWID** | Country-level GHG temperature forcing | 1990–2022 |

All datasets are downloaded directly at notebook run-time — no manual downloads required.

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
| 10 | **Forecasting — ARIMA & Prophet** | ADF stationarity test, ARIMA(2,1,2), Facebook Prophet |
| 11 | **Clustering & PCA** | K-Means (k=4) climate regimes, PCA on zonal profiles |
| 12 | **Anomaly Detection** | Changepoint detection (PELT), Isolation Forest |
| 13 | **Key Findings** | Summary table of all major results |

---

## Key Findings

| Metric | Value |
|--------|-------|
| Total warming since 1880 | **+1.2 to +1.5 °C** |
| 21st-century warming rate | **~0.20 °C/decade** (2× the 20th-century average) |
| CO₂ rise (1959 → 2024) | **316 → 424 ppm (+34%)** |
| CO₂–Temperature correlation | **r > 0.95** (p < 0.001) |
| Arctic amplification | Polar regions warm **2–4× the global average** |
| Most anomalous years (Isolation Forest) | **1998, 2016, 2023** (El Niño peaks) |
| Structural break (changepoint) | **~1975–1980** — onset of accelerated warming |
| ARIMA forecast 2035 | **+1.5 to +1.8 °C** above 1951–1980 baseline |

---

## Project Structure

```
├── notebooks/
│   └── Global_Temperature_Analysis.ipynb   # Main analysis notebook
├── data/                                    # Auto-populated at runtime
├── requirements.txt
└── README.md
```

## Quick Start

```bash
git clone https://github.com/Nobby-sibanda/Global-Temperature-Analysis.git
cd Global-Temperature-Analysis
pip install -r requirements.txt
jupyter notebook notebooks/Global_Temperature_Analysis.ipynb
```

> **Note:** The notebook fetches all data automatically via URL at runtime.  
> An internet connection is required for the first run.
