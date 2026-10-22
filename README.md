# MD-readiness assessment of PDB structures: supplementary code

Supplementary code for **BioExcel-3 Deliverable D2.5**, *Final report on standards and recommendations for simulation, data generation, storage and mining*.

## Contents

| Notebook | What it does |
|---|---|
| `01_scoring_assemblies.ipynb` | Extracts issue counts for assemblies, computes penalties and tiers, and ranks assemblies within each PDB entry |
| `02_scoring_non_assemblies.ipynb` | Does the same for non-assemblies, ranked across the whole collection |
| `03_analysis_figures_tables.ipynb` | Produces the figures and summary tables from the outputs of 01 and 02 |

## Workflow

```
MongoDB (PDB_Checking)
   │  (or parquet cache when MongoDB is unavailable)
   ├── 01_scoring_assemblies ──────► data/assembly_md_readiness_ranked.parquet
   │                                 data/assembly_issue_config.json
   └── 02_scoring_non_assemblies ──► data/non_assembly_md_readiness_scored.parquet
                                     data/non_assembly_issue_config.json
                                            │
                    03_analysis_figures_tables ──► figures/*.png + tables
```

Run the notebooks in order: 01, then 02, then 03. Notebooks 01 and 02 are independent of each other. Notebook 03 needs the outputs of both.

## Outputs of notebook 03

| Section | Figure | Values printed below it |
|---|---|---|
| 3. Issue prevalence | `figures/issue_prevalence.png` | % of structures with each issue |
| 4. Tier distribution | `figures/tier_distribution.png` | Counts and % per tier |
| 5. Penalty distribution | `figures/penalty_density_log_ecdf.png`, `figures/penalty_density_raw.png` | Penalty summary statistics |
| 6. Penalty within tiers | `figures/penalty_by_tier.png` | Penalty statistics by tier |
| 7. Penalty contribution by issue | `figures/penalty_contribution.png` | Share of the total penalty per issue |

Other analyses in the report (ligand composition, experimental method, alternate locations, microheterogeneity, AlphaFold models) are not part of this package.

## How to run

```bash
conda env create -f environment.yml
conda activate md-readiness
jupyter lab
```

**Data source.** Notebooks 01 and 02 first try to connect to MongoDB with the credentials in `.env`; copy `.env.example` to `.env` and fill it in.
- If the connection succeeds, the issue counts are extracted from the database and written to the parquet cache in `data/`.
- If it fails, the notebooks use the cached files `data/assembly_issue_counts.parquet` and `data/non_assembly_issue_counts.parquet`.
- Set `USE_CACHE_ONLY = True` in Section 1 to skip the database.

**Consistency.** Severity weights and remediation classes are defined once in notebooks 01 and 02 (Section 2), and each notebook saves them to `data/*_issue_config.json`. Notebook 03 reads those files, checks that the two configurations are identical, and verifies that the per-issue penalty terms add up to each structure's penalty.

## Licence and citation

Code: Apache-2.0 · Data: CC-BY-4.0 · DOI: *to be assigned*
