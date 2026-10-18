# Module: claims-data-analytics

**Understand healthcare claims data, and prove it with queries on three real-world-shaped payer feeds.**

The concepts follow the chapters of Tuva's free book
[*Claims Data Analytics*](https://thetuvaproject.com/knowledge-base/claims-data-analytics-book/index.html).
Each lesson summarizes a chapter, then has you query Halcyon Health's environment to see the concept in action,
including how three payers deliver the "same" data differently.

| | |
|---|---|
| **Audience** | Analysts and engineers new to claims data, or experienced ones who want a structured refresher |
| **Time** | 17 lessons of 30–60 minutes each |
| **You'll use** | SQL on the local DuckDB database (`uv run kb sql`); no cloud account needed |
| **Data** | `source_*_bronze`, `silver`, `gold`, `reference` in `halcyon.duckdb` |

## Lessons

### Part 1: Healthcare data sources

| # | Chapter | In Halcyon's data |
|---|---|---|
| 01 | Introduction to Claims | Trace one claim from a raw file to `gold.medical_claim` |
| 02 | Eligibility and Enrollment | Compare three eligibility layouts; build coverage spans; find gaps |
| 03 | Headers and Lines | Prove claim grain in CMS RIF vs split-by-setting files |
| 04 | Claims Data Elements | Bill type, POS, revenue code, DRG, HCPCS, NPI: completeness by source |
| 05 | Adjustments, Denials and Reversals | Find reversals and voids; implement "latest version wins" |
| 06 | Claims Lag and Runout | Lag triangles, completion factors, why recent months look low |
| 07 | Provider Data | Billing vs rendering NPI, specialty, completeness |
| 08 | Code Sets | ICD-10-CM, CPT/HCPCS, NDC, revenue codes; find invalid codes |

### Part 2: Claims groupers and algorithms

| # | Chapter | In Halcyon's data |
|---|---|---|
| 09 | Member Months | Build member months from spans; reconcile to `gold.member_months` |
| 10 | Member Attribution | Plurality-of-visits attribution; compare two rules |
| 11 | Service Categories | Classify claims (simplified Tuva logic) |
| 12 | Encounters | Roll claims into inpatient stays and ED visits |

### Part 3: Analytics

| # | Chapter | In Halcyon's data |
|---|---|---|
| 13 | Cost Metrics (PMPM) | PMPM by payer and service category |
| 14 | Emergency Department Analytics | ED visits per 1,000; frequent utilizers |
| 15 | Hospital Readmissions | 30-day all-cause readmissions (simplified) |
| 16 | Quality Measures | One HEDIS-style measure end to end |
| 17 | HCCs and Risk Adjustment | Map diagnoses to HCCs; compute and compare RAF |

> Status: lesson files are being written. This index is the agreed outline. Book content is linked and
> summarized, not copied.
