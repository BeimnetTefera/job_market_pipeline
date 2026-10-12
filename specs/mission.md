# Mission

## Problem
On YouTube, LinkedIn, X and other platforms, much of the career advice
about what to learn is driven by clickbait rather than research. People
end up choosing what to learn without evidence. Instead, they should be
able to see what the job market actually asks for and prepare
accordingly. This project makes researching job requirements easy.

## Who it's for
**Primary users for v1:** students and career switchers preparing to
land their first data role (data engineering, data science, or data
analytics). Experienced job seekers can use it too, but v1 is not
designed around them.

## Questions the data must answer
For data engineering, data science, and data analytics roles:
1. Which data jobs are open to interns, new graduates, or junior
   candidates, and what do they require?
2. Which skills, tools, and platforms are most requested?
3. What does the job actually involve (for example, what share of
   data engineering postings mention building pipelines or data quality)?
4. Which soft skills are asked for?
5. What share of jobs are remote, hybrid, or on-site?
6. Which seniority levels are in demand (intern, junior, mid, senior)?
7. What salary ranges are offered, where stated?
8. Which countries are these jobs in?

## Scope (v1)
**In:**
- Two sources: Greenhouse and Lever, one company from each.
- Collect every open job from those companies, every day.
- Keep a daily history of all jobs from day one. History cannot be
  backfilled, so it is never skipped.
- Analyze only data engineering, data science, and data analytics roles.

**Out (v1):**
- Other sources, and more companies, until v1 is done.
- Analysis of non-data job families.

**Later:**
- Trends over time (which roles are growing), using the history kept in v1.
- Comparing two roles side by side.
- An AI agent users can ask questions.

## Success looks like
**Pipeline:**
- Runs automatically once a day.
- For each company, the number of jobs saved matches the number the
  API returned in that run.
- Running it more than once never creates duplicates.

**Dashboard:**
- Answers the questions above for the three data roles.
- Every number can be traced back to actual job postings.
- When a posting doesn't state something, it shows "not stated"
  instead of guessing.

**v1 is done** after 7 successful runs in a row. A run is successful
when the job counts match the API for both companies, there are no
duplicates, and no manual fix was needed.
