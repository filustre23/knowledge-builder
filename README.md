# knowledge-builder

**Learn a technical topic by doing it on realistic enterprise data, with Claude as your tutor.**

Most tutorials teach on toy datasets. knowledge-builder teaches on the data of a hypothetical company,
**Halcyon Health**, a value-based care organization that receives eligibility and claims feeds from three
different payers. All the data is synthetic, but it's messy in the ways real data is: different layouts, late
files, reversals, bad codes. Every concept you learn is something you see, query and verify in that data.

```
   concept  ──▶  see it in Halcyon's data  ──▶  you run it  ──▶  Claude checks it  ──▶  quiz + progress
```

## Modules

| Module | You'll learn | Lessons | Status |
|---|---|---|---|
| [`databricks-intro`](modules/databricks-intro/) | Databricks end to end: Unity Catalog, volumes, `read_files`, `COPY INTO`, Auto Loader, Lakeflow pipelines, dbt, jobs, governance, CI/CD, cost | 15 | Proposed |
| [`claims-data-analytics`](modules/claims-data-analytics/) | Claims data concepts from Tuva's [*Claims Data Analytics*](https://thetuvaproject.com/knowledge-base/claims-data-analytics-book/index.html) book: eligibility, headers and lines, reversals, lag, member months, attribution, PMPM, ED, readmissions, quality, risk adjustment | 17 | Proposed |

The full lesson lists are in [CLAUDE.md](CLAUDE.md#modules).

## How it works

1. **Open the repo in Claude Code** and start a lesson, e.g. `/start-lesson claims-data-analytics 05`.
2. **Claude explains the concept** briefly and shows where it appears in Halcyon's data.
3. **You do the exercise:** run the SQL (`uv run kb sql`) or CLI command yourself and paste the result back.
4. **Claude verifies it** against the lesson's check queries and explains what's right or off.
5. **Hints, not answers:** stuck? `/hint` walks a ladder from a nudge to a partial query to the full solution.
6. **Wrap up** with a short quiz. Your progress is logged locally.

## The data: it ships with the repo

No cloud account needed. Halcyon's data is committed as small Parquet snapshots, and a local
[DuckDB](https://duckdb.org) database is built from them in seconds:

```
data/<layer>/<table>.parquet  ──▶  uv run kb build-db  ──▶  halcyon.duckdb  ──▶  you + Claude query it
```

| Schema | What it holds |
|---|---|
| `raw` | Sample raw payer files, exactly as delivered (pipe-delimited, CSV.gz, Parquet) |
| `source_a_bronze` · `source_b_bronze` · `source_c_bronze` | Each payer's data as delivered, one schema per source |
| `silver` | The three payers cleaned, conformed and unioned |
| `gold` | Analytics-ready marts: eligibility, member months, medical and pharmacy claims |
| `observation` | Pipeline health and data-quality results |
| `reference` | Public code sets: ICD-10-CM, HCPCS, revenue codes, place of service |
| `learn` | **Your scratch space.** Create anything you like here. |

Every table is documented in [`knowledge/`](knowledge/) (grain, keys, columns, quirks per payer), so Claude can
write and check queries accurately.

**Have a Databricks workspace?** The platform lessons in `databricks-intro` (volumes, Auto Loader, pipelines, jobs,
governance) can also run on [Databricks Free Edition](https://www.databricks.com/learn/free-edition), loading the
same snapshot. The data snapshots are produced by a Databricks lakehouse pipeline built in a companion project.

## Getting started

**Prerequisites:**
- [Claude Code](https://claude.com/claude-code)
- [uv](https://docs.astral.sh/uv/)

```bash
git clone https://github.com/filustre23/knowledge-builder.git
cd knowledge-builder
uv sync                 # installs DuckDB and the kb CLI
uv run kb build-db      # builds halcyon.duckdb from data/
claude                  # then: /start-lesson claims-data-analytics 01
```

> **Status:** the modules and lesson outlines are in place. The data snapshot, the `kb` CLI and the lesson files
> are being built. Until the first snapshot lands, the commands above won't run yet.

## Ground rules

- **Synthetic data only:** no PHI, no real people, no real company data.
- **You run the commands;** Claude teaches and checks.
- **Local first:** DuckDB lessons are free and offline. On Databricks, keep queries scoped, because the warehouse bills while it runs.
