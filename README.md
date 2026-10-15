# Predicting CO₂ Emissions from Commercial Aviation: A Machine Learning Regression Approach

Coursework for COM7003 Artificial Intelligence, part of my MSc in Artificial Intelligence and Data Science at Leeds Trinity University.

I used the open AeroSCOPE flight dataset (293,838 route records) to ask a simple question: can a model estimate a route's annual CO₂ emissions from information you'd have before the flight happens? I compared five regression models, tuned the best one, and then found out my best scores were too good to be true.

## The result, and why it isn't what it looks like

Random Forest and Gradient Boosting both scored R² = 1.0000 on the test set. That looked great until I checked the feature importances. In AeroSCOPE, CO₂ is calculated directly from fuel burn, and the capacity measures `ask` and `rpk` sit in the same chain. Those three features made up about 64% of the optimised forest's importance. The models weren't predicting anything, they were rebuilding a formula. That's data leakage, and none of those columns would exist at route-planning time.

The plain linear regression baseline, at R² = 0.72, is the honest number for pre-flight prediction. The report says so, and it describes a revised pipeline that drops the leaky columns and keeps only distance, aircraft type, airport codes, seats and the domestic flag.

| Model | Test R² | MAE (log scale) |
|-------|---------|-----------------|
| Linear Regression | 0.7214 | 1.0329 |
| Ridge Regression | 0.7214 | 1.0329 |
| Decision Tree | 0.9996 | 0.0397 |
| Random Forest | 1.0000 | 0.0005 |
| Gradient Boosting | 1.0000 | 0.0076 |
| Random Forest, tuned, top 10 features | 1.0000 | 0.0069 |

Errors are on the log1p-transformed CO₂ scale.

## What's in this repo

- `Aviation_Report(...).pdf`: the 12-page report. It covers the problem and who benefits, data preparation, model choice, tuning, evaluation, the leakage analysis, and deployment, ethics and bias.
- `Aviation_code.ipynb`: the Python notebook with the full pipeline. It uses pandas, NumPy, scikit-learn, Matplotlib and Seaborn.
- `Assessment2_Aviation.pptx`: a 20-slide presentation of the same project.

## How the pipeline works

I dropped the two continent columns (43.5% missing), removed 6,830 rows with CO₂ at or below zero, applied a log1p transform to the heavily skewed target, and removed outliers with the 1.5 × IQR rule. That left 244,478 records. Categorical columns were label-encoded and numeric features standardised, then I split 80/20 (195,582 train, 48,896 test). The Random Forest was tuned with `RandomizedSearchCV` (10 combinations, 3-fold CV).

## Running the notebook

The dataset isn't in this repo. AeroSCOPE is published on Zenodo by Salgas et al. (2023), described in [this paper](https://doi.org/10.59490/joas.2023.7201). The notebook reads a file called `AeroSCOPE_global_aviation_traffic_dataset_16_11.csv`, so download the dataset, put that file next to the notebook, and run the cells in order.

## Other limits I flagged

The dataset leans towards Europe and North America, so predictions for other regions could be less reliable, which matters if a model like this were ever used for carbon pricing. The report also covers dataset shift as fleets change, and notes that CORSIA compliance needs ICAO-approved methods, so a model like this is realistically a planning tool.

## Please don't copy

This is my own assessed work, shared for my portfolio. Don't submit it as yours. If it helps your thinking, cite it.

## Contact

- GitHub: [Zarin2000](https://github.com/Zarin2000)
- LinkedIn: [Zarin Tasnim Nuzhat](https://www.linkedin.com/in/zarin-tasnim-nuzhat-829007202)
- ORCID: [0009-0006-3469-1876](https://orcid.org/0009-0006-3469-1876)
