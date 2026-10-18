# CLAUDE.md

Interactive concept learning on realistic enterprise data. Each **module** teaches one topic, such as Databricks or
claims analytics, through short lessons. The learner **does the work themselves** (CLI commands, SQL, code)
against a live data environment for a hypothetical company, while Claude teaches, hints and verifies.

> Status: **proposal.** The modules and lesson lists below are the agreed shape. Lesson files get written and refined
> module by module.

## The hypothetical company: Halcyon Health (fictional)

- **What it is:** a value-based care company that takes financial risk for members of several health plans and
  coordinates their care.
- **Its data problem:** each payer sends eligibility and claims in its own layout, cadence and format.
- **Sources** (all synthetic, from public generators):

| `source_id` | Payer persona | Feed format | Cadence |
|---|---|---|---|
| `source_a` | Medicare fee-for-service | CMS RIF, pipe-delimited | Monthly |
| `source_b` | Commercial (Aetna-style layout) | CSV.gz, fixed-width eligibility | Weekly |
| `source_c` | Regional plan (HCCI-style layout) | Parquet, claims split by setting | Daily |

- **No real people, no PHI, no real company data.** Everything is synthetic. Keep it that way in every lesson.
- If a lesson needs company context, such as contracts, a care team or a KPI target, invent it consistently and
  record it in `company/` so later lessons reuse the same facts.

## The data environment

Lessons query the Databricks lakehouse built by the companion repo **`databricks-learning`**: AWS us-west-2,
Unity Catalog, serverless. This repo never builds infrastructure. It only reads the environment, plus a personal
scratch schema.

| Layer | Where | Used by |
|---|---|---|
| Raw files | `lab_prod.landing` (volumes, one per source) | databricks-intro (ingestion lessons) |
| Bronze | `lab_prod.<source_id>_bronze.*` (one schema per source, plus `_quarantine` tables) | databricks-intro, claims (source-variation lessons) |
| Silver | `lab_prod.silver` (`stg_*` per source, `int_*` unioned and conformed) | claims analytics |
| Gold | `lab_prod.gold` (`eligibility`, `member_months`, `medical_claim`, `pharmacy_claim`, …) | claims analytics |
| Observation | `lab_prod.observation` (pipeline health, data-quality results) | databricks-intro |
| Scratch | `lab_dev.learn_<user>` | Learner exercises that create objects |

- **Read-only outside scratch.** Learners `SELECT` from `lab_prod`. Anything they `CREATE` goes in `lab_dev.learn_<user>`.
- **Before the environment exists,** early databricks-intro lessons run in a sandbox: the `workspace` catalog,
  `workspace.sandbox` schema, with files uploaded to a managed volume.
- **Each lesson declares the tables it needs** (`requires_tables`). If they don't exist yet, say which
  `databricks-learning` phase builds them and offer the sandbox alternative.
- **Cost:** every SQL query wakes the warehouse, which bills DBUs. Keep exercise queries scoped (filters, `LIMIT`)
  and follow the environment's budget rules (`databricks-learning` → `/cost-check`).

## How Claude teaches here

1. **Concept first, briefly.** Give the idea in plain language, plus one analogy to GCP or Snowflake when it helps
   (see `databricks-learning/docs/platform-equivalents.md`).
2. **Show it in Halcyon's data.** Every concept is grounded in a real table, file or row from the environment, never
   a toy example when the real data can show it.
3. **The learner does it.** Give the exercise and the command or query skeleton. The learner runs it and pastes the output.
   Don't run the exercise for them.
4. **Verify.** Claude checks the result: by reading the pasted output, by running a read-only check query from the
   lesson's `checks/` file, or both. Say what's right, what's off, and why.
5. **Hint ladder, not answers.** If the learner is stuck: nudge → point to the concept → partial query → full
   solution only on request.
6. **Close the loop.** End each lesson with 2–3 check-your-understanding questions and a one-line summary.
   Log it in the learner's progress file.

Learner-run vs Claude-run:
- **Claude may run** read-only queries and commands: `SELECT`, `DESCRIBE`, `databricks … list|get`.
- **The learner runs** anything that creates, changes or bills meaningfully: `CREATE`, `COPY INTO`, pipeline runs,
  deploys, `fs cp`.

## Repo layout (target)

```
knowledge-builder/
├── README.md
├── CLAUDE.md
├── company/                        # Halcyon Health facts reused across lessons (payers, contracts, KPIs)
├── modules/
│   ├── databricks-intro/
│   │   ├── README.md               # module overview, prerequisites, lesson index
│   │   ├── lessons/NN-<slug>.md    # one lesson per file
│   │   └── checks/NN-<slug>.sql    # Claude's verification queries (not for the learner to peek)
│   └── claims-data-analytics/
│       ├── README.md
│       ├── lessons/
│       └── checks/
├── progress/                       # per-learner progress logs (gitignored)
└── .claude/skills/                 # tutor skills (proposed below)
```

### Lesson file format

```markdown
---
id: claims-05
module: claims-data-analytics
title: Adjustments, denials and reversals
duration: 45m
prerequisites: [claims-03, claims-04]
requires_tables: [lab_prod.source_b_bronze.medical, lab_prod.silver.int_claim_adjustments]
warehouse: true            # does this lesson run SQL on the warehouse?
sources:
  - https://thetuvaproject.com/claims-data-analytics/data-sources/claims-data/adjustments-denials-reversals
---
## Why it matters
## The concept
## In Halcyon's data
## Exercises        (numbered; each says what to run and what to paste back)
## Check your understanding
## Going further
```

- **Source material:** link to it and explain in your own words. Don't copy external book or doc text verbatim.
- **Exercises** must be answerable from the environment and checkable by a query in `checks/`.

## Modules

### Module 1: `databricks-intro`

Learn Databricks by building Halcyon's ingestion path. It follows the same sequence as the `databricks-learning` build.

| # | Lesson | You'll do |
|---|---|---|
| 01 | Workspace, account and CLI auth | Log in with `databricks auth login`, set up profiles, take the workspace tour |
| 02 | Unity Catalog: metastore → catalog → schema → table/volume | List and describe objects; compare to BigQuery projects and Snowflake databases |
| 03 | Volumes: landing files | Create a sandbox volume, `fs cp` a payer drop, compare volumes to Snowflake stages |
| 04 | Exploring raw files with `read_files()` | Profile delimiter, header, types and quirks for one source |
| 05 | Batch loading with `COPY INTO` | Load a drop, re-run it to see idempotency, load a second drop |
| 06 | Auto Loader | Incremental loading, schema inference and evolution, `_rescued_data` |
| 07 | Lakeflow pipelines | Streaming tables, expectations (warn, drop, fail), quarantine tables |
| 08 | Observation | The pipeline event log; expectation metrics; freshness and volume checks |
| 09 | Medallion on purpose | Per-source bronze → silver (union) → gold; why sources meet in silver |
| 10 | dbt on Databricks | dbt-databricks targets, incremental `merge` vs `replace_where`, liquid clustering |
| 11 | Orchestration | Lakeflow Jobs, file-arrival triggers, task dependencies, repair runs |
| 12 | Governance | Grants, row filters, column masks, tags, lineage, `system.access.audit` |
| 13 | Environments and CI/CD | Catalog per environment, Asset Bundles, dev → preprod (`main`) → prod (`release`) |
| 14 | Cost | Warehouses vs serverless, DBUs, `system.billing.usage`, budgets |
| 15 | Consumption | AI/BI dashboards, Genie spaces, SQL Alerts |

### Module 2: `claims-data-analytics`

Concepts follow the chapters of Tuva's free book
[*Claims Data Analytics*](https://thetuvaproject.com/knowledge-base/claims-data-analytics-book/index.html).
Every exercise queries Halcyon's environment, so learners see how three payers differ and how a common model
resolves them.

| # | Book chapter | Lesson focus in Halcyon's data |
|---|---|---|
| 01 | Introduction to Claims | Trace one claim from a raw `source_b` file through bronze → silver → `gold.medical_claim` |
| 02 | Eligibility and Enrollment | Compare three eligibility layouts; build coverage spans; find gaps and overlaps |
| 03 | Headers and Lines | Header/line grain in CMS RIF (`source_a`) vs HCCI's split-by-setting files (`source_c`); prove grain with key tests |
| 04 | Claims Data Elements | Bill type, place of service, revenue code, DRG, HCPCS, NPI: completeness per source |
| 05 | Adjustments, Denials and Reversals | Find the seeded reversals and voids; implement "latest version wins"; reconcile to the defect manifest |
| 06 | Claims Lag and Runout | Paid vs service dates, lag triangles, completion factors, why recent months look low |
| 07 | Provider Data | NPIs on claims, billing vs rendering provider, specialty, provider completeness by source |
| 08 | Code Sets | ICD-10-CM, CPT/HCPCS, NDC, revenue codes; validate codes against seeds; find invalid codes |
| 09 | Member Months | Build member months from spans; reconcile to `gold.member_months`; the edge cases |
| 10 | Member Attribution | Plurality-of-visits attribution; compare two rules and see who changes |
| 11 | Service Categories | Classify claims into inpatient, outpatient, professional, ED and pharmacy (simplified Tuva logic) |
| 12 | Encounters | Roll claims into inpatient stays and ED visits; overlapping and interim claims |
| 13 | Cost Metrics (PMPM) | PMPM by payer and service category; why the denominator matters |
| 14 | Emergency Department Analytics | ED identification (revenue codes, POS 23); visits per 1,000; frequent utilizers |
| 15 | Hospital Readmissions | 30-day all-cause readmissions (simplified CMS logic); index stays and exclusions |
| 16 | Quality Measures | One HEDIS-style measure end to end: eligible population, exclusions, numerator |
| 17 | HCCs and Risk Adjustment | Map diagnoses to HCCs (simplified), compute a RAF, compare risk across payers |

### Future modules (ideas)

- `dbt-fundamentals`: models, tests, contracts, incremental strategies, packages
- `data-quality-engineering`: expectations, observability, break-it drills, incident triage
- `healthcare-code-sets`: ICD-10, CPT/HCPCS, NDC, LOINC, SNOMED in depth
- `value-based-care-economics`: risk contracts, benchmarks, shared savings, attribution in contracts
- `terraform-for-data-platforms`: Unity Catalog and AWS with the Databricks and AWS providers

## Proposed tutor skills (`.claude/skills/`, built later)

| Skill | Purpose |
|---|---|
| `/start-lesson <module> <n>` | Load the lesson, check prerequisites and `requires_tables`, give the concept and exercise 1 |
| `/check` | Verify the current exercise from the pasted output and/or the lesson's `checks/` query |
| `/hint` | Next rung on the hint ladder for the current exercise |
| `/quiz` | Check-your-understanding questions for the current lesson |
| `/progress` | Show completed lessons and what's next; update `progress/<user>.md` |
| `/new-lesson <module>` | Scaffold a lesson file in the standard format (for authors) |

## Repo rules

- Synthetic data only. No PHI, no real company names or data, no real people.
- No secrets, account IDs, workspace URLs or machine-specific paths in tracked files. Each learner uses their own
  Databricks CLI profile (default name `lab`).
- `progress/` is personal and gitignored.
- README and CLAUDE.md describe the repo as it is, in present tense.
