# RouteGuard: Last-Mile Address Risk Scoring

## Project Overview 💪🏻

RouteGuard is a portfolio project inspired by a research paper I came across on machine-learning-based address error detection in logistics systems.

The idea immediately connected with problems I observed in real last-mile delivery operations: a small address issue, such as a missing apartment number, unclear street information, or ZIP/city mismatch, can create much larger downstream problems — failed delivery attempts, returned parcels, inactive packages, and extra manual review workload.

I built this project to turn that inspiration into a reproducible data science workflow. The goal is to explore how address validation features, text-based signals, and machine learning can help identify high-risk delivery addresses before dispatch.

Since real logistics data contains sensitive customer and operational information, this public version does not use or expose any company data. Instead, it uses public and/or synthetic address data to demonstrate the methodology in a safe and reproducible way.

---

## Business Problem 📦

In last-mile delivery, address quality directly affects delivery success.

When an address is incomplete, inconsistent, or difficult to validate, the issue is often discovered too late — after the parcel has already been dispatched. At that point, the result may be a failed delivery attempt, an inactive package, additional warehouse handling, or a customer service escalation.

This project focuses on one practical question:

**Can we identify high-risk delivery addresses before dispatch, so operations teams can review them earlier and reduce address-driven inactive parcels?**

Examples of address-related risk signals include:

- Missing house number, apartment number, or unit number

- ZIP code and city mismatch

- Incomplete or unusually short address text

- Non-standard formatting or unclear delivery instructions

- Address text that is difficult to normalize or match to a valid location

---
## Data Privacy and Public Demo 

This repository is a public demonstration version.

It does **not** contain:

- Company parcel records

- Customer names or addresses from internal systems

- Warehouse-level confidential data

- Driver, DSP, or route-level internal records

- Any proprietary operational data

To make the project shareable, I will use public and/or synthetic address data to recreate the modeling workflow while protecting sensitive information.

The public version is not meant to duplicate any internal system. It is designed to show the methodology: data cleaning, address feature engineering, risk scoring, model evaluation, and operational impact analysis.

---
## Planned Workflow

### 1. Data Preparation and EDA
Load the public/synthetic address data, clean key fields, and explore address quality patterns.

### 2. Address Risk Label Design
Create realistic risk labels based on address issues such as missing unit numbers, ZIP/city mismatch, incomplete address text, or invalid formats.

### 3. Feature Engineering
Build address validation features, text-based features, fuzzy matching signals, and optional text embeddings.

### 4. Modeling and Evaluation
Train baseline and machine learning models, then evaluate performance using ROC-AUC, precision, recall, and F1 score.

### 5. Business Impact Simulation
Translate model scores into a pre-dispatch review workflow and estimate review workload and potential reduction in address-driven inactive parcels.

---

## Results

### Model Comparison

| Model | Precision | Recall | F1 | AUC |
|---|---|---|---|---|
| Rule-based Baseline | 0.93 | 0.30 | 0.46 | 0.65 |
| Logistic Regression | 0.64 | 0.44 | 0.52 | 0.74 |
| XGBoost (Structured) | 0.80 | 0.41 | 0.54 | 0.73 |
| **XGBoost (Fused) — Final** | **0.81** | **0.52** | **0.64** | **0.77** |

Final model evaluated on held-out test set (4,500 parcels, never seen during training).

### Key Finding
Combining structured validation features with sentence embeddings (all-MiniLM-L6-v2) outperformed structured features alone, confirming that semantic signals in address text carry predictive value beyond explicit validation rules.

---

## Business Impact Simulation

Simulating a pre-dispatch review workflow on 4,500 test parcels:

| Review Rate | Parcels Reviewed | Risky Caught | Recall | Precision |
|---|---|---|---|---|
| 5% | 225 | 225 | 25.0% | 100.0% |
| **10%** | **450** | **433** | **48.1%** | **96.2%** |
| 15% | 675 | 479 | 53.2% | 71.0% |
| 20% | 900 | 512 | 56.9% | 56.9% |

**Recommended operating point: 10% review rate**
- Review only 10% of daily parcel volume
- Intercept ~48% of risky addresses before dispatch
- 96% Precision — minimal wasted review effort

At scale (50K daily parcels): reviewing 5,000 parcels/day intercepts ~4,800 risky addresses pre-dispatch.

---

## Limitations

- **Synthetic data ceiling:** Error injection follows deterministic patterns, limiting model generalizability. In production with real customer-entered addresses — which contain richer linguistic variation — text embeddings are expected to capture stronger signals.
- **No reference database:** A real deployment would cross-validate addresses against a postal reference dataset (e.g., USPS), adding significant predictive power.
- **Label design:** Risk labels were designed based on operational observation, not ground-truth delivery outcomes.

This project demonstrates the methodology. Performance figures reflect synthetic data constraints, not production expectations.

---

## References

- Eroglu et al. (2025). *Predictive Machine Learning Framework for Address Error Detection in Logistics Systems.* IEEE UBMK.
- Swamy et al. (2025). *An Address Intelligence Framework for E-commerce Deliveries.* EMNLP Industry Track.
