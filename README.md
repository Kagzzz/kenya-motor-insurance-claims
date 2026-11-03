<div align="center">

# Modelling Motor Vehicle Insurance Claims in Kenya

### A Frequency–Severity Approach to Risk-Based Premium Estimation

**SAC 407 · Project in Actuarial Science**

<br />

School of Pure and Applied Sciences<br />
Department of Mathematics and Actuarial Science<br />
Kenyatta University · March 2025

<br />

[![Report](https://img.shields.io/badge/Report-Word-2B579A?style=flat-square)](report/Modelling%20Motor%20Vehicle%20Insurance%20Claims%20in%20Kenya.docx)
[![Presentation](https://img.shields.io/badge/Presentation-PowerPoint-B7472A?style=flat-square)](presentation/Motor%20Insurance%20Frequency-Severity%20Presentation.pptx)
[![Language](https://img.shields.io/badge/Analysis-R-276DC3?style=flat-square)](https://www.r-project.org/)

</div>

---

## Overview

Motor insurance is one of the largest classes in Kenya's general insurance market, and one of its least profitable. Between 2022 and 2024 the class reported repeated underwriting losses even as gross premiums grew past **KES 55 billion**. This project asks a pricing question: can a transparent, interpretable model estimate expected claim cost more accurately than a single blended rate?

The study applies the **frequency–severity framework** to the claims experience of a Kenyan insurer. Claim frequency (how often claims occur) and claim severity (what each claim costs) are modelled separately, then multiplied to produce a **pure premium**:

```
Pure Premium = E(N) × E(X)
```

where `E(N)` is the expected number of claims and `E(X)` is the expected cost per claim. The result is a risk-differentiated estimate of claim cost — before expenses, commissions, taxes, reinsurance and profit loadings.

This repository contains the full written report and the accompanying presentation.

---

## Authors

| Name | Registration number |
| :--- | :--- |
| Karanja Wambura Brenda | I162/3917/2022 |
| Jeptoo June | I162/0723/2022 |
| Junior Joel | I162/4837/2022 |
| Mwangi Kiboi Bradon | I162/0715/2022 |
| Mwangi Kagucia | I162/0713/2022 |

---

## Repository structure

```text
kenya-motor-insurance-claims/
├── README.md
├── report/
│   └── Modelling Motor Vehicle Insurance Claims in Kenya.docx
└── presentation/
    └── Motor Insurance Frequency-Severity Presentation.pptx
```

| Path | Description |
| :--- | :--- |
| [`report/Modelling Motor Vehicle Insurance Claims in Kenya.docx`](report/Modelling%20Motor%20Vehicle%20Insurance%20Claims%20in%20Kenya.docx) | The complete project report: five chapters, 19 tables, 12 figures and the full R appendix. |
| [`presentation/Motor Insurance Frequency-Severity Presentation.pptx`](presentation/Motor%20Insurance%20Frequency-Severity%20Presentation.pptx) | The 14-slide defence presentation summarising the data, models, results and recommendations. |

---

## The report

| Chapter | Contents |
| :--- | :--- |
| **1. Introduction** | Market context, statement of the problem, objectives, justification and significance. |
| **2. Literature Review** | Motor claim modelling, frequency and severity GLMs, dependence, machine-learning approaches, and the research gap in Kenya. |
| **3. Methodology** | Research design, data preparation, zero-truncated count models, Gamma and Lognormal severity models, pure premium construction, assumptions and limitations. |
| **4. Data Analysis and Results** | **Part I — Data Analysis:** data preparation and descriptive analysis of frequency, severity and explanatory variables. **Part II — Results and Findings:** model estimates, diagnostics, pure premium estimation and risk-profile differentiation. |
| **5. Conclusions and Recommendations** | Findings, practical recommendations and areas for future research. |
| **References** | Eleven cited sources, including AKI and IRA industry reports. |
| **Appendix A** | Complete R code for data preparation, modelling, diagnostics and pure premium estimation. |

---

## Data

The analysis uses a secondary claims dataset supplied by a Kenyan insurer:

| Item | Value |
| :--- | ---: |
| Raw claim records | 107,566 |
| Variables | 16 |
| Period covered | 2019 – 2024 |
| Unique policies (frequency dataset) | 69,327 |
| Fully settled claims (severity dataset) | 53,739 |

Because the file is a claims database, **every policy has claimed at least once**. This rules out ordinary Poisson and Negative Binomial models and motivates zero-truncated specifications. Severity is measured on fully settled claims only, using gross paid amounts on the original KES scale.

> **Note:** the underlying policy-level dataset is confidential and is **not** included in this repository. The report and its appendix document the variables, cleaning steps and models in full.

---

## Methodology

### Claim frequency

Counts range from one to four claims per policy, so the analysis conditions on `N > 0`:

- **Zero-Truncated Poisson (ZTP)** — primary specification.
- **Zero-Truncated Negative Binomial (ZTNB)** — alternative allowing extra dispersion.

### Claim severity

Claim amounts are continuous, positive and strongly right-skewed:

- **Gamma GLM** with a log link — primary specification.
- **Lognormal regression** — comparison model, assessed on the original KES scale with a bias adjustment.

### Model evaluation

- Frequency: log-likelihood, AIC, residual diagnostics, observed-versus-predicted counts and predictive error.
- Severity: mean absolute error (MAE) and root mean squared error (RMSE), with predictions returned to the KES scale.
- Validation used an approximately **80/20** development–test split, made at policy level so no policy appeared on both sides.

---

## Key results

### Selected models

| Component | Selected model | Why it was selected |
| :--- | :--- | :--- |
| Frequency | **Zero-Truncated Negative Binomial** | Substantially lower AIC and higher log-likelihood than ZTP; predictive errors were almost identical, so the gain is distributional fit. |
| Severity | **Gamma GLM** | Lower MAE and RMSE on the KES scale and a mean prediction closer to observed severity. Lognormal won on likelihood but over-predicted average claim cost. |

### Estimated pure premiums

| Statistic | Estimate (KES) |
| :--- | ---: |
| Mean | 637,700 |
| Median | 592,221 |
| First quartile | 546,984 |
| Third quartile | 721,507 |
| Minimum | 384,630 |
| Maximum | 1,186,221 |

### Differentiation by vehicle type

| Vehicle type | Mean estimated pure premium (KES) |
| :--- | ---: |
| Bus | 842,433 |
| Truck / Lorry | 811,348 |
| SUV / 4WD | 772,657 |
| Saloon (reference) | 542,831 |

Bus estimates sit roughly **55% above Saloon**, and almost all of that gap comes from severity rather than frequency.

---

## Key findings

- **Severity drives risk differentiation, not frequency.** Predicted frequency varied only between about **1.46 and 1.63** claims per claimant policy; only Minivan differed significantly from Saloon. Severity separated vehicle types far more sharply.
- **Significant severity predictors** were vehicle age, sum insured, Bus, Matatu/PSV, SUV/4WD and Truck/Lorry. Sum insured added about **+4.0%** expected severity per KES 1 million; vehicle age reduced it by about **1.2%** per year.
- **The framework produces a meaningful spread** in expected claim cost — from roughly KES 385,000 to KES 1.19 million — using characteristics an insurer already records.
- **The estimates are claim costs, not commercial premiums.** They describe claimant experience and exclude expenses, commissions, taxes, reinsurance and profit margins.
- **Better data would materially improve pricing.** Complete policy-level records with non-claimant policies and exposure would allow unconditional annual frequencies and portfolio-wide pure premiums.

---

## Limitations

- No zero-claim policies: frequency is estimated on claimant policies only.
- No exposure data (policy duration or earned exposure), so an offset cannot be used.
- Severity uses settled claims only; open and partially settled claims are excluded.
- A single insurer's book is analysed, so results do not automatically generalise to the Kenyan market.
- Several rating variables were unavailable, including driving experience, annual mileage, no-claim discount, policy excess and detailed coverage.

These limitations are stated and interpreted in Chapters 3 and 5 of the report.

---

## Using this repository

1. **Read the report** — open [`report/Modelling Motor Vehicle Insurance Claims in Kenya.docx`](report/Modelling%20Motor%20Vehicle%20Insurance%20Claims%20in%20Kenya.docx) in Microsoft Word or LibreOffice.
2. **View the presentation** — open [`presentation/Motor Insurance Frequency-Severity Presentation.pptx`](presentation/Motor%20Insurance%20Frequency-Severity%20Presentation.pptx) in PowerPoint, Google Slides or LibreOffice Impress.
3. **Reproduce the analysis** — Appendix A of the report contains the full R workflow, from data import through to pure premium tables and figures.

---

## Citation

If you reference this work, please cite it as:

> Karanja, B., Jeptoo, J., Junior, J., Mwangi, B., & Mwangi, K. (2025). *Modelling Motor Vehicle Insurance Claims in Kenya Using a Frequency–Severity Approach for Risk-Based Premium Estimation*. SAC 407 Project in Actuarial Science, Kenyatta University.

```bibtex
@techreport{kagucia2025motor,
  title       = {Modelling Motor Vehicle Insurance Claims in Kenya Using a
                 Frequency-Severity Approach for Risk-Based Premium Estimation},
  author      = {Karanja, Brenda and Jeptoo, June and Junior, Joel and
                 Mwangi, Bradon and Mwangi, Kagucia},
  institution = {Kenyatta University},
  type        = {SAC 407 Project in Actuarial Science},
  year        = {2025},
  month       = {March}
}
```

---

<div align="center">

*Department of Mathematics and Actuarial Science · Kenyatta University*

</div>
