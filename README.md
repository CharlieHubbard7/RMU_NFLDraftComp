# Predicting first-round NFL Draft picks (RMU Sports Analytics Competition 2026)

The competition prompt: the 2026 NFL Draft is coming to Pittsburgh, and a memorabilia shop in the Strip District needs to know which offensive skill-position prospects will go in the first round, so it can stock the right jerseys.

The approach uses **position-specific logistic regression models** for QBs, WRs and RBs, trained on draft classes from 2010–2024 and scored on the 2026 class.

| Position | Features (examples) | Cross-validated AUC |
|---|---|---|
| Quarterback | completion %, yards per attempt, TD/INT, rushing production, size | 0.74 |
| Wide receiver | receptions, yards, TDs, yards per reception, 40-yard dash, size | 0.77 |
| Running back | yards per carry, rushing yards and TDs, receptions, 40-yard dash, size | 0.81 |

Top 2026 prospects by model probability:
- **Jeremiyah Love** (RB): 0.90
- **Fernando Mendoza** (QB): 0.84
- **Omar Cooper Jr.** (WR): 0.66

## Design choices

- **Separate models per position.** QBs, WRs and RBs are judged on different things; one pooled model would blur them.
- **Logistic regression over black-box models.** It gives probabilities and coefficients the shop owner can read.
- **Pipeline:** median imputation → standard scaling → class-balanced logistic regression, evaluated with stratified k-fold cross-validation and ROC curves.
- **Calibrating to the draft.** Historical first rounds average 3 QBs, 4 WRs and 1.3 RBs (8.3 skill players). A fourth step ranks all prospects together against that base rate.
- **Dropped features.** Postseason win rate was excluded because the College Football Playoff expansion changes what it means across years.

## Files

| File | Contents |
|---|---|
| [`draft_analysis_final.ipynb`](draft_analysis_final.ipynb) | Full analysis: EDA, feature engineering, models, ROC curves, 2026 predictions |
| [`nfl_presentation.pdf`](nfl_presentation.pdf) | Competition presentation |
| [`RMU SAC_ NFL Draft Competition.pdf`](RMU%20SAC_%20NFL%20Draft%20Competition.pdf), [`RMU SAC_ Data Descriptions.pdf`](RMU%20SAC_%20Data%20Descriptions.pdf) | Competition prompt and data dictionary |

The competition datasets aren't redistributed here. The notebook expects them under `TrainingData/` and `TestingData/`.

**Known limitation:** the models reward proven college production, so they likely undervalue high-ceiling developmental prospects.
