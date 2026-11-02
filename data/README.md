# Data

Raw data are not stored in this repository. This file documents the source, the files used and how to rebuild `data/raw/`.

## Source
- **Project:** TCGA-BRCA, NCI Genomic Data Commons (GDC), <https://portal.gdc.cancer.gov>
- **Access:** open-access files only
- **Downloaded:** 2026-10-21 (GDC Data Release 46.0 was active on that date)

## Files used
| Folder in `data/raw/` | Content |
|---|---|
| `RNA/<file_id>/` | 1,111 RNA-Seq files, *STAR - Counts*, GENCODE v36 |
| `clinical.project-tcga-brca.2026-10-21/` | `clinical.tsv`, `follow_up.tsv`, `pathology_detail.tsv` (plus `exposure.tsv`, `family_history.tsv`, almost empty) |
| `biospecimen.project-tcga-brca.2026-10-21/` | `sample.tsv`, `slide.tsv`, `portion.tsv`, `analyte.tsv`, `aliquot.tsv` |

`data/metadata/` identifies exactly which files were used:
- `gdc_manifest.2026-10-21.191818.txt`: file IDs, names, MD5 checksums and sizes of the 1,111 RNA files
- `gdc_sample_sheet.2026-10-21.tsv`: links each RNA file ID to its patient and sample

## Rebuilding `data/raw/`
1. Download the RNA files with the GDC Data Transfer Tool:
   `gdc-client download -m data/metadata/gdc_manifest.2026-10-21.191818.txt -d data/raw/RNA`
2. Download the TCGA-BRCA clinical and biospecimen tables from the GDC portal and unzip them into the two folders above.
3. The sample sheet is read from `data/metadata/`.

## Processed tables (`data/processed/`)
| File | Notebook | Content |
|---|---|---|
| `patient_rna_selection.csv`, `rna_selection_audit.csv`, `flow_rna_selection.csv` | 01 | selected RNA file per patient; all candidate files; counts |
| `gdc_aliquot_lookup.csv` | 01 | aliquot IDs from the GDC API (cache) |
| `clinical_patient.csv`, `tumor_nuclei_by_sample.csv` | 02 | one clinical row per patient; tumour-nuclei % per sample |
| `rna_qc.csv`, `rna_panel_tpm.csv`, `molecular_patient.csv` | 03 | file quality; gene TPM values; log2 values and scores |
| `analysis_table.csv`, `flow_cohort.csv` | 04 | final patient-level table (`in_cohort` flag); cohort counts |

## Processing rules
- **One RNA file per patient:** smallest sample ID, then smallest aliquot ID, then file ID. The 16 unused files stay listed in `rna_selection_audit.csv`.
- **Missing values:** the GDC marker `'--` is read as missing.
- **Tumour variables** come from the single diagnosis flagged as primary disease.
- **Receptor status:** ER and PR from IHC. HER2 from definite FISH, otherwise definite IHC. Equivocal results and conflicting repeated tests are missing; a conflicting FISH result is not replaced by IHC.
- **Expression:** `tpm_unstranded`, analysed as `log2(TPM + 1)`.
- **Tumour nuclei %** (`slide.tsv`) is a pathologist estimate, not tumour purity.

## Expression scores
- **Proliferation score:** mean `log2(TPM + 1)` of 17 genes: `CCNB1, CCNE1, CDC20, CEP55, EXO1, KIF2C, MELK, MYBL2, NUF2, PTTG1, RRM2, TYMS, UBE2C, UBE2T, BIRC5, ANLN, MKI67`.
- **Luminal score:** mean of the z-scores of `ESR1, PGR, FOXA1, GATA3`, each standardised over all 1,095 RNA patients.
- **`ERBB2`:** `log2(TPM + 1)`.

The proliferation panel follows published breast-cancer proliferation gene sets (Parker et al. 2009; Buus et al. 2021). It shares 14 genes with the 18-gene Prosigna proliferation component, adds `CCNB1`, `MYBL2` and `BIRC5`, and omits `CDC6`, `CENPF`, `KNTC2` and `ORC6L`. It is a project-defined score, not the validated Prosigna score or a clinical signature. `MKI67` here is mRNA, not the Ki-67 protein index.

References:
- Parker JS et al. Supervised risk predictor of breast cancer based on intrinsic subtypes. *J Clin Oncol*. 2009;27:1160–1167. [DOI](https://doi.org/10.1200/JCO.2008.18.1370)
- Buus R et al. Development and validation for research assessment of Oncotype DX Breast Recurrence Score, EndoPredict and Prosigna. *npj Breast Cancer*. 2021;7:15. [DOI](https://doi.org/10.1038/s41523-021-00216-w)