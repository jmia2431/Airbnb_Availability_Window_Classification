# Airbnb Availability Window Classification (Sydney, Inside Airbnb)

## Repository Structure
- `analysis/`: Rmd files for cleaning, preprocessing, modelling and reporting
- `data/`: cleaned / analysis-ready datasets
- `final/`: final report / presentation outputs (HTML/PDF) and supporting assets
- `archive/`: drafts or intermediate artifacts (if applicable)

## Project Overview
This group coursework project builds an interpretable multi-class classification workflow to predict which future availability window (0–30 / 31–60 / 61–90 days) contains the most available days for a listing.
The analysis is based on the Inside Airbnb open dataset for Sydney (June 10, 2025). 

## Data Source
- Source: Inside Airbnb (Sydney dataset dated June 10, 2025)
- Raw scale: **18,187 listings × 79 variables**
- Data types: 42 numeric and 32 categorical variables; high-cardinality features required careful handling
- Missingness (raw): 8.81% overall

## Data Cleaning & Preprocessing (Key Work)
To ensure consistency and avoid information leakage, we implemented a structured cleaning and preprocessing workflow:
- **Variable removal:** dropped identifiers (e.g., `id`, `host_id`), URLs, free-text fields, and redundant coordinates
- **Standardisation & feature construction:** 
  - converted `bathrooms_text` to numeric
  - derived `host_tenure` and `listing_age` from date fields
  - cleaned currency/percentage symbols
  - encoded `neighbourhood_overview` as a binary indicator
- **Validation filters:** enforced logical constraints; retained Australian listings with valid `license` and `has_availability`
- **Outcome construction:** partitioned forward availability into 0–30, 31–60, 61–90 day windows, and labelled the class by the window with the most available days (`First`, `Second`, `Third`, `Indeterminate`)
- After cleaning: **8,883 listings remained**, high-cardinality variables reduced **59 → 9**, missingness reduced 8.81% → 0.39%

## Preprocessing Pipeline (Leakage-Safe, Reproducible)
All feature transformations were implemented using a unified `tidymodels::recipe` pipeline, fitted **only within training folds**
during cross-validation to prevent leakage. Key steps included:
- character → factor (`step_string2factor`)
- merge rare categories below 2% (`step_other(threshold = 0.02)`)
- median imputation for numeric features (`step_impute_median`)
- log transform + normalisation for numeric predictors (`step_log`, `step_normalize`)
- dummy encoding for categorical features (`step_dummy`)
- upsampling to address class imbalance (`step_upsample`)

## My Contribution
This was a group coursework project. My primary contributions focused on data cleaning and preprocessing, including:
- designing and implementing the cleaning rules (feature removal, standardisation, validation filters)
- constructing the multi-class availability outcome from forward availability windows
- building a leakage-safe preprocessing pipeline using `tidymodels::recipes` to ensure consistent transformations across models
- improving dataset quality by reducing missingness and handling high-cardinality categorical features

## Tools & Technologies
R, tidyverse, tidymodels (recipes), rsample, knitr/rmarkdown/quarto
