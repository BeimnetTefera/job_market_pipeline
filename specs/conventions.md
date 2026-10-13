# Conventions

## 1. Catalogs and schemas

One catalog per environment, one schema per layer:

    job_market_dev                 job_market_prod
    ├── landing   (volume: raw)    ├── landing   (volume: raw)
    ├── bronze                     ├── bronze
    ├── silver                     ├── silver
    ├── gold                       ├── gold
    └── ops                        └── ops

- **Environments:** `job_market_dev` and `job_market_prod`. Code never
  names a catalog directly; the catalog comes from config.
- **Landing** is a volume named `raw` inside the `landing` schema,
  with one folder per source (e.g. `raw/greenhouse/`, `raw/lever/`).
- **The source system** appears as a folder in landing, in the table
  name in bronze (e.g. `bronze.greenhouse_jobs`), and as a `source`
  column from silver onward. It never appears in a schema name.
- **Quarantine tables** live in the same schema as the table they
  protect, named `<table>_quarantine`
  (e.g. `silver.job_listings_quarantine`).
- **`ops`** holds tables about the pipeline itself, not job data:
  the check log and the run summary.
