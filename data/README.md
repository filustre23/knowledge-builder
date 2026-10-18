# data/

Committed Parquet snapshots of Halcyon Health's (synthetic) data. `uv run kb build-db` turns them into a local
`halcyon.duckdb`.

| Path | Contents |
|---|---|
| `raw/<source_id>/` | Sample raw files exactly as each payer delivers them |
| `<source_id>_bronze/` | Each payer's tables as ingested, plus `_quarantine` tables |
| `silver/` | Per-source staging and unioned, conformed models |
| `gold/` | Analytics marts: `eligibility`, `member_months`, `medical_claim`, `pharmacy_claim`, … |
| `observation/` | Pipeline health and data-quality results at snapshot time |
| `reference/` | Public code sets (license noted per file in `manifest.yml`) |
| `VERSION` | Snapshot version that lessons and checks are written against |
| `manifest.yml` | Every table: layer, row count, checksum, source, license |

All data is synthetic, produced from public generators: CMS synthetic Medicare claims, Tuva's synthetic data
generator, and Synthea. There are no real people and no PHI.

> No snapshot has been committed yet.
