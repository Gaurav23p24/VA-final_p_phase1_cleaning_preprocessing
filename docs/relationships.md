## Power BI Relationships

Goal: Enable plug-and-play modeling with a light star schema. Create these relationships after loading the CSVs from `data/cleaned/`.

### Tables to Import
- Facts
  - global_crisis_cleaned.csv (alias: fact_crisis)
  - fdic_failed_banks_cleaned.csv (alias: fact_fdic_failures)
  - bank_aggregates_cleaned.csv (alias: fact_bank_aggregates)
  - investor_credit_sentiment_cleaned.csv (alias: fact_credit_sentiment)
  - usrec_cleaned.csv (optional standalone reference)
- Dimensions
  - dim_date.csv
  - dim_geography.csv
  - dim_bank.csv (from FDIC)
  - dim_usrec.csv (optional if not merging into dim_date)

### Relationships (set all to Single direction, unless your visuals require Both)
- Date relationships (1:M)
  - dim_date[date_key] → fact_crisis[date_key]
  - dim_date[date_key] → fact_fdic_failures[date_key]
  - dim_date[date_key] → fact_bank_aggregates[date_key]
  - dim_date[date_key] → fact_credit_sentiment[date_key]
  - dim_date[date_key] → dim_usrec[date_key] (if using separate recession dim)
- Geography relationships (1:M)
  - dim_geography[geo_key] → fact_crisis[geo_key]
  - dim_geography[geo_key] → fact_fdic_failures[geo_key]
  - dim_geography[geo_key] → fact_bank_aggregates[geo_key]
- Bank relationship (1:M)
  - dim_bank[bank_key] → fact_fdic_failures[bank_key]

### Cardinality and Keys
- dim_date[date_key] is unique
- dim_geography[geo_key] is unique
- dim_bank[bank_key] is unique
- Facts contain many rows per key

### Recommended Model Settings
- Mark `dim_date[date]` as Date Table (Power BI: Table tools → Mark as date table)
- Hide surrogate keys (`*_key`) from fields list to reduce clutter
- Ensure only one active relationship per date across facts (activate/deactivate as needed)

### Import Steps (quick)
1) Get Data → Text/CSV → import all CSVs from `data/cleaned/`
2) In Model view, create relationships above (Power BI may auto-detect most)
3) Mark `dim_date` as Date Table using column `date`
4) Optionally, hide key columns and non-display technical columns

### Notes and Caveats
- geo_key conventions: ISO3 for countries; "USA-{ST}" for states; reserved "USA" and "GLOBAL"
- fact_crisis is annual; its date_key is Jan-01 of each year (consistent with dim_date)
- If you prefer recession in calendar, merge `dim_usrec` into `dim_date` by date_key before import
- Keep aggregations consistent (e.g., sum counts, average rates) at the visual level


