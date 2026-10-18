# Module: databricks-intro

**Learn Databricks by building Halcyon Health's payer-data ingestion, from raw files to governed, tested tables.**

| | |
|---|---|
| **Audience** | Data or analytics engineers who know SQL and a cloud warehouse (BigQuery or Snowflake) and are new to Databricks |
| **Time** | About 15 lessons of 30–60 minutes each |
| **You'll use** | Databricks CLI, SQL Editor, notebooks, Lakeflow pipelines and jobs, dbt-databricks |
| **Data** | Halcyon's three payer feeds (`source_a`, `source_b`, `source_c`), starting as raw files |

## What you'll be able to do

- Navigate Unity Catalog and explain where data and files actually live
- Land files in volumes and explore them with `read_files()`
- Load data with `COPY INTO`, then incrementally with Auto Loader
- Build a Lakeflow pipeline with expectations and quarantine tables, and monitor it
- Model per-source bronze → silver → gold with dbt on Databricks
- Orchestrate, govern, deploy (dev → preprod → prod) and cost-control the whole thing

## Lessons

| # | Lesson | You'll do |
|---|---|---|
| 01 | Workspace, account and CLI auth | Log in with `databricks auth login`, set up profiles, take the workspace tour |
| 02 | Unity Catalog | Metastore → catalog → schema → table/volume; compare to BigQuery and Snowflake |
| 03 | Volumes | Create a sandbox volume, upload a payer drop, compare volumes to Snowflake stages |
| 04 | Exploring raw files | Profile a feed with `read_files()`: delimiter, header, types, quirks |
| 05 | `COPY INTO` | Batch-load a drop, re-run it to see idempotency, load a second drop |
| 06 | Auto Loader | Incremental loading, schema evolution, `_rescued_data` |
| 07 | Lakeflow pipelines | Streaming tables, expectations (warn / drop / fail), quarantine |
| 08 | Observation | The pipeline event log, expectation metrics, freshness and volume checks |
| 09 | Medallion on purpose | Per-source bronze → silver union → gold |
| 10 | dbt on Databricks | Targets, incremental strategies, liquid clustering |
| 11 | Orchestration | Lakeflow Jobs, file-arrival triggers, repair runs |
| 12 | Governance | Grants, row filters, column masks, tags, lineage, audit |
| 13 | Environments and CI/CD | Catalog per environment, Asset Bundles, `main` → preprod, `release` → prod |
| 14 | Cost | Warehouses, serverless, DBUs, `system.billing.usage`, budgets |
| 15 | Consumption | AI/BI dashboards, Genie, SQL Alerts |

Lessons 01–06 run in a sandbox (`workspace.sandbox`) and need nothing pre-built. Later lessons use the
`lab_*` environment as it gets built.

> Status: lesson files are being written. This index is the agreed outline.
