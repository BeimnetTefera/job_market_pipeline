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

## 2. Table names

- **Lowercase snake_case, always.**
- **Singular nouns** from silver onward (`job_listing`, `company`, `job_skill`).
- **Bronze:** `<source>_<entity>`, using each source's own word,
  in the singular (`greenhouse_job`, `lever_posting`).
- **Silver:** the entity only, with no source. History and current
  state use suffixes:
  - `<entity>_history`: every version, one row per entity per day
  - `<entity>_current`: latest version of what's open now (may be a view)
- **Gold:**
  - `fct_<entity>` for fact tables (e.g. `fct_job_posting`)
  - `dim_<entity>` for dimension tables (e.g. `dim_company`)
  - `<what>_by_<dimension>` for chart-specific aggregates
    (e.g. `skill_demand_by_role`)
- **Quarantine:** `<table>_quarantine`, in the same schema (see section 1).
- **No versions or dates in table names** (`_v2`, `_2026`, `_backup`).
  History lives in rows, not in table copies.
