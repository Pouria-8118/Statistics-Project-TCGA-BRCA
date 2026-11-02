# Data

Raw data are **not** stored in this repository. This file documents where they come from and how to rebuild the folder.

## Source
- **Project:** TCGA-BRCA, NCI Genomic Data Commons (GDC), <https://portal.gdc.cancer.gov>
- **Access:** open-access files only
- **Downloaded:** 2026-10-21
- **GDC data release:** 46.0 (released 2026-08-10; active on the download date). See the [GDC Data Release Notes](https://docs.gdc.cancer.gov/Data/Release_Notes/Data_Release_Notes/).

## Files used
| Folder in `data/raw/` | Content |
|---|---|
| `RNA/<file_id>/` | 1,111 RNA-Seq files, *STAR - Counts*, GENCODE v36 (`*.rna_seq.augmented_star_gene_counts.tsv`) |
| `clinical.project-tcga-brca.2026-10-21/` | `clinical.tsv`, `follow_up.tsv`, `pathology_detail.tsv` (plus `exposure.tsv`, `family_history.tsv`, almost empty) |
| `biospecimen.project-tcga-brca.2026-10-21/` | `sample.tsv`, `slide.tsv`, `portion.tsv`, `analyte.tsv`, `aliquot.tsv` |

## Metadata kept in the repository
`data/metadata/` holds the small text files that identify exactly which files were used:
- `gdc_manifest.2026-10-21.191818.txt` (file IDs, names, MD5 checksums and sizes of the 1,111 RNA files)
- `gdc_sample_sheet.2026-10-21.tsv` (links each RNA file ID to its patient and sample)

These metadata files are kept in the repository; the raw downloads and processed tables are excluded through `.gitignore`.

## Rebuilding `data/raw/`
1. Download the RNA files with the GDC Data Transfer Tool using the manifest:
   `gdc-client download -m data/metadata/gdc_manifest.2026-10-21.191818.txt -d data/raw/RNA`
2. Download the clinical and biospecimen tables for TCGA-BRCA from the GDC portal (Repository, or the cohort's clinical/biospecimen download) and unzip them into the two folders above.
3. Keep the GDC sample sheet at `data/metadata/gdc_sample_sheet.2026-10-21.tsv`. Notebook 01 reads it from this location to map GDC file IDs to patient and sample IDs.

## Processed tables (`data/processed/`)
Created by the notebooks, in this order.

| File | Created by | Content |
|---|---|---|
| `patient_rna_selection.csv`, `rna_selection_audit.csv`, `flow_rna_selection.csv` | 01 | one selected RNA file per patient; all candidates; counts |
| `gdc_aliquot_lookup.csv` | 01 | aliquot IDs from the GDC API for samples with several files (cache) |
| `clinical_patient.csv`, `tumor_nuclei_by_sample.csv` | 02 | one clinical row per patient; tumour-nuclei % per sample |
| `rna_qc.csv`, `rna_panel_tpm.csv`, `molecular_patient.csv` | 03 | file quality; TPM of the gene panel; log2 values and scores |
| `analysis_table.csv`, `flow_cohort.csv` | 04 | final patient-level table (`in_cohort` flag); cohort counts |

## Processing rules
- **One RNA file per patient:** smallest sample ID, then smallest aliquot ID, then file ID. The 16 unused files stay in `rna_selection_audit.csv`; they are not deleted from the raw data.
- **Missing values:** the GDC marker `'--` is read as missing.
- **Tumour variables:** come from the single diagnosis flagged as primary disease.
- **Receptor status:** ER and PR from IHC; HER2 from definitive FISH/ISH if available, otherwise definitive IHC; equivocal or conflicting results are missing. Repeated conflicting results are not silently resolved by falling back to another test method.
- **Expression:** `tpm_unstranded`, analysed as `log2(TPM + 1)`.
- **Tumour nuclei %:** `slide.tsv` contains a pathologist estimate; it is not a direct tumour-purity measurement.

### Proliferation-related 17-gene panel: rationale and limits

The project-defined panel is:

`CCNB1, CCNE1, CDC20, CEP55, EXO1, KIF2C, MELK, MYBL2, NUF2, PTTG1, RRM2, TYMS, UBE2C, UBE2T, BIRC5, ANLN, MKI67`.

It was assembled with reference to published breast-cancer proliferation-related gene sets. Fourteen genes overlap the 18-gene proliferation component used in the PAM50/Prosigna Risk of Recurrence (ROR) literature: `ANLN, CCNE1, CDC20, CDCA1/NUF2, CEP55, EXO1, KIF2C, MELK, MKI67, PTTG1, RRM2, TYMS, UBE2C, UBE2T`. The three additional genes are `CCNB1`, `MYBL2` and `BIRC5`; these belong to the broader PAM50 intrinsic gene set, and `CCNB1` and `BIRC5` also appear in a published 11-gene PAM50 proliferation index. The four genes from the 18-gene ROR proliferation component not included here are `CDC6`, `CENPF`, `KNTC2` and `ORC6L`.

The score used in this project is the arithmetic mean of `log2(TPM + 1)` across the 17 listed genes. It is **project-defined**, is not identical to the validated 18-gene Prosigna ROR proliferation score, and must not be interpreted as a validated clinical signature. The secondary `MKI67` measure is mRNA expression, not the Ki-67 protein index.

References:
- Parker JS et al. Supervised risk predictor of breast cancer based on intrinsic subtypes. *J Clin Oncol*. 2009;27:1160–1167. [DOI: 10.1200/JCO.2008.18.1370](https://doi.org/10.1200/JCO.2008.18.1370).
- Buus R et al. Development and validation for research assessment of Oncotype DX® Breast Recurrence Score, EndoPredict® and Prosigna®. *npj Breast Cancer*. 2021;7:15. [DOI: 10.1038/s41523-021-00216-w](https://doi.org/10.1038/s41523-021-00216-w).
- The Role of Proliferation in Determining Response to Neoadjuvant Chemotherapy in Breast Cancer: A Gene Expression-Based Meta-Analysis. *Clin Cancer Res*. 2016;22(24):6039–6049. [PubMed Central](https://pmc.ncbi.nlm.nih.gov/articles/PMC5161615/).