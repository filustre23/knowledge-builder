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
3. **You do the exercise:** run the CLI command or SQL yourself and paste the result back.
4. **Claude verifies it** against the lesson's check queries and explains what's right or off.
5. **Hints, not answers:** stuck? `/hint` walks a ladder from a nudge to a partial query to the full solution.
6. **Wrap up** with a short quiz. Your progress is logged locally.

## The data environment

Lessons query a Databricks lakehouse (AWS, Unity Catalog, serverless) built by the companion repo
**`databricks-learning`**:

| Layer | What it holds |
|---|---|
| `landing` | Raw payer files, one volume per source |
| `<source>_bronze` | Each payer's data as delivered, one schema per source |
| `silver` | The three payers cleaned, conformed and unioned |
| `gold` | Analytics-ready marts: eligibility, member months, medical and pharmacy claims |
| `observation` | Pipeline health and data-quality results |

Learners read from the shared environment and create their own objects in a personal scratch schema.

## Getting started

**Prerequisites:**
- [Claude Code](https://claude.com/claude-code)
- The [Databricks CLI](https://docs.databricks.com/aws/en/dev-tools/cli/), authenticated to the lab workspace as profile `lab`
- Read access to the `databricks-learning` environment

```bash
git clone https://github.com/filustre23/knowledge-builder.git
cd knowledge-builder
claude
```

Then ask Claude to start the first lesson of a module.

## Ground rules

- **Synthetic data only:** no PHI, no real people, no real company data.
- **You run the commands;** Claude teaches and checks.
- **Mind the cost:** queries run on a shared SQL warehouse, so keep them scoped.
