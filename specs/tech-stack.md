## 1. Platform and languages

**Platform: Databricks Free Edition.** Its limits shape the design:
- Serverless compute only: all jobs and notebooks run on serverless.
- Daily usage quota: exceeding it shuts compute down until it resets.
  A quota shutdown is treated as a failed run (see Quality principles).
  Runs are kept light.
- Up to 5 concurrent job tasks per account.
- Non-commercial use only.
- No SLA, and inactive accounts may be deleted. Landing is
  irreplaceable, so a backup outside Databricks will be decided later.

**Languages:**
- **Plain Python** fetches from the APIs and writes landing files.
  *Why:* fetching is a few sequential HTTP calls with polite delays,
  which Spark doesn't speed up; and plain Python writes the response
  exactly as received, as the landing rule requires.
- **PySpark** builds bronze and silver: reading landing files, parsing
  JSON, unifying sources, deduplication, and history.
- **SQL** builds gold: data modeling and business aggregations.
  *Why:* gold holds the business logic, and SQL keeps it readable.
- **Python version:** whatever the serverless environment used by the
  jobs provides, so local tests behave like Databricks.
