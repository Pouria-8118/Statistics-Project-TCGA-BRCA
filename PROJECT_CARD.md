# Project Card

## 1. Dataset and subset
- **Source:** TCGA-BRCA from the NCI Genomic Data Commons (GDC), open-access files, downloaded 2026-10-21. GDC Data Release 46.0 was active on the download date.
- **Files:** clinical tables (`clinical.tsv`, `follow_up.tsv`, `pathology_detail.tsv`), `slide.tsv`, and 1,111 RNA-Seq files (STAR - Counts, `tpm_unstranded`).
- **Subset:** patients with RNA-Seq and known ER, PR, HER2 status and age (940 of 1,095 patients with RNA; subject to the final rerun after the HER2 conflict rule is applied).

## 2. Provisional title
Tumour Proliferation and Receptor Status in TCGA-BRCA: Does RNA Expression Add to Clinical ER/PR/HER2 Status?

## 3. Main statistical question
In TCGA-BRCA patients, how does tumour proliferation (measured as a gene-expression score from RNA-Seq) relate to ER, PR and HER2 status, and does RNA expression of luminal genes and *ERBB2* add information about proliferation beyond the clinical receptor status and age?

## 4. Unit of observation and population
- **Unit:** the patient (one RNA file per patient, chosen by a fixed rule: smallest sample submitter ID, then smallest aliquot submitter ID, then smallest GDC file ID).
- **Population:** TCGA-BRCA patients with RNA-Seq and complete receptor status and age. Results describe this group, not all breast cancer patients.

## 5. Main variables and scale
| Variable | Scale |
|---|---|
| Proliferation-related score (mean log2(TPM+1) of 17 genes); *MKI67* | continuous |
| Luminal score (mean z-score of *ESR1, PGR, FOXA1, GATA3*); *ERBB2* | continuous |
| Age at index | continuous |
| ER, PR, HER2 status | binary |

**Proliferation gene panel and score definition.** The project-defined 17-gene panel is `CCNB1, CCNE1, CDC20, CEP55, EXO1, KIF2C, MELK, MYBL2, NUF2, PTTG1, RRM2, TYMS, UBE2C, UBE2T, BIRC5, ANLN, MKI67`. The score is the arithmetic mean of the 17 gene-wise values `log2(TPM + 1)`. The panel shares 14 genes with the 18-gene proliferation component used in the PAM50/Prosigna Risk of Recurrence (ROR) literature (counting current symbol `NUF2` with its former symbol `CDCA1`); it additionally includes `CCNB1`, `MYBL2` and `BIRC5`, and omits `CDC6`, `CENPF`, `KNTC2` and `ORC6L`. It is therefore a **project-defined proliferation-related expression score**, not the validated 18-gene Prosigna score and not a clinical signature. See Buus et al. (2021) and Parker et al. (2009), with the 11-gene PAM50 proliferation index discussed by the meta-analysis cited in the project README.

The luminal score is the mean of gene-wise z-scores for *ESR1, PGR, FOXA1* and *GATA3*, with each gene standardised across all 1,095 RNA patients. `ERBB2` is measured as `log2(TPM + 1)`. `MKI67` alone is the secondary response and is also measured as `log2(TPM + 1)`; it is mRNA expression, not the Ki-67 protein index.

## 6. Roles of the variables
- **Response:** proliferation-related expression score (secondary: *MKI67* expression).
- **Explanatory, clinical:** ER, PR, HER2, age.
- **Explanatory, molecular:** luminal score, *ERBB2* expression.

## 7. Type of question
Comparative (proliferation across receptor groups) and associational (added value of RNA).

## 8. Initial hypothesis
ER-negative tumours have a higher proliferation-related expression score than ER-positive tumours. Whether RNA adds information beyond clinical receptor status is left open.

## 9. Initial limitations
- **Missing data:** requiring complete receptor status and age removes 155 of 1,095 patients, who are not a random subset.
- **Measurement:** receptor tests in the clinical files have no usable date or timepoint, so correspondence with the selected primary-tumour sample and diagnosis time cannot be verified; HER2 combines definitive FISH/ISH results when available and otherwise definitive IHC results.
- **RNA:** bulk tissue mixes tumour and non-tumour cells; tumour-nuclei percentage is a pathologist estimate, not a direct tumour-purity measurement.
- **Score validity:** the 17-gene proliferation-related score is project-defined and exploratory, not a validated clinical signature or the Prosigna score. The RNA measure of *MKI67* is not the Ki-67 protein index.
- **Cohort:** 11 male patients are included; sensitivity to their exclusion is planned.

Details of data and processing: `data/README.md`.