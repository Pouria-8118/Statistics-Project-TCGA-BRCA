# Project Card

## 1. Dataset and subset
- **Source:** TCGA-BRCA from the NCI Genomic Data Commons (GDC), open-access files, downloaded 2026-10-21.
- **Files:** clinical tables (`clinical.tsv`, `follow_up.tsv`, `pathology_detail.tsv`), `slide.tsv`, and 1,111 RNA-Seq files (STAR - Counts, `tpm_unstranded`).
- **Subset:** patients with RNA-Seq and known ER, PR, HER2 status and age (940 of 1,095 patients with RNA).

## 2. Provisional title
Tumour Proliferation and Receptor Status in TCGA-BRCA: Does RNA Expression Add to Clinical Information?

## 3. Main statistical question
Among TCGA-BRCA breast cancer patients, does adding RNA-derived molecular information (a luminal-program score and *ERBB2* expression) to clinical information (ER, PR and HER2 status and age) explain additional variation in tumour proliferation, measured as a 17-gene expression score?

## 4. Unit of observation and population
- **Unit:** the patient (one RNA file per patient, chosen by a fixed rule).
- **Population:** TCGA-BRCA patients with RNA-Seq and complete receptor status and age. Results describe this group, not all breast cancer patients.
- **Study design:** retrospective observational study based on secondary analysis of existing TCGA data; no intervention was assigned.

## 5. Main variables and scale
| Variable | Scale |
|---|---|
| Proliferation score (mean log2(TPM+1) of 17 genes) | continuous |
| Luminal score (mean z-score of *ESR1, PGR, FOXA1, GATA3*) | continuous |
| *ERBB2* expression, log2(TPM+1) | continuous |
| Age at index | continuous |
| ER, PR, HER2 status | Categorical (binary nominal) |

Gene list and score definitions: `data/README.md`.

## 6. Roles of the variables
- **Response:** proliferation score.
- **Explanatory, clinical:** ER, PR, HER2, age.
- **Explanatory, molecular:** luminal score, *ERBB2* expression.

## 7. Type of question
Associational.

## 8. Initial hypothesis
RNA-derived luminal and *ERBB2* expression explain some variation in proliferation beyond the clinical variables, because receptor status is a coarse binary summary of the underlying biology.

## 9. Initial limitations
- **Missing data:** requiring complete receptor status and age removes 155 of 1,095 patients, who may not be a random subset.
- **Measurement:** receptor tests in the clinical files have no date, so they cannot be confirmed as primary-tumour results at diagnosis; HER2 combines FISH and IHC.
- **RNA and score:** bulk tissue mixes tumour and non-tumour cells, and the 17-gene score is project-defined, not a validated clinical signature.