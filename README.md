# Banking Crisis Data Cleaning & Preprocessing (2000-2024)

## Project Goal
Prepare clean, analysis-ready datasets to map global banking crises and U.S. recovery dynamics for downstream visualization.

## Raw datasets used
- `original_data/20160923_global_crisis_data.xlsx`: Global systemic banking crises (country-year panel).
- `original_data/FDIC Failed Bank List (US).csv`: FDIC failed banks (institution-level, U.S.).
- `original_data/bank_aggregates.xlsx`: U.S. banking sector aggregates (quarterly metrics).
- `original_data/InvestorCreditSentiment.xlsx`: Investor credit sentiment (HYS/IG signals, yearly).
- `original_data/USREC.csv`: NBER U.S. recession indicator (monthly, 0/1).

## Cleaned outputs (in `data/cleaned/`)
- `global_crisis_cleaned.csv`: Country-year panel; standardized country codes, parsed years, selected crisis indicators ready for joins.
- `fdic_failed_banks_cleaned.csv`: Institution-level with parsed open/close dates, standardized states, resolved encodings; suitable for timelines and counts.
- `bank_aggregates_cleaned.csv`: Quarterly U.S. sector metrics with tidy column names, numeric types, and validated date index.
- `investor_credit_sentiment_cleaned.csv`: Annual sentiment measures aligned to calendar year; numeric types and consistent tickers/labels.
- `usrec_cleaned.csv`: Monthly recession indicator as datetime index; validated continuity and binary values.
- `dim_date.csv`: Full date dimension (daily) with year/quarter/month fields for modeling and joins.
- `dim_geography.csv`: Normalized geography mapping (countries, US states) for consistent keys across datasets.
- `dim_bank.csv`: Bank reference dimension (if applicable) derived from FDIC entities for consistent identifiers.
- `dim_usrec.csv`: Convenience dimension for recession periods (start/end spans).
