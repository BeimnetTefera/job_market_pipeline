## Data principles

1. **Landing keeps the source data exactly as received.** API responses
   are saved as files in their original format (e.g. JSON), unchanged.
   Landing files are never edited or deleted.
   *Why:* the APIs only show today's jobs; landing is the original
   record of the past and can never be recreated.

2. **Bronze is parsed, not cleaned, and append-only.** Bronze is a Delta
   table built from landing files, one row per record, with metadata
   added. It is only ever written in append mode; no update, delete,
   or overwrite is ever run against it.
   *Why:* silver and gold can always be rebuilt from bronze, but bronze
   can only be rebuilt from landing.

3. **History is never deleted or overwritten.** When a job closes or
   changes, its earlier versions are kept. No person or process decides
   which history to drop.
   *Why:* trends over time (a planned feature) depend on history, and
   history can't be backfilled.

4. **Every row is traceable.** Every bronze row carries: source, company,
   run ID, landing file path, and ingestion time. This metadata is
   carried forward into silver, so every dashboard number can be traced
   to real postings.

5. **Pipelines are idempotent.** Running the pipeline any number of times
   for the same day, including after a failure partway through, produces
   the same rows and the same counts, with no duplicates.

6. **Every table declares its grain and key.** Each table's feature spec
   states what one row means and which columns make it unique. No table
   exists without both.

7. **Unknown is never guessed.** If a source doesn't state a value, it is
   stored as empty and shown as "not stated."

8. **Derived values keep their source.** Any value derived from another
   column (e.g. seniority from a job title) is stored next to the
   column it came from, so it can be checked later.

9. **All timestamps are stored in UTC,** in every layer.
   *Why:* the companies are in different time zones; mixing them
   silently breaks date-based logic like "jobs collected today."

## Quality principles

1. **Data is checked at every layer boundary.** Data must pass the
   checks defined in the feature's validation.md before it moves to
   the next layer.

2. **Every check declares a severity:**
   - **Stop:** the run fails and nothing is promoted. Used for
     whole-run problems, e.g. job counts not matching the API, zero
     jobs returned, or duplicate keys.
   - **Quarantine:** bad rows are set aside; good rows continue. Used
     for row-level problems, e.g. a job with no title.
   - **Warn:** logged, run continues. Used for suspicious but possibly
     real changes, e.g. a large drop in job count since the last run.

3. **Failures explain themselves.** Every failed check records the
   expected value, the actual value, and a readable message (e.g.
   "API returned 42 jobs, bronze has 40").
   *Why:* a failure you can't diagnose quickly is a failure that
   stays unfixed.

4. **Bad rows are never silently dropped.** They go to a quarantine
   table with source, company, run ID, and the reason they failed.
   *Why:* dropping rows breaks traceability.

5. **Bad data is worse than late data.** A failed run is never
   promoted past the gate that failed. Its data stays in landing and
   bronze, tagged by run ID, and the dashboard keeps showing the last
   successful run, labelled "data as of [date]."

6. **Every check result is recorded** in a check log (run ID, gate,
   table, source, company, check name, severity, status, rows checked,
   rows failed, expected, actual, message, timestamp in UTC), plus one
   summary row per run.
   *Why:* "7 successful runs in a row" must be provable, not felt.
