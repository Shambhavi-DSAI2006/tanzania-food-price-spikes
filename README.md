# Early Warning of Food-Price Spikes in Tanzanian Markets

**Can we predict, one month ahead, which market–commodity price series will rise by more than 10%, so that Tanzania's Ministry of Agriculture can act early, by releasing reserve stock or prioritising market monitoring?**

Mini-project for *Z3003 Data Mining: Scaling to Large Data*, IIT Madras Zanzibar (BS Semester 5, DSAI).
Author: **Shambhavi Srivastava** (ZDA24B005) · Dataset approved by Dr. I. Nyalala, 20 October 2026.

---

## Project status

| Section | Content | Status |
|---|---|---|
| 1 · Data understanding | Shape, attribute types, missing values, data-quality dimensions | ✅ Complete |
| 2 · Exploratory analysis | Summaries, spike threshold, correlation + confounder, χ² tests, Gower and haversine distance | ✅ Complete (being revised after instructor feedback, 10 Nov) |
| 3 · Preprocessing | Within-series cleaning, past-only features, robust scaling fitted on training data | 🔄 Next |
| 4 · Mining | Classification with baselines; precision / recall / F1 / PR-AUC with time-series cross-validation | ⏳ Planned |
| 5 · Findings | One-page summary for the Ministry, scaling, ethics and limits | ⏳ Planned |

**Evaluation design (agreed with the instructor):** one time split for all series. Training uses 2006–2021, with model tuning by cross-validation inside these years. The primary test is **Jan 2023 – Sep 2026, wholesale only**; 2022 (retail and wholesale) is reported as a secondary check.

---

## Data

World Food Programme, *United Republic of Tanzania — Food Prices* and *Markets*, via the Humanitarian Data Exchange (downloaded 9 November 2026).

| | |
|---|---|
| Price table | 67,999 rows × 16 columns · Jan 2006 – Sep 2026 · 870 price series |
| Coverage | 63 markets in mainland Tanzania (no Zanzibar markets) · 31 commodities · retail and wholesale |
| Unit of analysis | One series-month: one commodity, in one market, in one unit, at one price level, in one month |

---

## Key findings so far

**Data understanding (Section 1)**
- **No empty cells, but 7.0% of expected months have no row** (5,141 of 73,140). The gaps are systematic: wholesale series miss 15.2% of months versus 2.4% for retail (χ² test), and the rate varies by region and year.
- **The gaps do not hide price jumps,** but comparing a price with the last *available* price would more than double the spike rate (28.4% vs 12.4%). Labels are therefore built from consecutive months only.
- **Data-quality issues found:** all 30 "MT" rows are really per-kg prices; one entry error (3,000,000 TZS/kg); 65 wrong USD prices; retail series end in 2022.

**Exploratory analysis (Section 2, training years 2006–2021 only)**
- **Spikes are common and heavy-tailed:** 11.9% of months rise by more than 10%. A normal curve with the same spread would predict 0.16%.
- **The 10% threshold matches the boxplot outlier fence** (Q3 + 1.5·IQR = +9.7%) and sits at the 88th percentile.
- **Confounder:** maize and rice prices correlate at ρ = 0.79, but the shared rise in prices over time (6–8% a year) drives this. Their month-to-month changes correlate at only ρ = 0.36.
- **Spikes cluster:** the chance of a spike rises from 10.6% to 20.3% when there was a spike the month before.
- **Season and product matter:** maize spike risk is 21–30% in October–January (the lean season). Vegetables spike most often, meat least.
- **Retail prices look "sticky":** 46% of retail monthly changes are exactly zero (8% for wholesale), suggesting carried-forward prices.
- **Nearby markets move together:** price changes correlate at about 0.35 for markets under 250 km apart versus 0.19 beyond 1,000 km (haversine distance).

---

## Figures

**Coverage and missing months**
![Coverage and gaps](figures/fig_1_1_coverage_and_gaps.png)

**Monthly price changes are heavy-tailed**
![Change distribution](figures/fig_2_1_change_distribution.png)

**When and where spikes happen**
![Spike rate by season and food group](figures/fig_2_2_spike_rate_season_group.png)

**The confounder: a shared price trend**
![Staples and confounder](figures/fig_2_3_staples_confounder.png)

**Nearby markets co-move**
![Distance vs co-movement](figures/fig_2_4_distance_comovement.png)

---

## Repository contents

```
Shambhavi_MiniProject.ipynb             analysis notebook (all outputs saved)
data/Shambhavi_MiniProject_Data.csv     WFP price table (renamed, otherwise unchanged)
data/Shambhavi_MiniProject_Markets.csv  WFP markets table (renamed, otherwise unchanged)
figures/                                figures saved by the notebook
requirements.txt                        Python packages
LICENSE                                 MIT licence for the code
```

---

## How to run

Requires Python 3.10 or newer. From a fresh clone:

```bash
git clone https://github.com/Shambhavi-DSAI2006/tanzania-food-price-spikes.git
cd tanzania-food-price-spikes
python -m venv .venv
# Windows:  .venv\Scripts\activate      macOS/Linux:  source .venv/bin/activate
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace Shambhavi_MiniProject.ipynb
```

Or open the notebook in VS Code or Jupyter and choose **Run All**. A full run takes about 30 seconds.

**Reproducibility checks built into the notebook:**
1. A **data fingerprint** (row count, date range and SHA-256 checksum of each CSV, independent of line endings) stops the run if the data file is not the analysed version.
2. A final **consistency check** recomputes every key number quoted in the text and stops the run if any no longer matches.

---

## Licences

- **Code** (the notebook's code cells): MIT licence, see [LICENSE](LICENSE).
- **Data**: © World Food Programme, licensed under [CC BY-IGO](https://creativecommons.org/licenses/by/3.0/igo/). Redistributed here unchanged except for file names, with attribution as the licence requires. Source: https://data.humdata.org/dataset/wfp-food-prices-for-united-republic-of-tanzania
