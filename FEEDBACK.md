# Response to instructor feedback

Feedback from **Dr. I. Nyalala, 10 November 2026**, and how each point was addressed (11 November 2026).
Section numbers refer to `Shambhavi_MiniProject.ipynb`.

| # | Feedback | What changed | Where |
|---|---|---|---|
| 1 | Notebook outputs on GitHub come from the old data file, but the text quotes the new data. | Added a **data fingerprint**: row count, date range and SHA-256 checksum of each CSV (line-ending independent). The notebook stops if the file is not the analysed version. A final **reproducibility check** recomputes every key number quoted in the text (53 so far) and stops if any disagree. | §1.1, last code cell |
| 2 | README only says how to run the code. | README now gives the project status, the evaluation design, key findings with numbers and all figures (saved automatically to `figures/` by the notebook). | `README.md` |
| 3 | No requirements file and no code licence. | Added `requirements.txt` (tested minimum versions) and an MIT `LICENSE` for the code. The data keep their WFP CC BY-IGO licence. | `requirements.txt`, `LICENSE` |
| 4 | Test that the notebook runs from a fresh clone. | Cloned into an empty folder, installed only `requirements.txt` in a clean virtual environment, and ran the notebook end to end: **0 errors, 0 warnings**, fingerprint and all quoted-number checks pass. Tested on Windows (Python 3.12) and Linux, with both the oldest (pandas 2.2.3, scikit-learn 1.5.2) and newest (pandas 3.1.1, scikit-learn 1.9.1) supported versions. | README "How to run" |
| 5 | (a) One time split; test on 2023 – Sep 2026, wholesale only; report 2022 for retail and wholesale as a secondary check. | Training 2006–2021, with **all tuning by rolling-origin cross-validation inside these years** (validation years 2017–2021), so 2022 stays an honest secondary check, reported separately for retail and wholesale. The primary test is Jan 2023 – Sep 2026, filtered explicitly to wholesale. | §2.0, §2.6 |
| 6 | (b) State the missing Zanzibar markets as a limitation; do not extend the data. | Stated as a limitation; the data are not extended; the model is not to be applied to Zanzibar. | §1.3.4 (and Section 5) |
| 7 | The spike rate changes across years, and most training data is retail while the test data is wholesale. | Both shifts measured: the wholesale spike rate ranges from **6% to 32%** by year (training 15.7%, 2022 21.6%, test 9.6%), and training is **77% retail** vs a 100% wholesale test. A **third shift** was found: unchanged wholesale quotes rise from **15% of months (2021) to 57% (2026)**. A 9-step evaluation plan covers CV scored on wholesale rows, base-rate-aware metrics (PR-AUC lift, recall, ROC-AUC), same-row baselines, results by year, past-only drift features, wholesale-only vs all-data training, a fixed monthly alert budget, calibration checks and subgroup results. | §2.6, Figure 2.6 |
| 8 | The 10% threshold is justified only by the data distribution; justify it by the decision it supports. | Rewritten **decision-first**. **Harm:** food is 59.9% of household consumption (NBS HBS 2017/18). **Speed:** a 10% month exceeds a typical series' yearly drift. **Reliability:** 66% of >10% jumps are still ≥10% higher three months later. **Workload:** about 16 alerts per 100 wholesale series a month, vs 28 at 5%, or only 12% of lasting rises caught at 20%. **Action cadence:** monthly NFRA releases. The statistics (boxplot fence 9.7%, 88th percentile) are now supporting evidence, and the limitations (trade-off, gradual rises) are stated. All evidence uses training years only. | §2.2, Figure 2.5 |
| 9 | p-values assume independent observations, which you do not have. | Limitation stated wherever p-values appear. Added **cluster bootstrap** intervals (resampling whole series, and separately whole months), a **block bootstrap** for the maize–rice correlation and a **Mantel test** for distance vs co-movement. Honest intervals are 1.8–5× wider than naive ones, and every finding still holds. | §2.5, notes in §1.3.3, §2.3, §2.4.2 |

## Also completed since the feedback

**Section 3 (Preprocessing):**
- **Cleaning:** outliers are flagged as sudden departures from peer markets in the same month (226 prices, 0.33%).
- **Missing months:** a strategy that never fills labels and never interpolates features.
- **Features:** 15 features built from past months only, with a **scrambling test** proving that no feature uses future information.
- **Transformation:** shape-based robust scaling, fitted on the training years only.
- **Reduction / discretization:** entropy discretization, plus a PCA check that explains why PCA is not used.
