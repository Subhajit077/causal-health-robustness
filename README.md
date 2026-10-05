# How much should we trust an observational health claim? A robustness study of vitamin D and mortality (NHANES)

Case study: serum 25-hydroxyvitamin D below 50 nmol/L and all-cause mortality in US adults, using NHANES 2001-2014 linked to public-use mortality follow-up through 2019. The aim is to measure how fragile an observational estimate is and how far standard checks can help. It makes no clinical claim.

## Data
7 NHANES cycles; 41,836 adults eligible for linked mortality; 36,874 with a vitamin D measurement; 31,176 complete cases for the Cox models (5,077 deaths). 2001-06 values are CDC's regression-converted LC-MS/MS-equivalent values; 2007-14 values are measured directly. Public-use follow-up times are partly perturbed by NCHS, so only all-cause mortality is analysed.

## Findings

**1. The estimate depends strongly on adjustment.** Hazard ratio for vitamin D < 50 nmol/L (Cox, stratified by cycle):

| Adjustment | HR [95% CI] |
|---|---|
| none | 1.05 [0.99, 1.11] |
| + age | 1.40 [1.32, 1.48] |
| + sex, race/ethnicity, BMI | 1.50 [1.41, 1.60] |
| + smoking, education, income, exam season | 1.37 [1.29, 1.46] |

The low-vitamin-D group is younger on average (46.8 vs 50.3 years), which hides the association until age is adjusted. 5-year mortality: naive difference +0.69 points [0.13, 1.25], adjusted +2.32 points [1.72, 3.01] (bootstrap CIs; odds ratio 1.61 [1.44, 1.85]).

**Design-based inference.** With NHANES exam weights (WTMEC2YR/7), strata and PSUs (214 PSUs, 105 strata, 109 df), the fully adjusted HR for vitamin D < 50 nmol/L is 1.44 [1.32, 1.57] by delete-one-PSU jackknife and 1.44 [1.32, 1.58] with PSU-clustered robust SEs, against 1.37 [1.29, 1.46] unweighted and model-based. The standard error is 35% larger than the model-based one (design effect about 1.8).

**2. Within the choices we varied, the estimate is fairly stable.** 192 Cox models (6 covariate groups, 3 assay periods, age always adjusted): HR 1.28 to 1.54 (mean 1.39, SD 0.06), all CIs above 1. Adjusting for race raises the mean HR (1.36 to 1.42) and adjusting for smoking lowers it (1.41 to 1.36). Cutoffs (all cycles, no lag): < 30 nmol/L 1.66, < 50 1.37, < 75 1.19; per 10 nmol/L lower 1.06. Excluding the first 0 / 24 / 60 months of follow-up changes the < 50 estimate from 1.37 to 1.33 to 1.28.

**3. Stability across analyses does not detect hidden confounding.** Semi-synthetic benchmark: real covariates, planted effect, known truth, 100 repetitions per scenario (bias in percentage points of 5-year risk difference):

| Scenario | naive | regression | IPW | AIPW (logit) | AIPW (boosting) |
|---|---|---|---|---|---|
| A linear outcome | -1.84 | 0.05 | 0.03 | 0.02 | 0.05 |
| B nonlinear outcome | -2.64 | 0.01 | 0.01 | 0.02 | 0.11 |
| C + poor overlap | -3.90 | -0.18 | -0.51 | -0.06 | 0.02 |
| D + hidden confounding | 0.06 | 3.13 | 3.09 | 3.15 | 3.23 |
| E + strong hidden confounding | 2.91 | 6.01 | 5.98 | 6.02 | 6.06 |
| F misspecified outcome, heterogeneous effect | -2.19 | -0.05 | 0.04 | 0.01 | 0.09 |

With all confounders observed, any adjusted estimator recovers the planted effect and the naive difference is badly wrong (in scenario A it has the wrong sign). With a hidden confounder every adjusted estimator is biased in the same way and AIPW interval coverage falls to 0%, so agreement between them is no evidence of correctness.

**4. Sensitivity.** E-value for the design-based estimate (HR 1.44): 1.90 (CI bound 1.72) using the common-outcome approximation; the unweighted estimate (HR 1.37) gives 1.79 (1.67). A hidden confounder would need to be associated with both low vitamin D and mortality by about this factor, beyond measured covariates. No measured covariate reaches that on both sides (the strongest, about 1.4); our measured covariates are crude proxies, so this does not rule out confounding by health status, diet or supplement use.

## Limitations
The multiverse, adjustment path and benchmark are unweighted and use model-based intervals (too narrow by roughly a third; see the design-based result above). The design-based analysis treats the complete-case sample as the full design instead of using the full design with subpopulation weights, and the combined-cycle weight rule (WTMEC2YR/7) should be checked against CDC's guidance. No negative-control analysis. Complete-case analysis. Single vitamin D measure per person; early-cycle values are predicted, not measured. Reverse causation is not ruled out; the lag analysis is a weak test. The benchmark's outcome and treatment models are logistic fits to the real data, hidden-confounder strengths are arbitrary, and scenarios differ in more than one respect. Not peer reviewed; not medical advice.

## Reproduce
`01_causal_health_robustness.ipynb` runs on CPU (about 1 hour in total). Data are downloaded from CDC inside the notebook. Results: `multiverse_v1.csv`, `spec_curve.csv`, `benchmark_v1.csv`, `benchmark_F.csv`, `jackknife_reps.csv`; figure: `fig_spec_curve.png`.
