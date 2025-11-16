## Data Dictionary

Purpose: Give the Power BI developer a clear, minimal schema guide for fast model setup. Keys are consistent across files; CSVs are UTF-8 with headers and snake_case columns.

### Shared Keys and Conventions
- date_key: Integer YYYYMMDD derived from a parsed date
- geo_key: ISO3 for countries; "USA-{ST}" for U.S. states; "USA" and "GLOBAL" reserved
- bank_key: Stable surrogate (hash of bank_name_clean|city|state_code)
- All dates are ISO (YYYY-MM-DD) when present; numeric columns coerced with errors='coerce'

---

### fact_crisis.csv (aka global_crisis_cleaned.csv)
- Grain: country-year (annual)
- Keys
  - geo_key: ISO3 country code (dimension: dim_geography)
  - date_key: YYYY0101 (Jan-01 of the given year)
  - year: Numeric year for convenience
- Measures/attributes (examples, actual names follow source)
  - Systemic crisis indicators (0/1)
  - Crisis-related attributes (categories, flags)
- Transformations
  - Country standardized to ISO3
  - Year filtered to 2000–2024 (ANALYSIS_START_YEAR/END_YEAR)
  - Dates coerced with errors='coerce'; non-binary crisis values coerced to NaN then inspected
- Source: original_data/20160923_global_crisis_data.xlsx

### fact_fdic_failures.csv (aka fdic_failed_banks_cleaned.csv)
- Grain: failed bank event (per bank closure date)
- Keys
  - bank_key: Stable SHA1-based surrogate generated from name/city/state
  - geo_key: "USA-{ST}" using USPS 2-letter state code
  - date_key: YYYYMMDD from Closing Date
- Attributes (examples)
  - bank_name_clean (string)
  - city (string), state_code (USPS)
  - closure metadata if present (e.g., acquiring institution, notes)
- Transformations
  - Encoding fixed (cp1252 → latin-1 fallback)
  - State codes uppercased and trimmed
  - Names trimmed; smart quotes/dashes normalized where applicable
- Source: original_data/FDIC Failed Bank List (US).csv

### fact_bank_aggregates.csv (aka bank_aggregates_cleaned.csv)
- Grain: national or state-level aggregates by period (typically monthly/quarterly/yearly; see file)
- Keys
  - date_key: YYYYMMDD (period end or constructed date)
  - geo_key: "USA" unless state-level detail exists
- Measures (examples)
  - Financial indicators (38+ metrics per source), coerced to numeric
- Transformations
  - Dates coerced; if only year provided, Jan-01 used
  - Choice of units (percent 0–1 vs 0–100) documented in notebook if normalized
- Source: original_data/bank_aggregates.xlsx

### fact_credit_sentiment.csv (aka investor_credit_sentiment_cleaned.csv)
- Grain: yearly sentiment
- Keys
  - date_key: YYYY0101 (Jan-01 of year)
- Measures (examples)
  - High yield spread or sentiment index (numeric)
- Transformations
  - Year coerced to int; date_key built from Jan-01 of each year
- Source: original_data/InvestorCreditSentiment.xlsx

### usrec_cleaned.csv
- Grain: recession indicator by date (typically monthly)
- Keys
  - date_key: YYYYMMDD
- Attributes
  - recession (0/1)
- Transformations
  - DATE parsed to datetime; binary flag coerced to int (0/1)
- Source: original_data/USREC.csv

---

### dim_date.csv
- Grain: daily
- Columns
  - date_key (int, YYYYMMDD)
  - date (ISO)
  - year (int), quarter (int 1–4), month (int 1–12)
  - month_name (string), year_month (YYYY-MM)
- Notes: Used as the main calendar; facts join via date_key

### dim_geography.csv
- Grain: geography
- Columns
  - geo_key (ISO3 for countries; "USA-{ST}" for states; "USA" and "GLOBAL" reserved)
  - country_iso3 (ISO3), country_name (string)
  - state_code (USPS 2-letter; nullable)
  - is_usa (0/1)
- Notes: `USA` and `GLOBAL` included for convenience

### dim_bank.csv
- Grain: bank
- Columns
  - bank_key (int surrogate)
  - bank_name_clean (string)
  - city (string)
  - state_code (USPS)
- Notes: Derived from FDIC failures for stable bank dimension

### dim_usrec.csv (optional)
- Grain: date_key
- Columns
  - date_key (int)
  - recession_flag (0/1)
- Notes: Convenient if not merging recession into dim_date directly

---

### Data Quality and Validation Summary
- Duplicates: inspected by natural keys (e.g., geo_key + year) before any removal
- Missing values: coerced to NaN; imputation is avoided unless justified
- Encodings: FDIC CSV loaded using cp1252 with latin-1 fallback
- Date ranges: filtered to 2000–2024
- Exports: UTF-8, commas, header row, index=False, snake_case


