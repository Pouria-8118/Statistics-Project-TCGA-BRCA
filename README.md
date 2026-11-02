# Tumour Proliferation and Receptor Status in TCGA-BRCA

Individual statistics project on TCGA-BRCA (clinical and RNA-Seq data from the NCI Genomic Data Commons, open-access files).

> **Status:** Phase 1 (data understanding and descriptive analysis).

## Statistical question

Among TCGA-BRCA breast cancer patients, does adding RNA-derived molecular information (a luminal-program score and *ERBB2* expression) to clinical information (ER, PR and HER2 status and age) explain additional variation in tumour proliferation, measured as a 17-gene expression score? The full project definition is in [`PROJECT_CARD.md`](PROJECT_CARD.md).

![Data flow from downloaded files to the analytic cohort](figures/04_fig1_data_flow.png)

## Data

| Item | Value |
|---|---|
| RNA-Seq files (STAR - Counts, GENCODE v36) | 1,111 |
| Patients with RNA (one file selected per patient) | 1,095 |
| Patients with clinical data | 1,098 |
| Analytic cohort (RNA, ER, PR, HER2 and age available) | 940 |

The unit of analysis is the patient. Raw data are not included in this repository; sources, files and rebuilding steps are in [`data/README.md`](data/README.md).

![Proliferation score by receptor group](figures/04_fig5_prolif_by_group.png)

## Repository structure

```text
data/
  metadata/       GDC manifest and sample sheet
  raw/            GDC downloads (not tracked)
  processed/      tables produced by the notebooks (not tracked)
figures/          figures produced by the notebooks
notebooks/        analysis notebooks, run in order
reports/          phase reports (PDF)
PROJECT_CARD.md   project definition
requirements.txt  Python dependencies
```

## Notebooks

| Notebook | Content |
|---|---|
| `01_data_inventory_id_mapping` | Data inventory, RNA-to-patient mapping, one RNA file per patient |
| `02_clinical_audit_receptors` | Clinical audit and ER/PR/HER2 status |
| `03_rna_extraction_molecular_audit` | RNA extraction, quality checks, proliferation and luminal scores |
| `04_integration_descriptives_final_question` | Data flow, included vs excluded patients, descriptive analysis |

## Reproducing

```bash
pip install -r requirements.txt
jupyter notebook notebooks/
```

Place the GDC downloads in `data/raw/` as described in [`data/README.md`](data/README.md) and run the notebooks 01 to 04 in order. Notebook 01 queries the GDC API only if its aliquot cache is missing; notebook 03 reads all 1,095 selected RNA files on its first run.

## References

- Grossman RL et al. Toward a shared vision for cancer genomic data. *N Engl J Med*. 2016;375:1109–1112. [DOI](https://doi.org/10.1056/NEJMp1607591)
- TCGA Research Network. Comprehensive molecular portraits of human breast tumours. *Nature*. 2012;490:61–70. [DOI](https://doi.org/10.1038/nature11412)
- GDC: <https://portal.gdc.cancer.gov>